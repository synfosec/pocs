# classic passwd

#### Information Given

I forgot my password, can you give me access to the program?

## Step 1

Dump all funcs

```
0x0000000000001000  _init
0x0000000000001030  strcpy@plt
0x0000000000001040  puts@plt
0x0000000000001050  printf@plt
0x0000000000001060  strcmp@plt
0x0000000000001070  __isoc99_scanf@plt
0x0000000000001080  exit@plt
0x0000000000001090  __cxa_finalize@plt
0x00000000000010a0  _start
0x00000000000010d0  deregister_tm_clones
0x0000000000001100  register_tm_clones
0x0000000000001140  __do_global_dtors_aux
0x0000000000001180  frame_dummy
0x0000000000001185  vuln
0x0000000000001289  gfl
0x00000000000012f6  main
0x0000000000001320  __libc_csu_init
0x0000000000001380  __libc_csu_fini
0x0000000000001384  _fini
```

## Step 2

Disassemble ```main``` and ```vuln```

main

```
0x00000000000012f6 <+0>:	push   %rbp
0x00000000000012f7 <+1>:	mov    %rsp,%rbp
0x00000000000012fa <+4>:	mov    $0x0,%eax
0x00000000000012ff <+9>:	call   0x1185 <vuln>
0x0000000000001304 <+14>:	mov    $0x0,%eax
0x0000000000001309 <+19>:	call   0x1289 <gfl>
0x000000000000130e <+24>:	mov    $0x0,%eax
0x0000000000001313 <+29>:	pop    %rbp
0x0000000000001314 <+30>:	ret
```

vuln

```
0x0000000000001185 <+0>:	push   %rbp
0x0000000000001186 <+1>:	mov    %rsp,%rbp
0x0000000000001189 <+4>:	sub    $0x2c0,%rsp
0x0000000000001190 <+11>:	movabs $0x207962206564614d,%rax
0x000000000000119a <+21>:	mov    %rax,-0xd(%rbp)
0x000000000000119e <+25>:	movl   $0x6e6f6e34,-0x5(%rbp)
0x00000000000011a5 <+32>:	movb   $0x0,-0x1(%rbp)
0x00000000000011a9 <+36>:	movabs $0x2f2f3a7370747468,%rax
0x00000000000011b3 <+46>:	movabs $0x632e627568746967,%rdx
0x00000000000011bd <+56>:	mov    %rax,-0x30(%rbp)
0x00000000000011c1 <+60>:	mov    %rdx,-0x28(%rbp)
0x00000000000011c5 <+64>:	movabs $0x69626f306e2f6d6f,%rax
0x00000000000011cf <+74>:	mov    %rax,-0x20(%rbp)
0x00000000000011d3 <+78>:	movw   $0x3474,-0x18(%rbp)
0x00000000000011d9 <+84>:	movb   $0x0,-0x16(%rbp)
0x00000000000011dd <+88>:	movabs $0x6435736a36424741,%rax
0x00000000000011e7 <+98>:	mov    %rax,-0x23e(%rbp)
0x00000000000011ee <+105>:	movl   $0x476b6439,-0x236(%rbp)
0x00000000000011f8 <+115>:	movw   $0x37,-0x232(%rbp)
0x0000000000001201 <+124>:	lea    0xdfc(%rip),%rdi        # 0x2004
0x0000000000001208 <+131>:	mov    $0x0,%eax
0x000000000000120d <+136>:	call   0x1050 <printf@plt>
0x0000000000001212 <+141>:	lea    -0x230(%rbp),%rax
0x0000000000001219 <+148>:	mov    %rax,%rsi
0x000000000000121c <+151>:	lea    0xdf8(%rip),%rdi        # 0x201b
0x0000000000001223 <+158>:	mov    $0x0,%eax
0x0000000000001228 <+163>:	call   0x1070 <__isoc99_scanf@plt>
0x000000000000122d <+168>:	lea    -0x230(%rbp),%rdx
0x0000000000001234 <+175>:	lea    -0x2c0(%rbp),%rax
0x000000000000123b <+182>:	mov    %rdx,%rsi
0x000000000000123e <+185>:	mov    %rax,%rdi
0x0000000000001241 <+188>:	call   0x1030 <strcpy@plt>
0x0000000000001246 <+193>:	lea    -0x23e(%rbp),%rdx
0x000000000000124d <+200>:	lea    -0x2c0(%rbp),%rax
0x0000000000001254 <+207>:	mov    %rdx,%rsi
0x0000000000001257 <+210>:	mov    %rax,%rdi
0x000000000000125a <+213>:	call   0x1060 <strcmp@plt>
0x000000000000125f <+218>:	test   %eax,%eax
0x0000000000001261 <+220>:	jne    0x1271 <vuln+236>
0x0000000000001263 <+222>:	lea    0xdb4(%rip),%rdi        # 0x201e
0x000000000000126a <+229>:	call   0x1040 <puts@plt>
0x000000000000126f <+234>:	jmp    0x1287 <vuln+258>
0x0000000000001271 <+236>:	lea    0xdaf(%rip),%rdi        # 0x2027
0x0000000000001278 <+243>:	call   0x1040 <puts@plt>
0x000000000000127d <+248>:	mov    $0x0,%edi
0x0000000000001282 <+253>:	call   0x1080 <exit@plt>
0x0000000000001287 <+258>:	leave
0x0000000000001288 <+259>:	ret
```

We can see that it is appending numerious strings into one and then comparing it at ```0x000000000000125a <+213>:	call   0x1060 <strcmp@plt>```

## Step 3

We set a breakpoint at ```0x000000000000125a``` and run the program

## Step 4

At the compare we find the flag
