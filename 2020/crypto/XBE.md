### 0x00 Challenge: XBE
**Description:**
>Sometimes it is easy to guess the encryption key.

### 0x01 Write-up:
The flag.txt file is encrypted using a simetric encryption (XOR) that means if you know part of the plaintext, you can get part of the encryption key. If we use "DEKRA{" as key (because all the flags starts with that chain), we can get the start of the real encryption key "SuperS".

![](https://i.imgur.com/OtFbQPu.png)

Using a little of guessing or brute-forcing, we can get the complete encryption key "SuperSecretKey" that brings us the string "DEKRA{X0RXoRX0R}"

![](https://i.imgur.com/NXpdp1E.png)

