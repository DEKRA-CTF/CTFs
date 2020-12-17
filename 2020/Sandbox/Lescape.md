### 0x00 Challenge: Lescape
**Description:**
>Can you escape from this Linux jail and get the flag?
>
>nc 168.119.247.237 5016

### 0x01 Write-up:
This challenge was a simple Linux jail that you need to escape. There are many ways to solve it!

**Way to solve:**
```
read aaa; exec $aaa
/bin/bash # after this you will get a simple shell
```
