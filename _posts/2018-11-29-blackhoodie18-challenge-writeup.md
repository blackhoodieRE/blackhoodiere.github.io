---
layout: single
title: "Solving the Blackhoodie18 Challenge"
exerpt: "defusing a binary bomb for fun and learning!"
tags: [reverse engineering, security, malware, BlackHoodie]
date: 2018-11-29 10:00:00 -0700
---

## defusing a binary bomb for fun and learning!

This year's Blackhoodie challenge was designed by [N1aKan](https://twitter.com/N1aKan).
As a first time Blackhoodie attendee and a complete newbie reverse engineer (this is the first time I solved a challenge),
it was a bit overwhelming. In this blogpost, I'll give a walkthrough of how to defuse a binary bomb from a first timer's perspective with no prior knowledge of reverse engineering, armed with a search engine and a bit of gdb know-how.

### Initial reconnaissance
The challenge archive contains one file: 7355608.c4. The extension on the file is .c4, which could be a hint or
an obfusating detail. In either case, we start with a Google search for the file extension to see if anything significant turns up.
A search reveals that .c4 denotes a compressed bitmap image format, which does not match with the output we get when we
analyse the file using the `file` utility on Linux.

```bash
7355608.c4: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, for GNU/Linux 2.6.32, BuildID[sha1]=17c8857a28c514ad309346cff48d88a3459b0731, not stripped
```
The output shows that we are dealing with an executable file in the ELF format.
To find out a bit more about the kinds of things that might be hiding inside this file, we can use the `readelf` commandline
tool, which displays information about ELF files. In particular, we are interested in the symbol table - a data structure
that contains information about the functions, objects, classes, interfaces and so forth present in the program. Looking at the kinds of things that makeup this program might give us a hint about how to start finding the flag. To read the symbol table
from the ELF file, we need to run the `readelf` utility with the `-s` flag.

```bash
readelf -s 7355608.c4
```

There are lots of interesting things in the output. Among them we see two names that look like C source code files
`bomb.c` and `crtstuff.c` and a bunch of interesting function names: `func_cipher42`, `apoptosis`,
`func_cipher6`, `func_cipher13`, `nuke` and `bomb`. A quick internet search reveals that `crtstuff.c`
is a standard source code file part of the GCC compiler so it's unlikely to have our flag, while `bomb.c` leads to a site about binary bombs - binaries that need to be 'defused' by manipulating some aspect about them (for example their input).


### Defusing the binary bomb

Now that we know this file might be a binary bomb, it's time to run it to see what happens!
We copy the file to a virtual machine that's not connected to any network (just in case the binary tries something funny!)
and run it. We see the following output printed to stdout.

```bash
The bomb has been planted.
BOOM !
```

It looks like we triggered the binary bomb, but the fact that the program prints something out to stdout
gives us a good first hint of what to look for. To look inside the binary and see what it actually does,
we need to disassemble it. We can be pretty sure that this is a C program and C programs always start executing
from the main function, so that is a great starting point.
Let's launch GDB with our target binary.

```bash
gdb 7355608.c4
```

and then disassemble the `main` function.

```bash
disas main
```

There are lots of x86 instructions in the output and for a first-timer, most of these don't make much sense,
but we do know that the program will print some output to stdout right before printing BOOM! and exiting, so
we skip in the disassembly output until we hit a call to the `putchar` function, which is a standard C-library function for printing characters to screen (see a snippet of the disassembled output below).

```c
0x0000000000400c62 <+822>:   callq  0x400560 <putchar@plt>
0x0000000000400c67 <+827>:   addl   $0x1,-0x16c(%rbp)
0x0000000000400c6e <+834>:   cmpl   $0x19,-0x16c(%rbp)
0x0000000000400c75 <+841>:   jle    0x400c4e <main+802>
0x0000000000400c77 <+843>:   mov    $0xa,%edi
0x0000000000400c7c <+848>:   callq  0x400560 <putchar@plt>
0x0000000000400c81 <+853>:   jmp    0x400c8d <main+865>
0x0000000000400c83 <+855>:   mov    $0x0,%eax
0x0000000000400c88 <+860>:   callq  0x4006d6 <boom>
0x0000000000400c8d <+865>:   callq  0x4005c0 <__ctype_b_loc@plt>

[some lines redacted for brevity]

0x0000000000400caf <+899>:   test   %eax,%eax
0x0000000000400cb1 <+901>:   je     0x400c83 <main+855>
0x0000000000400cb3 <+903>:   mov    $0x0,%ecx
0x0000000000400cb8 <+908>:   mov    $0x1,%edx
0x0000000000400cbd <+913>:   mov    $0x0,%esi
0x0000000000400cc2 <+918>:   mov    $0x0,%edi
0x0000000000400cc7 <+923>:   mov    $0x0,%eax
```

Examining the lines above, we notice that at offset 899 the code branches.
The [`test`](https://en.wikipedia.org/wiki/TEST_(x86_instruction)) instruction performs a bitwise AND comparison on the value
at register eax and then sets the value of the ZF flag based on the result. The value will be 1 if the result of the comparison is 0 and 0 otherwise. The value of ZF determines what happens at offset 901. Let's put a breakpoint at offset 899 and examine the value of eax.

```c
info registers eax
eax            0x0      0
```
It is 0.
When we examine the value of the ZF flag, we see that it is indeed set to 1 (since it appears in the eflags list. If the value of ZF was zero, we would not see ZF in the eflags list below).

```c
p $eflags
$2 = [ PF ZF IF ]
```

If the value of ZF is 1, the control in the program jumps to offset 855 and we see that at 860, we call
the function `boom`, which we can assume is the function responsible for printing 'BOOM!' and triggering the binary bomb. Of course, we should not take the name of the function at face value. After all, it could be that
the author of the challenge chose the name intentionally to confuse us :).

