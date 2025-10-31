# 1. RSA oracle

> An attacker was able to intercept communications between a bank and a fintech company. They managed to get the message (ciphertext) and the password that was used to encrypt the message.
After some intensive reconassainance they found out that the bank has an oracle that was used to encrypt the password and can be found here nc titan.picoctf.net 53380. Decrypt the password and use it to decrypt the message. The oracle can decrypt anything except the password.

## Solution:

I first connect to nc titan.picoctf.net 53380 to see whats there  

<img width="703" height="118" alt="image" src="https://github.com/user-attachments/assets/0bca39a7-3570-4903-bc17-7c118bab274c" />


so first I tried putting the contents of password.enc into the decrypt part.

<img width="1086" height="195" alt="image" src="https://github.com/user-attachments/assets/73e2da24-e3a5-49e8-a9db-7b6fb9d5050e" />


As we see it didnt work.


I noticed that the encryption was using RSA (evident from the name of the challenge), and I could get encrypted values for small numbers like 1, 2, 3, and 4. so I wrote a simple script to collect these pairs. When the program encrypts a character like "1", it converts it to its ASCII hex value (0x31 = 49 in decimal), then encrypts it. Using the formula c = m^e mod N, I know that c - m^e should be a multiple of N and hence by taking the GCD of these differences, I can find N 
```
abirbhav@ROG-STRIX-G713IE:/mnt/c/WINDOWS/system32$ nc titan.picoctf.net 53380
*****************************************
****************THE ORACLE***************
*****************************************
what should we do for you?
E --> encrypt D --> decrypt.
E
enter text to encrypt (encoded length must be less than keysize): 1
1

encoded cleartext as Hex m: 31

ciphertext (m ^ e mod n) 4374671741411819653095065203638363839705760144524191633605358134684143978321095859047126585649272872908765432040943055399247499744070371810470682366100689

what should we do for you?
E --> encrypt D --> decrypt.
E
enter text to encrypt (encoded length must be less than keysize): 2
2

encoded cleartext as Hex m: 32

ciphertext (m ^ e mod n) 4707619883686427763240856106433203231481313994680729548861877810439954027216515481620077982254465432294427487895036699854948548980054737181231034760249505

what should we do for you?
E --> encrypt D --> decrypt.
E
enter text to encrypt (encoded length must be less than keysize): 3
3

encoded cleartext as Hex m: 33

ciphertext (m ^ e mod n) 1998517197048216725617978890728205902760633363770165103499700157925986170022682604311921651991344892635565706489644418147980643978563559991322776155635395

what should we do for you?
E --> encrypt D --> decrypt.
E
enter text to encrypt (encoded length must be less than keysize): 4
4

encoded cleartext as Hex m: 34

ciphertext (m ^ e mod n) 3993239489061277327472930109138093827255646312769901312414509207541733524779884801267968848884701166599834406248783129646083261476137481855550108336137485

what should we do for you?
E --> encrypt D --> decrypt.
```
the code I used to calculate N:

```
import math

pairs = [(49, 4374671741411819653095065203638363839705760144524191633605358134684143978321095859047126585649272872908765432040943055399247499744070371810470682366100689),
         (50, 4707619883686427763240856106433203231481313994680729548861877810439954027216515481620077982254465432294427487895036699854948548980054737181231034760249505),
         (51, 1998517197048216725617978890728205902760633363770165103499700157925986170022682604311921651991344892635565706489644418147980643978563559991322776155635395),
         (52, 3993239489061277327472930109138093827255646312769901312414509207541733524779884801267968848884701166599834406248783129646083261476137481855550108336137485)]

e = 65537
N = 0
for a, b in pairs:
    diff = b - pow(a, e)
    if N == 0:
        N = diff
    else:
        N = math.gcd(N, diff)
print("Value of N is:", N)
```
```
Value of N is: 5507598452356422225755194020880876452588463543445995226287547479009566151786764261801368190219042978883834809435145954028371516656752643743433517325277971
```

After running this, I got the value of N. Now since RSA is multiplicative, I could multiply the encrypted password by an encrypted value of 10, ask the program to decrypt it, and then divide the result by 10 to get the original password. The key property of RSA is: `Enc(m1) × Enc(m2) = Enc(m1 × m2) mod N`. Since I know N and e (which is always 65537 for RSA), I can calculate Enc(10) myself: `Enc(10) = 10^e mod N = 10^65537 mod N`and Then I multiply: `Enc(password) × Enc(10) = Enc(password × 10) mod N`

