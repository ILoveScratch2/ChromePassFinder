# ChromePassFinder
A simple python tool for get Google Chrome web browser's stored passwords without permission or computer password. (only compatible on windows)


---

## Principle:

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


---
## Usage:

```
main.py [-h] (help)
```
Example:
```
main.py -h
```
Output:
```
usage: main.py [-h] search_url

Get passwords stored in Chrome.

positional arguments:
  search_url  Search url in password database

options:
  -h, --help  show this help message and exit
```


```
main.py   (no arguments)
```
#Example:
```
main.py
```
#Output \( passwords are all fake in this example\) : 
```
Decrypt Login Data:
('https://twitter.com/login/error', 'hello@gmail.com', 'hellohello')
('https://www.zhihu.com/', 'helloworld@hotmail.com', '1234567')
('https://signup.live.com/signup', 'myemail@outlook.com', '123 321 1234567')
('https://signup.live.com/signup', 'test2@outlook.com', 'whatTheHeaven')
('https://signup.live.com/signup', 'test@outlook.com', 'fairyBluePotato')
('https://github.com/login', 'ILoveScratch2', 'IWontTellYouPassword')
('https://login.live.com/login.srf', 'django202333@outlook.com', 'MyPasswprd')
('https://vjudge.net/', 'Hello', '1234567')
('https://www.class.com/', 'helloworld', 'password11111')
('https://example.com/', 'Test', "The password to test chrome's passwprd storage")
---snip---
```


```
main.py [website url]    (search record for a website)
```
Example:
```
main.py https://vjudge.net/
```
Output:
```
Decrypt Login Data:
('https://vjudge.net/', 'Hello', '1234567')
```
