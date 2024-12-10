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
In this problem we are given an encoding python script and corresponding ciphertext, the encoding algorithim maps any message from lookup1 to lookup2

convert.py
```python
import sys
chars = ""
from fileinput import input
for line in input():
  chars += line

lookup1 = "\n \"#()*+/1:=[]abcdefghijklmnopqrstuvwxyz"
lookup2 = "ABCDEFGHIJKLMNOPQRSTabcdefghijklmnopqrst"

out = ""

prev = 0
for char in chars:
  cur = lookup1.index(char)
  out += lookup2[(cur - prev) % 40]
  prev = cur

sys.stdout.write(out)
```
This Program essentially works as such:
- initliaze prev as 0
- find the current index of the letter being inspected
- find the difference between cur and prev, and assign characters from lookup2 as such
- repeat till last character

My Decryption Algorithim
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
- the (cur-prev)%40 is simply reversed by (cur+prev)%40 after changing the order of the instructions a bit
- on running this I get the following output which I save as decrypt.txt
```python
#asciiorder
#fortychars
#selfinput
#pythontwo

chars = decrypted_message
b = 1 / 1

for i in range(len(chars)):
    if i == b * b * b:
        print (chars[i]) #prints
        b += 1 / 1
```
seeing the self input comment, I just enter this as input again, finally getting the flag
## Incorrect Tangents
- Struggled with reversing the modulo operation initially
- entered original ciphertext into new script
## Concepts Learnt
- Reversing Encryption Algorithims
- Learnt about Algorithim Design and how one should not rely on the secrecy of the algorithim

# Sum-O-Primes
**FLAG** -picoCTF{pl33z_n0_g1v3_c0ngru3nc3_0f_5qu4r35_92fe3557}
## Approach
In this problem we are not only given the product of p and q, but also their sum , a pretty standard problem involving RSA( C=M^e(mod N)) and reversing it using extra information or vulnerabilities

Encoding Script
```python
!/usr/bin/python

from binascii import hexlify
from gmpy2 import mpz_urandomb, next_prime, random_state
import math
import os
import sys

if sys.version_info < (3, 9):
    import gmpy2
    math.gcd = gmpy2.gcd
    math.lcm = gmpy2.lcm

FLAG  = open('flag.txt').read().strip()
FLAG  = int(hexlify(FLAG.encode()), 16)
SEED  = int(hexlify(os.urandom(32)).decode(), 16)
STATE = random_state(SEED)

def get_prime(bits):
    return next_prime(mpz_urandomb(STATE, bits) | (1 << (bits - 1)))

p = get_prime(1024)
q = get_prime(1024)

x = p + q
n = p * q

e = 65537

m = math.lcm(p - 1, q - 1)
d = pow(e, -1, m)

c = pow(FLAG, e, n)

print(f'x = {x:x}')
print(f'n = {n:x}')
print(f'c = {c:x}')
```
Now Lets say p+q = sum and p*q=product

we can make a quadratic from this of form x^2+bx+c=0, where b= -(sum) and c = product, the roots for this will be p and q

first I implement a square root function, since the builtin sqrt function deals with floats and with numbers this big it has a good chance of overflowing, and follow it up with some quadratic solving going with the assumption that the discriminant will be a whole number and that dividing (- b +/- d) by 2a will give me a whole number

Script to get message back :
```python
def sqrt(n):
    if n < 2:
        return n
    x = n // 2
    while True:
        next_x = (  x + n // ( x)) // 2
        if next_x >= x:
            return x
        x = next_x
a=1
b='1626a189dcb38ca6b8e9ee26623ab5c3c6cd7e4c7ff6726f4b03831ca48c617a056827c5763458d0aa7172650072b892649cc73f943f156b795ff5dd2fc9a53b140cf9c3ee2cbb8181d17bb0275f404b4090766f798ad156db7e71000e93db65f3e1bc7406532d0f509fbecf095ef215b4ad51f5e8ac765861e5f93808948bf72'
b1=int(b,16)
c='720d66204ec312d7f1bc688495d4585ec58520170b86ed3488c3f9c76407b7e9e466b82a282ba90d484698160f2e27f413b07cf8805d560abdffa977547d5fec3190a1ce284dfc8e92193f2f70590bf9c6e6d0ab449e35ef43ed20232b7f8686696125cde1f950230fbc6858392a3715c1b8a4947748b7fadd5cc921716ad5e0129c91ea88fceee140fb1c594606186afacb69143ef8f7b3b1aa2cc3206395c60e71ec0555dd15838d8a8395e8ccf9a4e4c4199ae0ab3f8af7ebc6605edc5ddd480be2d6c41e38618eba5822a1e566080877268802750de71e890ac865ebf87fdc290d9151e407dff4c97390c9e7388fd538e2716515cea2240f55963c2e0c21'
c1=int(c,16)

d=sqrt(b1*b1 - 4*a*c1)
p=((-b1-d)//2*a)*-1
q=((-b1+d)//2*a)*-1
e=65537
m = math.lcm(p - 1, q - 1)
d = pow(e, -1, m)
cipher="554b90eb12fbece709d7bf23ab91f9b52d71cd77fbf42f65d68623c2055d99956b9bcf2eaf14771fa5781fae86624e44b452a0f68768849faba1b9695ce353a17238a3e7040ee7aede68b35bf4b51daf0982653910b280ac98aad9a5b3c49d226e10b2e8660effc2cb2a553039bde527e42f1795bc078af6ed2928505be6df1ebe993f2ed8c10477dd5cc9f899d1e69b6512b71c732472dde521f5393c76b2f9fbed668560d4e50ca177dd14b923414549d688b20fab94dba7cad7b5a729941c772dc4a1db79b0e6a111d2d2e8998b4e2a272dc940a9dd4cf856faa5a2ee0cb6f36f0ce6edbb421697e517a4d589cc5a880eecf6fbf65e5f6a1a437b06e5ff9a"
cipernum=int(cipher,16)
message=pow(cipernum,d,c1)
print(message)
```
I then convert the message from decimal to hexadecimal using an online converter
![image](https://github.com/user-attachments/assets/553987ee-61eb-4bf1-bf84-df1ceada44d2)

and then finally I convert this hexadecimal number to text using another online tool
![image](https://github.com/user-attachments/assets/76c42989-a0e7-4672-b6a8-b3eeb45d3af8)

## Incorrect Tangents
- used the default sqrt function only for it to overflow
- got p and q with a negative sign since I kind of messed up the b value for the quadratic, hence multiplying them with -1 to fix that
## Concepts Learnt
- Mathematical Manipulation to undo cryptography

#
