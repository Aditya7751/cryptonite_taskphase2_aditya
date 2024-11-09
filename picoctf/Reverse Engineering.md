# GDB baby step 1
**FLAG** - picoCTF{549698}
## Approach:
First of all I research about the GNU Debugger(``GDB``) , due to it being stated in the challenge name, and also the first hint talking about it
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

# Vault Door 3
FLAG - **picoCTF{jU5t_a_s1mpl3_an4gr4m_4_u_c79a21}**
## Understanding the Problem
1. The Entered Password has to be 32 characters long
   
![image](https://github.com/user-attachments/assets/4aafb243-ff36-4242-810d-e5ae22bc5251)

2. The First 8 characters(indices 0 to 7) of the buffer and the entered password have to be the same

![image](https://github.com/user-attachments/assets/7ac720d3-a33e-4937-be14-a728f1525a20)

3.The 9th to 16th characters of the buffer are the same  the (23-i)th element of the entered password

![image](https://github.com/user-attachments/assets/34f845da-4e0d-4dcd-92c4-cca0af52b6e5)

4.The i increments in intervals of 2 from the 16th index to the 30th index(so even elements from 16), now these indices of the buffer are equal to the (46-i)th element of the password

![image](https://github.com/user-attachments/assets/7e1971d0-aef1-48b2-95c7-44d858108daf)

5. The Odd indices from 31 to 17 of the buffer are equal to the same index of the password

![image](https://github.com/user-attachments/assets/00925b3f-9a2e-4b1e-a042-5ba5dfe3ef54)

6. The Buffer has to be the same as "jU5t_a_sna_3lpm12g94c_u_4_m7ra41" for it to return true
   
![image](https://github.com/user-attachments/assets/7b9505c3-5fa4-4bd1-a155-96bf2ecaeb90)

## Approach

To Reverse this shuffling of letters, I use the same transformations , but interchanging the buffer and password variables, and also reversing the order of the operations that took place in the given program

```java
import java.util.*;

public class VaultCrack {
    public static void main(String[] args) {
        String buffer="jU5t_a_sna_3lpm12g94c_u_4_m7ra41";
        char[] password=new char[32];
        int i;
        for (i=31; i>=17; i-=2) {
            password[i] = buffer.charAt(i);
        }
        for(i=16;i<32;i+=2){
            password[i]=buffer.charAt(46-i);
        }
        for(i=8;i<16;i++){
            password[i]=buffer.charAt(23-i);
        }
        for(i=0;i<8;i++){
            password[i]=buffer.charAt(i);
        }
        String ans=new String(password);
        System.out.println(ans);
    }
}
```
## Incorrect Tangents
- Intially I tried to crack the program with "jU5t_a_sna_3lpm12g94c_u_4_m7ra41" as a password, but realized it was a medium problem for a reason
- I remember messing up the loops for this for a long time because I didnt read the initial program properly and kept initliazing i=0 in every loop out of habit

## Concepts Learnt
- Following how the logic flows through in problems, and also how to approach reversing them
- Reading the Question Properly

# ARMssembly 1
**FLAG** - picoCTF{000000e8}
## Approach
After downloading `chall_1.s` I figured that I had to look through the function given

```assembly
func:
	sub	sp, sp, #32
	str	w0, [sp, 12]
	mov	w0, 87
	str	w0, [sp, 16]
	mov	w0, 3
	str	w0, [sp, 20]
	mov	w0, 3
	str	w0, [sp, 24]
	ldr	w0, [sp, 20]
	ldr	w1, [sp, 16]
	lsl	w0, w1, w0
	str	w0, [sp, 28]
	ldr	w1, [sp, 28]
	ldr	w0, [sp, 24]
	sdiv	w0, w1, w0
	str	w0, [sp, 28]
	ldr	w1, [sp, 28]
	ldr	w0, [sp, 12]
	sub	w0, w1, w0
	str	w0, [sp, 28]
	ldr	w0, [sp, 28]
	add	sp, sp, 32
	ret
	.size	func, .-func
	.section	.rodata
	.align	3
```
This code essentially stores w0 at (sp+12),stores 87 at (sp+16),3 at (sp+20) and (sp+24), after a lot of mathematical manipulations, 232-w0 is stored at w0, which is very important to remember, as this w0 is returned. now there is a line in main
```assembly
cmp	w0, 0
```
which makes me sure that w0=232, since the comparison needs to be true for the win

## Incorrect tangents
- tried an assembly to c converter, but that didnt work as expected, so I just tried to read func normally after that(note: after solving this the normal way, I checked the assembly to c code, I think it got the order wrong in a few operations, but this was also a viable method to solve after a few adjustments to the code)
## Concepts Learnt
- Basic Assembly
