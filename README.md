# ChromePassFinder
A simple python tool for get Google Chrome web browser's storaged passwords without permission or computer password.


---


Principle:
[Chromium](https://source.chromium.org/chromium/chromium/src/+/main:components/os_crypt/sync/os_crypt_win.cc;l=1?q=os_crypt_win&sq=&ss=chromium ) has explicitly written out its own decryption process in the open source code as follows
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

---


