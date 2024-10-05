# ChromePassFinder
A simple python tool for get Google Chrome web browser's stored passwords without permission or computer password. (only compatible on windows)


---

Principle:

[Chromium](https://source.chromium.org/chromium/chromium/src/+/main:components/os_crypt/sync/os_crypt_win.cc;l=1?q=os_crypt_win&sq=&ss=chromium ) has explicitly written out its own decryption process in the open source code as follows

Place chrome stored it's password database:
```
C:\Users\[USERNAME]\AppData\Local\Google\Chrome\User Data\Default\Login Data
```
Place chrome stored it's encryption key for password database:
```
C:\Users\[USERNAME]\AppData\Local\Google\Chrome\User Data\Default\Local State
```

Chromium code\(chromium/src/main/components/os_crypt/os_crypt_win.cc\ Line 183):
```c
bool OSCryptImpl::DecryptString16(const std::string& ciphertext,
                              std::u16string* plaintext) {
  std::string utf8;
  if (!DecryptString(ciphertext, &utf8))
    return false;

  *plaintext = base::UTF8ToUTF16(utf8);
  return true;
}
```



