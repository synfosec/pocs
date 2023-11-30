# BBbbloat

#### Information Given

Can you get the flag? Reverse engineer this binary.

Hints: None

# Step 1

Check the security of the binary

```
arch     x86                                                  
baddr    0x0                                                 
binsz    12613                                               
bintype  elf                                           
bits     64                                                                                                                 
canary   true                                                                                                               
class    ELF64                                                                                                              
compiler GCC: (Ubuntu 9.4.0-1ubuntu1~20.04.1) 9.4.0                                                                         
crypto   false                                                                                                              
endian   little                                                                                                             
havecode true                                                                                                               
intrp    /lib64/ld-linux-x86-64.so.2                                                                                        
laddr    0x0                                                                                                                
lang     c                                                                                                                  
linenum  false                                                                                                              
lsyms    false                                                                                                              
machine  AMD x86-64 architecture                             
nx       true                                                                                                               
os       linux                                                                                                              
pic      true                                                                                                               
relocs   false                                                                                                              
relro    full                                                                                                               
rpath    NONE                                                                                                               
sanitize false                                                                                                              
static   false                                                                                                              
stripped true                                                                                                               
subsys   linux                                                                                                              
va       true
```

There is canary, pie and nx

# Step 2

Dump all funcs

```
0x00001160    1     47 entry0
0x00003fe0    1    208 fcn.00003fe0
0x000010d0    1     11 sym.imp.free
0x000010e0    1     11 sym.imp.putchar
0x000010f0    1     11 sym.imp.puts
0x00001100    1     11 sym.imp.strlen
0x00001110    1     11 sym.imp.__stack_chk_fail
0x00001120    1     11 sym.imp.printf
0x00001130    1     11 sym.imp.fputs
0x00001140    1     11 sym.imp.__isoc99_scanf
0x00001150    1     11 sym.imp.strdup
0x00001307    6    675 main
0x00001240    5     60 entry.init0
0x00001200    5     54 entry.fini0
0x000010c0    1     11 fcn.000010c0
0x00001190    4     34 fcn.00001190
0x00001249   10    190 fcn.00001249
0x00001000    3     27 fcn.00001000
0x00001030    2     28 fcn.00001030
0x00001040    1     15 fcn.00001040
0x00001050    1     15 fcn.00001050
0x00001060    1     15 fcn.00001060
0x00001070    1     15 fcn.00001070
0x00001080    1     15 fcn.00001080
0x00001090    1     15 fcn.00001090
0x000010a0    1     15 fcn.000010a0
0x000010b0    1     15 fcn.000010b0
0x00001628    1     13 fcn.00001628
```

We have ```main```

# Step 3

Disassemble ```main```

