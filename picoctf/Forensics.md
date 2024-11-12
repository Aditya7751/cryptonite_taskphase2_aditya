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
