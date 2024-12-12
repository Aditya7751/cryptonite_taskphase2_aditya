# Trivial Flag Transfer Protocol
**FLAG** - picoCTF{h1dd3n_1n_pLa1n_51GHT_18375919}
## Approach
First I download the `tftp.pcapng` file, now I download and open it on wireshark
![image](https://github.com/user-attachments/assets/bb0f5dfb-90eb-42f8-8a61-19597a56bc0a)
now I look into what the tftp protocol is, I find out it is used for transferring files between servers.
knowing that I use wireshark to export all files in tftp.pcapng, using export objects -> tftp
![image](https://github.com/user-attachments/assets/23e47a8c-47ce-437f-a4dd-dda126d9bbcf)
I find these files in there

instructions.txt contains the following
` GSGCQBRFAGRAPELCGBHEGENSSVPFBJRZHFGQVFTHVFRBHESYNTGENAFSRE.SVTHERBHGNJNLGBUVQRGURSYNTNAQVJVYYPURPXONPXSBEGURCYNA `
which I decode using ROT13
` TFTPDOESNTENCRYPTOURTRAFFICSOWEMUSTDISGUISEOURFLAGTRANSFER.FIGUREOUTAWAYTOHIDETHEFLAGANDIWILLCHECKBACKFORTHEPLAN `

now upon decoding plan with ROT13
`IUSEDTHEPROGRAMANDHIDITWITH-DUEDILIGENCE.CHECKOUTTHEPHOTOS`

I decide to install the program.deb file with `apt-install program.deb` but I see that it installs steghide, confirming that I need to use steghide to find a hidden flag in one of the 3 pictures

I run `steghide extract -sf ./picture1.bmp -p DUEDILIGENCE` it took me a while to figure out that DUEDILIGENCE was the password
this doesnt work so I try `steghide extract -sf ./picture2.bmp -p DUEDILIGENCE` which doesnt work either, but luckily `steghide extract -sf ./picture3.bmp -p DUEDILIGENCE` works and gives me a flag.txt file

## Incorrect Tangents
- Kept using wrong passwords while using steghide until I read the thing properly
## Concepts Learnt
- Using Steganographic Tools
- File Protocols

# m00nwalk
**FLAG** - picoCTF{beep_boop_im_in_space}
## Approach
I start by googling around the method they used to send the images back from the moon, which turns out to be sstv, a way to send images through radio waves, also after further investigation into how sstv has different modes and how it works, I figure it will use scottie since the hint said its related to the mascot of cmu(scottie).

Then I use a sstv decoder i find on github to decipher the image
![image](https://github.com/user-attachments/assets/c03a3089-917e-490f-82bd-b05d73b9b74f)
after turning my screen around a bit I figure out whats written, and I submit that as the flag

## Incorrect Tangents
- I tried using a lot of sstv software like rxsstv but they always messed up the quality into something unreadable
## Concepts Learnt
- Audio Steganography

# tunn3l v1s10n
**FLAG**- 
On Opening the file on hexedit, the first 2 bytes are `42 4D`, which are the magic bytes for BMP,confirming that the file is a BMP, but changing the extension still doesnt let me open the file, so I analyze the file to see the offset is `BA D0 00 00` which is like almost 54000, the offset for bytes should be 40 or 0x28 in hex, to account for the 54 bytes with file information.
I open this file to see
![image](https://github.com/user-attachments/assets/12854091-df7d-44c8-8e2c-88c89d646771)
then I decided to mess with the width of the image changing it from `32 01` to `40 03` which finally gave me the full image as upon some research I found there has to be an increment of 2
![image](https://github.com/user-attachments/assets/0abb6009-9cd2-4f55-b5a2-5c34ba7f139a)

## Incorrect Tangents
- Used Steganographic methods to find the flag
- Messed up changing the hex codes the first few times
## Resources Used
- https://www.ece.ualberta.ca/~elliott/ee552/studentAppNotes/2003_w/misc/bmp_file_format/bmp_file_format.htm
- https://en.wikipedia.org/wiki/BMP_file_format
## Concepts Learnt
- Hex Value Manipulation