```
┌ 675: int main (char **envp, int64_t arg4);
│           ; arg int64_t arg3 @ rdx
│           ; arg int64_t arg4 @ rcx
│           ; var int64_t var_8h @ rbp-0x8
│           ; var int64_t var_18h @ rbp-0x18
│           ; var int64_t var_20h @ rbp-0x20
│           ; var int64_t var_28h @ rbp-0x28
│           ; var int64_t var_30h @ rbp-0x30
│           ; var int64_t var_38h @ rbp-0x38
│           ; var int64_t var_3ch @ rbp-0x3c
│           ; var int64_t var_40h @ rbp-0x40
│           ; var int64_t var_44h @ rbp-0x44
│           ; var int64_t var_50h @ rbp-0x50
│           0x563bd2d51307 b    f30f1efa       endbr64
│           0x563bd2d5130b      55             pushq %rbp
│           0x563bd2d5130c      4889e5         movq %rsp, %rbp
│           0x563bd2d5130f      4883ec50       subq $0x50, %rsp
│           0x563bd2d51313      897dbc         movl %edi, var_44h
│           0x563bd2d51316      488975b0       movq %rsi, var_50h
│           0x563bd2d5131a      64488b042528.  movq %fs:0x28, %rax
│           0x563bd2d51323      488945f8       movq %rax, var_8h
│           0x563bd2d51327      31c0           xorl %eax, %eax
│           0x563bd2d51329      48b8413a3440.  movabsq $0x4c75257240343a41, %rax
│           0x563bd2d51333      48ba34466630.  movabsq $0x3062396630664634, %rdx ; arg3
│           0x563bd2d5133d      488945d0       movq %rax, var_30h
│           0x563bd2d51341      488955d8       movq %rdx, var_28h
│           0x563bd2d51345      48b8333d5f63.  movabsq $0x65623066635f3d33, %rax
│           0x563bd2d5134f      48ba35356260.  movabsq $0x4e326560623535, %rdx
│           0x563bd2d51359      488945e0       movq %rax, var_20h
│           0x563bd2d5135d      488955e8       movq %rdx, var_18h
│           0x563bd2d51361      c745c4783000.  movl $0x3078, var_3ch
│           0x563bd2d51368      8145c49ec213.  addl $0x13c29e, var_3ch
│           0x563bd2d5136f      816dc4a83000.  subl $0x30a8, var_3ch
│           0x563bd2d51376      d165c4         shll var_3ch
│           0x563bd2d51379      8b45c4         movl var_3ch, %eax
│           0x563bd2d5137c      4863d0         movslq %eax, %rdx
│           0x563bd2d5137f      4869d2565555.  imulq $0x55555556, %rdx, %rdx
│           0x563bd2d51386      48c1ea20       shrq $0x20, %rdx
│           0x563bd2d5138a      c1f81f         sarl $0x1f, %eax
│           0x563bd2d5138d      89d1           movl %edx, %ecx         ; arg4
│           0x563bd2d5138f      29c1           subl %eax, %ecx         ; arg4
│           0x563bd2d51391      89c8           movl %ecx, %eax
│           0x563bd2d51393      8945c4         movl %eax, var_3ch
│           0x563bd2d51396      c745c4783000.  movl $0x3078, var_3ch
│           0x563bd2d5139d      8145c49ec213.  addl $0x13c29e, var_3ch
│           0x563bd2d513a4      816dc4a83000.  subl $0x30a8, var_3ch
│           0x563bd2d513ab      d165c4         shll var_3ch
│           0x563bd2d513ae      8b45c4         movl var_3ch, %eax
│           0x563bd2d513b1      4863d0         movslq %eax, %rdx
│           0x563bd2d513b4      4869d2565555.  imulq $0x55555556, %rdx, %rdx
│           0x563bd2d513bb      48c1ea20       shrq $0x20, %rdx
│           0x563bd2d513bf      c1f81f         sarl $0x1f, %eax
│           0x563bd2d513c2      89d1           movl %edx, %ecx
│           0x563bd2d513c4      29c1           subl %eax, %ecx
│           0x563bd2d513c6      89c8           movl %ecx, %eax
│           0x563bd2d513c8      8945c4         movl %eax, var_3ch
│           0x563bd2d513cb      488d3d320c00.  leaq str.Whats_my_favorite_number__, %rdi
│           0x563bd2d513d2      b800000000     movl $0, %eax
│           0x563bd2d513d7      e844fdffff     callq sym.imp.printf    ; int printf(const char *format)
│           0x563bd2d513dc      c745c4783000.  movl $0x3078, var_3ch
│           0x563bd2d513e3      8145c49ec213.  addl $0x13c29e, var_3ch
│           0x563bd2d513ea      816dc4a83000.  subl $0x30a8, var_3ch
│           0x563bd2d513f1      d165c4         shll var_3ch
│           0x563bd2d513f4      8b45c4         movl var_3ch, %eax
│           0x563bd2d513f7      4863d0         movslq %eax, %rdx
│           0x563bd2d513fa      4869d2565555.  imulq $0x55555556, %rdx, %rdx
│           0x563bd2d51401      48c1ea20       shrq $0x20, %rdx
│           0x563bd2d51405      c1f81f         sarl $0x1f, %eax
│           0x563bd2d51408      89d6           movl %edx, %esi
│           0x563bd2d5140a      29c6           subl %eax, %esi
│           0x563bd2d5140c      89f0           movl %esi, %eax
│           0x563bd2d5140e      8945c4         movl %eax, var_3ch
│           0x563bd2d51411      c745c4783000.  movl $0x3078, var_3ch
│           0x563bd2d51418      8145c49ec213.  addl $0x13c29e, var_3ch
│           0x563bd2d5141f      816dc4a83000.  subl $0x30a8, var_3ch
│           0x563bd2d51426      d165c4         shll var_3ch
│           0x563bd2d51429      8b45c4         movl var_3ch, %eax
│           0x563bd2d5142c      4863d0         movslq %eax, %rdx
│           0x563bd2d5142f      4869d2565555.  imulq $0x55555556, %rdx, %rdx
│           0x563bd2d51436      48c1ea20       shrq $0x20, %rdx
│           0x563bd2d5143a      c1f81f         sarl $0x1f, %eax
│           0x563bd2d5143d      89d1           movl %edx, %ecx
│           0x563bd2d5143f      29c1           subl %eax, %ecx
│           0x563bd2d51441      89c8           movl %ecx, %eax
│           0x563bd2d51443      8945c4         movl %eax, var_3ch
│           0x563bd2d51446      488d45c0       leaq var_40h, %rax
│           0x563bd2d5144a      4889c6         movq %rax, %rsi
│           0x563bd2d5144d      488d3dcc0b00.  leaq 0x563bd2d52020, %rdi
│           0x563bd2d51454      b800000000     movl $0, %eax
│           0x563bd2d51459      e8e2fcffff     callq sym.imp.__isoc99_scanf ; int scanf(const char *format)
│           0x563bd2d5145e      c745c4783000.  movl $0x3078, var_3ch
│           0x563bd2d51465      8145c49ec213.  addl $0x13c29e, var_3ch
│           0x563bd2d5146c      816dc4a83000.  subl $0x30a8, var_3ch
│           0x563bd2d51473      d165c4         shll var_3ch
│           0x563bd2d51476      8b45c4         movl var_3ch, %eax
│           0x563bd2d51479      4863d0         movslq %eax, %rdx
│           0x563bd2d5147c      4869d2565555.  imulq $0x55555556, %rdx, %rdx
│           0x563bd2d51483      48c1ea20       shrq $0x20, %rdx
│           0x563bd2d51487      c1f81f         sarl $0x1f, %eax
│           0x563bd2d5148a      89d6           movl %edx, %esi
│           0x563bd2d5148c      29c6           subl %eax, %esi
│           0x563bd2d5148e      89f0           movl %esi, %eax
│           0x563bd2d51490      8945c4         movl %eax, var_3ch
│           0x563bd2d51493      c745c4783000.  movl $0x3078, var_3ch
│           0x563bd2d5149a      8145c49ec213.  addl $0x13c29e, var_3ch
│           0x563bd2d514a1      816dc4a83000.  subl $0x30a8, var_3ch
│           0x563bd2d514a8      d165c4         shll var_3ch
│           0x563bd2d514ab      8b45c4         movl var_3ch, %eax
│           0x563bd2d514ae      4863d0         movslq %eax, %rdx
│           0x563bd2d514b1      4869d2565555.  imulq $0x55555556, %rdx, %rdx
│           0x563bd2d514b8      48c1ea20       shrq $0x20, %rdx
│           0x563bd2d514bc      c1f81f         sarl $0x1f, %eax
│           0x563bd2d514bf      89d7           movl %edx, %edi
│           0x563bd2d514c1      29c7           subl %eax, %edi
│           0x563bd2d514c3      89f8           movl %edi, %eax
│           0x563bd2d514c5      8945c4         movl %eax, var_3ch
│           0x563bd2d514c8      8b45c0         movl var_40h, %eax
│           0x563bd2d514cb      3d87610800     cmpl $0x86187, %eax
│       ┌─< 0x563bd2d514d0      0f85ad000000   jne 0x563bd2d51583
│       │   0x563bd2d514d6      c745c4783000.  movl $0x3078, var_3ch
│       │   0x563bd2d514dd      8145c49ec213.  addl $0x13c29e, var_3ch
│       │   0x563bd2d514e4      816dc4a83000.  subl $0x30a8, var_3ch
│       │   0x563bd2d514eb      d165c4         shll var_3ch
│       │   0x563bd2d514ee      8b45c4         movl var_3ch, %eax
│       │   0x563bd2d514f1      4863d0         movslq %eax, %rdx
│       │   0x563bd2d514f4      4869d2565555.  imulq $0x55555556, %rdx, %rdx
│       │   0x563bd2d514fb      48c1ea20       shrq $0x20, %rdx
│       │   0x563bd2d514ff      c1f81f         sarl $0x1f, %eax
│       │   0x563bd2d51502      89d1           movl %edx, %ecx
│       │   0x563bd2d51504      29c1           subl %eax, %ecx
│       │   0x563bd2d51506      89c8           movl %ecx, %eax
│       │   0x563bd2d51508      8945c4         movl %eax, var_3ch
│       │   0x563bd2d5150b      c745c4783000.  movl $0x3078, var_3ch
│       │   0x563bd2d51512      8145c49ec213.  addl $0x13c29e, var_3ch
│       │   0x563bd2d51519      816dc4a83000.  subl $0x30a8, var_3ch
│       │   0x563bd2d51520      d165c4         shll var_3ch
│       │   0x563bd2d51523      8b45c4         movl var_3ch, %eax
│       │   0x563bd2d51526      4863d0         movslq %eax, %rdx
│       │   0x563bd2d51529      4869d2565555.  imulq $0x55555556, %rdx, %rdx
│       │   0x563bd2d51530      48c1ea20       shrq $0x20, %rdx
│       │   0x563bd2d51534      c1f81f         sarl $0x1f, %eax
│       │   0x563bd2d51537      89d6           movl %edx, %esi
│       │   0x563bd2d51539      29c6           subl %eax, %esi
│       │   0x563bd2d5153b      89f0           movl %esi, %eax
│       │   0x563bd2d5153d      8945c4         movl %eax, var_3ch
│       │   0x563bd2d51540      488d45d0       leaq var_30h, %rax
│       │   0x563bd2d51544      4889c6         movq %rax, %rsi
│       │   0x563bd2d51547      bf00000000     movl $0, %edi
│       │   0x563bd2d5154c      e8f8fcffff     callq 0x563bd2d51249
│       │   0x563bd2d51551      488945c8       movq %rax, var_38h
│       │   0x563bd2d51555      488b15b42a00.  movq reloc.stdout, %rdx ; [0x563bd2d54010:8]=0x7f4c6b96f760 ; "`\xf7\x96kL\x7f"
│       │   0x563bd2d5155c      488b45c8       movq var_38h, %rax
│       │   0x563bd2d51560      4889d6         movq %rdx, %rsi
│       │   0x563bd2d51563      4889c7         movq %rax, %rdi
│       │   0x563bd2d51566      e8c5fbffff     callq sym.imp.fputs     ; int fputs(const char *s, FILE *stream)
│       │   0x563bd2d5156b      bf0a000000     movl $0xa, %edi
│       │   0x563bd2d51570      e86bfbffff     callq sym.imp.putchar   ; int putchar(int c)
│       │   0x563bd2d51575      488b45c8       movq var_38h, %rax
│       │   0x563bd2d51579      4889c7         movq %rax, %rdi
│       │   0x563bd2d5157c      e84ffbffff     callq sym.imp.free      ; void free(void *ptr)
│      ┌──< 0x563bd2d51581      eb0c           jmp 0x563bd2d5158f
│      │└─> 0x563bd2d51583      488d3d990a00.  leaq str.Sorry__thats_not_it_, %rdi
│      │    0x563bd2d5158a      e861fbffff     callq sym.imp.puts      ; int puts(const char *s)
│      │    ; CODE XREF from main @ 0x563bd2d51581(x)
│      └──> 0x563bd2d5158f      b800000000     movl $0, %eax
│           0x563bd2d51594      488b7df8       movq var_8h, %rdi
│           0x563bd2d51598      6448333c2528.  xorq %fs:0x28, %rdi
│       ┌─< 0x563bd2d515a1      7405           je 0x563bd2d515a8
│       │   0x563bd2d515a3      e868fbffff     callq sym.imp.__stack_chk_fail ; void __stack_chk_fail(void)
│       └─> 0x563bd2d515a8      c9             leave
└           0x563bd2d515a9      c3             retq
```

We can see a cmp after the program prints the prompt

```
0x563bd2d514cb      3d87610800     cmpl $0x86187, %eax
```

But ```%eax``` is being compared to a literal hex value, if it's not the same it exits the program

# Exploit

The flag is revealed when running the program and inputting the value
