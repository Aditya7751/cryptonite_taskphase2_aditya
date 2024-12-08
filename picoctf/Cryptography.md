# miniRSA
**FLAG** - picoCTF{n33d_a_lArg3r_e_d0cd6eae}
## Approach
We are provided with the Cipertext, e value and N value, following the RSA formula `C=M^e(mod N)  `, and given the fact that our e value is very small
I just decided to the find the cube root of C to find M

```python
def cuberoot(n):
    if n < 2:
        return n
    x = n // 2
    while True:
        next_x = (2 * x + n // (x * x)) // 3
        if next_x >= x:
            return x
        x = next_x

root=cuberoot(2205316413931134031074603746928247799030155221252519872650080519263755075355825243327515211479747536697517688468095325517209911688684309894900992899707504087647575997847717180766377832435022794675332132906451858990782325436498952049751141)

```
implementing this formula for cube root using the newton raphson method. then upon finding the root I try to figure out how is plaintext converted 
to numerical form in the first place, thankfully I found this resource https://crypto.stackexchange.com/questions/3617/how-do-ciphers-change-plaintext-into-numeric-digits-for-computing , confirming that I have to convert this to hex form and then to a string from that

I do this with the following script
```python
hexstr=hex(root)
if hexstr.startswith('0x'):
        hex_tr = hexstr[2:]
 if len(hexstr) % 2 != 0:
        hexstr = '0' + hexstr
bytes.fromhex(hex_str).decode('utf-8')
```
## Incorrect Tangents
- I didnt know that I had to pad the string and hence struggled with getting meaningful output
- implementing newton raphson wasnt that hard, but I failed initially because I was implicitly dealing with floats and not forcing integer divison
## Concepts Learnt
- RSA Encryption
- Exploiting Weaknesses with weak e values
- Rudimentary Understanding of Wieners Algoritihim

# C3
**FLAG** - picoCTF{adlibs}
## Approach
In this problem we are given an encoding python script and corresponding ciphertext
```python
lookup1 = "\n \"#()*+/1:=[]abcdefghijklmnopqrstuvwxyz"
lookup2 = "ABCDEFGHIJKLMNOPQRSTabcdefghijklmnopqrst"

chars = "DLSeGAGDgBNJDQJDCFSFnRBIDjgHoDFCFtHDgJpiHtGDmMAQFnRBJKkBAsTMrsPSDDnEFCFtIbEDtDCIbFCFtHTJDKerFldbFObFCFtLBFkBAAAPFnRBJGEkerFlcPgKkImHnIlATJDKbTbFOkdNnsgbnJRMFnRBNAFkBAAAbrcbTKAkOgFpOgFpOpkBAAAAAAAiClFGIPFnRBaKliCgClFGtIBAAAAAAAOgGEkImHnIl"

decrypted_message = ""
prev = 0
for char in chars:
    cur = lookup2.index(char)
    decrypted_char_index = (cur + prev) % 40
    decrypted_message += lookup1[decrypted_char_index]
    prev = decrypted_char_index 

print(decrypted_message)
```
