# 1. JoyDivision

> 

## Solution:

I load up the ELF file inside Dogbolt decompiler explorer.

<img width="603" height="521" alt="image-2" src="https://github.com/user-attachments/assets/e9efe0ce-29d9-4c36-8247-defda16c1302" />

I try to figure out what main function does :  
it prints a welcome message, reads the packed data from palatinepackflag.txt, and then applies a four-step transformation. first it calls flipBits to modify the data, then calls the expand function three times in a row where the data from previous operation is carried on for next. finally, transformed data is writen to console and flag.txt.

<img width="485" height="455" alt="image" src="https://github.com/user-attachments/assets/e35d9acf-5f3f-4b6a-a5e9-7bd6ee7a6291" />

the function expand doubles the data size by splitting each byte into two 4-bit nibbles and shuffling them with key nibbles to create two new bytes.

<img width="394" height="398" alt="image-1" src="https://github.com/user-attachments/assets/d3cba238-4eff-461a-a9a4-fadf9beaa39b" />

the function flipBit modifies data by alternating a bitwise NOT on even bytes and a XOR with a rolling key on odd bytes.  

below is the python script which undoes that operation.  

```py
data = open("flag.txt", "rb").read()

def undo_expand(s):
    out = bytearray()
    for i in range(len(s) // 2):
        if i % 2 == 0:
            out.append((s[2*i] & 0x0F) | (s[2*i+1] & 0xF0))  # Extract lower nibble from first, upper from second
        else:
            out.append((s[2*i] & 0xF0) | (s[2*i+1] & 0x0F))  # Extract upper nibble from first, lower from second
    return out

data = undo_expand(data)  # First expansion reversal
data = undo_expand(data)  # Second expansion reversal
data = undo_expand(data)  # Third expansion reversal

key = 105
for i in range(len(data)):
    if i % 2 == 0:
        data[i] = ~data[i] & 0xFF  # Reverse NOT operation on even indices
    else:
        data[i] ^= key & 0xFF  # Reverse XOR with key on odd indices
        key = (key + 32) & 0xFF  # Increment key for next odd index

print(data.decode('utf-8'))
```

<img width="644" height="79" alt="image-3" src="https://github.com/user-attachments/assets/f6da4c68-162f-4071-b394-229fdb7bb0fd" />

## Flag:

```
sunshine{C3A5ER_CR055ED_TH3_RUB1C0N}
```

## Concepts learnt:

learnt to reverse engineer a "packer". identified its core logic that it flipBits (NOT/XOR) cipher and a three-stage expand (nibble-shuffling) function.

***
