### 0x00 Challenge: HiddenCode
**Description:**
>Our developers have created an online service that downloads the content of the websites you request. They said it has no security flaws, what do you think?
>
>URL: http://168.119.247.237:5004/

### 0x01 Write-up:
When entering the URL you can check how an html code is displayed. If you try to use the search bar, 2 parameters are leaked: "target" and "expression". The target refers to the URL where the HTML code is collected. The expression refers to the string used to select the displayed lines.

Also needs to highlight a "hidden" line with the path of the flag (/var/www/flag.txt).

We can use the function to leak the flag and the expression to specify to search "DEKRA" string (used in all flags).

URL used: 
```
http://168.119.247.237:5004/index.php?target=/var/www/flag.txt&expression=/DEKRA/
```

![](https://i.imgur.com/BobXYzR.png)