To confirm that it is indeed the
`boom` function that prints out `BOOM!` to stdout and exits, we disassemble it

```bash
disas boom
```

and find a loop that calls `putchar` (this is the part that prints BOOM!) and a call to `exit` (see offset 118 in the disassembled snippet below), which exits the program (ie triggers the bomb).

```c
0x000000000040072e <+88>:    callq  0x400560 <putchar@plt>
0x0000000000400733 <+93>:    addl   $0x1,-0x24(%rbp)
0x0000000000400737 <+97>:    cmpl   $0x5,-0x24(%rbp)
0x000000000040073b <+101>:   jle    0x400720 <boom+74>
0x000000000040073d <+103>:   mov    $0xa,%edi
0x0000000000400742 <+108>:   callq  0x400560 <putchar@plt>
0x0000000000400747 <+113>:   mov    $0x1,%edi
0x000000000040074c <+118>:   callq  0x4005b0 <exit@plt>
```
So we can pretty confident we found the function that triggers the bomb!

### Cutting the right wires

Now that we have an idea of what triggers the bomb, we have to find the 'right wires to cut' to defuse it and find the flag.
In the case examined above, we have to change the value stored in register eax to something other than zero.
We place a breakpoint at offset 899 and use gdb to change the value at eax to 5.

```c
set $eax=5
```

and then continue the execution. The program prints out

```c
I see you debugging me !
BOOM !
```
and exits once again, which means we've managed to avoid the first tripwire and hit the second one.
Examining the disassembled code of the `main` function, we notice that the program is calling `ptrace` at offset 928. `ptrace` is
a Linux system call that allows a process to control the execution of another process. The binary bomb uses this system call to
detect that it is being debugged.

```c
0x0000000000400ccc <+928>:   callq  0x4005a0 <ptrace@plt>
0x0000000000400cd1 <+933>:   cmp    $0xffffffffffffffff,%rax
0x0000000000400cd5 <+937>:   jne    0x400d20 <main+1012>
0x0000000000400cd7 <+939>:   movl   $0x0,-0x16c(%rbp)
0x0000000000400ce1 <+949>:   jmp    0x400d03 <main+983>
```
The program then checks if the value in the register rax is equal to 0xffffffffffffffff (offset 933 above).
If the values are equal, the program jumps back to offset 983 in the main function, which eventually leads to triggering the bomb as we can see below at 1007.

```c
0x0000000000400d03 <+983>:   cmpl   $0x17,-0x16c(%rbp)
0x0000000000400d0a <+990>:   jle    0x400ce3 <main+951>
0x0000000000400d0c <+992>:   mov    $0xa,%edi
0x0000000000400d11 <+997>:   callq  0x400560 <putchar@plt>
0x0000000000400d16 <+1002>:  mov    $0x0,%eax
0x0000000000400d1b <+1007>:  callq  0x4006d6 <boom>
```
On the other hand, if the values are not equal we jump right to offset 1012 and avoid triggering the bomb.
To avoid hitting this tripwire, we can set a gdb breakpoint at offset 933, examine the value of register rax
and change it to something other than 0xffffffffffffffff.

```c
info registers rax
rax            0xffffffffffffffff       -1
set $rax=60
```

We continue in a similar manner stepping through the program, trying to find the paths that lead the code
to call the `boom` function and manipulating the values in the registers so that we jump over these paths.
Following this procedure leads us to a final twist.

#### The Code of Forking Paths
In this final twist, the execution of the program has three possible routes to take.
```c
0x0000000000400dc2 <+1174>:  test   %eax,%eax
0x0000000000400dc4 <+1176>:  je     0x400de3 <main+1207>
0x0000000000400dc6 <+1178>:  cmp    $0x400,%eax
0x0000000000400dcb <+1183>:  je     0x400def <main+1219>
0x0000000000400dcd <+1185>:  cmp    $0xffffffff,%eax
0x0000000000400dd0 <+1188>:  jne    0x400e0d <main+1249>
```

We are testing the value at the register eax again and depending on its value we can possibly jump to three different sections of the `main` function: 1207, 1219 and 1249. By reading the disassembled sections of `main`, we quickly see that jumping to
1207 will trigger the boom function again, so we should definitely not set the value of `rax` to 0 (remember the x86 `test` instruction does a bitwise AND comparison on the values and sets the flag ZF to 1 if the result is 0. The subsequent `je` x86 instruction checks if the value of `ZF` is 1 and if yes, jumps to the indicated offset in the code). In this case we jump to 1207 (see below) and trigger the `boom` function.

```c
 0x0000000000400de3 <+1207>:  mov    $0x0,%eax
 0x0000000000400de8 <+1212>:  callq  0x4006d6 <boom>
```

 The remaining question is whether we should set the value of eax so that we go to offset 1219 or offset 1249. The difference between going to section 1219 is that here we will pass through the function `func_cipher42` twice. Disassembling the internals of the func_cipher42 function reveals that it loops 25 times and xors some values, which potentially means the function is manipulating the flag in some way. In the end, testing out both paths, we get the following. Going to 1219, prints out this to the screen

```c
a{~?]+*Bu=FL[]dk |CJOod
```

And going to 1249 prints out this

```c
The bomb has been defused ! :)
```

which means we have defused the bomb and found the flag!



----

Wanna try it out? [Here](https://github.com/DevanShellc0de/MISC/tree/master/Blackhoodie18_Challenge) is the chall + see the source code!
