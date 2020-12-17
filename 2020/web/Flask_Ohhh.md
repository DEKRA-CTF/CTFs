### 0x00 Challenge: Flask Ohhh
**Description:**
>Do you notice anything strange on this website?
>
>URL: http://168.119.247.237:5002/

### 0x01 Write-up:
This challenge was an SSTI. If you visit robots.txt you can check a hidden "/.code" file that leaks source code of the web app.

![](https://i.imgur.com/5vYLeHC.png)

Having knowlegde about the blacklist strings, you need to get the flag.

The final payload to get it was: 
```
/secret/{{url_for.__globals__.current_app.config}}
```

![](https://i.imgur.com/PXxjFOV.png)
