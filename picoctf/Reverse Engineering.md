# GDB baby step 1
**FLAG** - picoCTF{549698}
## Approach:
First of all I research into the GNU Debugger(``GDB``) , due to it being stated in the challenge name, and also the first hint talking about it
, after this I download the file given in the challenge `debugger0_a` , after going through a `ctf101` page describing how gdb is used in reverse engineering I figure out that you have to disassemble the file, after watching a youtube video I figure out the syntax and functions required to go through with the disassembly,
afterwards I use `set disassembly-flavor intel` since gdb uses at&t as default, although this is mostly about how people want their data represented
afterwards I run `disassemble main` as hint 2 suggests that I have to do so.
After some Observation I see that the `eax` register has a value `0x86342` which is a hexadecimal number, I use an online hexadecimal to decimal converter to get the flag, ( 86342 in hexadecimal = 549698 in decimal)

![image](https://github.com/user-attachments/assets/e5114305-db7d-43ea-b3df-3c2c05e75232)



## Code
```bash
root@DESKTOP-MPJ395R:~# cd /mnt/c/Users/Aditya/Downloads
root@DESKTOP-MPJ395R:/mnt/c/Users/Aditya/Downloads# gdb debugger0_a
GNU gdb (Ubuntu 12.1-0ubuntu1~22.04.2) 12.1
Copyright (C) 2022 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.
Type "show copying" and "show warranty" for details.
This GDB was configured as "x86_64-linux-gnu".
Type "show configuration" for configuration details.
For bug reporting instructions, please see:
<https://www.gnu.org/software/gdb/bugs/>.
Find the GDB manual and other documentation resources online at:
    <http://www.gnu.org/software/gdb/documentation/>.

For help, type "help".
Type "apropos word" to search for commands related to "word"...
Reading symbols from debugger0_a...
(No debugging symbols found in debugger0_a)
(gdb) info functions
All defined functions:

Non-debugging symbols:
0x0000000000001000  _init
0x0000000000001030  __cxa_finalize@plt
0x0000000000001040  _start
0x0000000000001070  deregister_tm_clones
0x00000000000010a0  register_tm_clones
0x00000000000010e0  __do_global_dtors_aux
0x0000000000001120  frame_dummy
0x0000000000001129  main
0x0000000000001140  __libc_csu_init
0x00000000000011b0  __libc_csu_fini
0x00000000000011b8  _fini
(gdb) set disassembly-flavor intel
(gdb) disassemble main
Dump of assembler code for function main:
   0x0000000000001129 <+0>:     endbr64
   0x000000000000112d <+4>:     push   rbp
   0x000000000000112e <+5>:     mov    rbp,rsp
   0x0000000000001131 <+8>:     mov    DWORD PTR [rbp-0x4],edi
   0x0000000000001134 <+11>:    mov    QWORD PTR [rbp-0x10],rsi
   0x0000000000001138 <+15>:    mov    eax,0x86342
   0x000000000000113d <+20>:    pop    rbp
   0x000000000000113e <+21>:    ret
End of assembler dump.
```
## Concepts Learnt
- Basic GDB usage
- disassembling programs
- register values
## References
- https://ctf101.org/reverse-engineering/what-is-gdb/
- https://visualgdb.com/gdbreference/commands/set_disassembly-flavor
- https://visualgdb.com/gdbreference/commands/disassemble
- https://www.youtube.com/watch?v=B03p00ibkVU