I did this in Python:

```
password = 4228273471152570993857755209040611143227336245190875847649142807501848960847851973658239485570030833999780269457000091948785164374915942471027917017922546
x = 10

enc_x = pow(x, e, N)

enc = (password * enc_x) % N
print(enc)
```
```
2246069766871706352813845635896283550427369688733816925667491485440565630208989627729225212984783125979682554685302094776744105380218282706364056056744223
```
I sent this value to the program for decryption and got a hex string back.
```
Enter text to decrypt: 2246069766871706352813845635896283550427369688733816925667491485440565630208989627729225212984783125979682554685302094776744105380218282706364056056744223
decrypted ciphertext as hex (c ^ d mod n): 21de3fe13e0
decrypted ciphertext: !ÞÿáþÀ
```

I converted it to an integer, then divided by 10 using the modular inverse to get the password:

```
temp_hex = "21de3fe13e0"
p2 = int(temp_hex, 16)
inv = pow(x, -1, N)
real = (p2 * inv) % N
k = (N.bit_length() + 7) // 8
password_bytes = real.to_bytes(k, 'big')
password = password_bytes.lstrip(b'\x00').decode()
print(password)
```
This gave me the password `60f50`, which I used to decrypt the secret file with OpenSSL:

```
openssl enc -aes-256-cbc -d -in secret.enc -pass pass:60f50
```
<img width="1018" height="86" alt="image" src="https://github.com/user-attachments/assets/5bc2816f-e223-4603-81a6-cd3b7bb3c3bd" />


And I get the flag

## Flag:

```
picoCTF{su((3ss_(r@ck1ng_r3@_60f50766}
```

## Concepts learnt:

learnt how to exploit RSA's multiplicative property to recover an encrypted password and decrypt a protected message. I also learned to reconstruct the RSA modulus using GCD from plaintext ciphertext pairs. and alot of python coding :/

## Resources:

