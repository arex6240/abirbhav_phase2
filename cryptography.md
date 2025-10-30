# 1. RSA oracle

> An attacker was able to intercept communications between a bank and a fintech company. They managed to get the message (ciphertext) and the password that was used to encrypt the message.
After some intensive reconassainance they found out that the bank has an oracle that was used to encrypt the password and can be found here nc titan.picoctf.net 52731. Decrypt the password and use it to decrypt the message. The oracle can decrypt anything except the password.

## Solution:



## Flag:

```

```

## Concepts learnt:



## Notes:



## Resources:



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