[Breaking RSA - Computerphile](https://youtu.be/-ShwJqAalOk)  
https://crypto.stackexchange.com/questions/24880/how-multiplicative-property-of-rsa-can-be-exploited

***

# 2. Custom encryption

> Can you get sense of this code file and write the function that will decode the given encrypted file content.
Find the encrypted file here flag_info and code file might be good to analyze and get the flag.

## Solution:

Encryption first reverses the message, then XORs each character with a repeating text key (“trudeau”), and finally multiplies each resulting character’s code by shared_key×311 (where the shared_key is derived from a small Diffie–Hellman step with p=97, g=31). Decryption just undoes those steps in reverse that is it divides each number in the cipher list by shared_key×311 to recover the XORed‑reversed text, then XORs that text again with the same key to remove the XOR, and finally reverses the string back to get the original message.

```
import sys

def generator(g, x, p):
    return pow(g, x) % p

def is_prime(p):
    v = 0
    for i in range(2, p + 1):
        if p % i == 0:
            v = v + 1
    if v > 1:
        return False
    else:
        return True
    
def decrypt(cipher, key):
    txt=[]
    for i in cipher:
        
        x=311*key
        y=round(i//x)
        
        txt.append(chr(y))
    return "".join(txt)

def dynamic_xor_decrypt(plaintext,text_key): 
    cipher_text = ""
    key_length = len(text_key)
    for i, char in enumerate(plaintext):
        key_char = text_key[i % key_length]
        encrypted_char = chr(ord(char) ^ ord(key_char))
        cipher_text += encrypted_char
    return cipher_text[::-1]

def test(a,b):
    p = 97
    g = 31
    if not is_prime(p) and not is_prime(g):
        print("Enter prime numbers")
        return
    print(f"a = {a}")
    print(f"b = {b}")
    u = generator(g, a, p)
    v = generator(g, b, p)
    key = generator(v, a, p) 
    b_key = generator(u, b, p)
    shared_key = None
    if key == b_key:
        shared_key = key
    else:
        print("Invalid key")
        return
    return shared_key

if __name__ == "__main__":  
    a=88
    b=26
    cipher=[97965, 185045, 740180, 946995, 1012305, 21770, 827260, 751065, 718410, 457170, 0, 903455, 228585, 54425, 740180, 0, 239470, 936110, 10885, 674870, 261240, 293895, 65310, 65310, 185045, 65310, 283010, 555135, 348320, 533365, 283010, 76195, 130620, 185045]
    shared_key=test(a,b)
    text=decrypt(cipher,shared_key)
    flag=dynamic_xor_decrypt(text,"trudeau")
    print(flag)
```

```
PS C:\Users\pc\Downloads> python custom_decryption.py
a = 88
b = 26
picoCTF{custom_d2cr0pt6d_019c831c}
```
## Flag:

```
picoCTF{custom_d2cr0pt6d_019c831c}
```

## Concepts learnt:

learnt to analyse a python program, what are the ways in which encryption here is done and how to reverse the encryption to get the flag 


## Notes:

--

## Resources:

GPT-5 (learning purposes only)

***

# 3. miniRSA

> Let's decrypt this: ciphertext? Something seems a bit small.

## Solution:

On opening the `ciphertext`, I see the following content : 
```
N: 29331922499794985782735976045591164936683059380558950386560160105740343201513369939006307531165922708949619162698623675349030430859547825708994708321803705309459438099340427770580064400911431856656901982789948285309956111848686906152664473350940486507451771223435835260168971210087470894448460745593956840586530527915802541450092946574694809584880896601317519794442862977471129319781313161842056501715040555964011899589002863730868679527184420789010551475067862907739054966183120621407246398518098981106431219207697870293412176440482900183550467375190239898455201170831410460483829448603477361305838743852756938687673
e: 3

ciphertext (c): 2205316413931134031074603746928247799030155221252519872650073010782049179856976080512716237308882294226369300412719995904064931819531456392957957122459640736424089744772221933500860936331459280832211445548332429338572369823704784625368933 
```

Generally for larger e, we use `m = c^d mod N`. Here we dont have d (private key). But since `e = 3` is tiny, it makes RSA vulnerable to attacks like "small exponent" or "low public exponent" attacks. So directly we get that `c = m^3` and therefore `m = c^(1/3)`. Calcuting m, we get that  
m = `13016382529449106065894479374027604750406953699090365388203708028670029596145277`
Now convert m to bytes : `m.to_bytes(byte_length, 'big')` we get `m = b'picoCTF{n33d_a_lArg3r_e_ccaa7776}'`

```
import math

N = 29331922499794985782735976045591164936683059380558950386560160105740343201513369939006307531165922708949619162698623675349030430859547825708994708321803705309459438099340427770580064400911431856656901982789948285309956111848686906152664473350940486507451771223435835260168971210087470894448460745593956840586530527915802541450092946574694809584880896601317519794442862977471129319781313161842056501715040555964011899589002863730868679527184420789010551475067862907739054966183120621407246398518098981106431219207697870293412176440482900183550467375190239898455201170831410460483829448603477361305838743852756938687673
e = 3
c = 2205316413931134031074603746928247799030155221252519872650073010782049179856976080512716237308882294226369300412719995904064931819531456392957957122459640736424089744772221933500860936331459280832211445548332429338572369823704784625368933 

def cube_root(n):
    low, high = 1, n
    while low < high:
        mid = (low + high + 1) // 2
        if mid**3 <= n:
            low = mid
        else:
            high = mid - 1
    return low

m = cube_root(c)


plaintext = m.to_bytes((m.bit_length() + 7) // 8, 'big').decode()
print(plaintext)
```

OUTPUT: `picoCTF{n33d_a_lArg3r_e_ccaa7776}`
## Flag:

```
picoCTF{n33d_a_lArg3r_e_ccaa7776}
```

## Concepts learnt:

learnt about RSA encryptions and its vulnerabilities like how it can become weak if small value of e is used and how to also exploit it if thats the case. 

## Notes:

--  

## Resources:

[RSA cryptosystem](https://en.wikipedia.org/wiki/RSA_cryptosystem)  
[Python bit functions on int (bit_length, to_bytes and from_bytes)](https://www.geeksforgeeks.org/python/python-bit-functions-on-int-bit_length-to_bytes-and-from_bytes/)

***

