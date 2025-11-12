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

# 2. worthy.knight

> 

## Solution:

Putting the worthy elf into a decompiler I get the following code inside the main function  
```c
__int64 __fastcall main(int a1, char **a2, char **a3)
{
  const unsigned __int16 *v3; // rsi
  char *v4; // rax
  unsigned __int16 v5; // cx
  unsigned __int16 v6; // dx
  unsigned int v7; // ebp
  char *v9; // r12
  size_t v10; // rax
  char *v11; // rbp
  int v12; // edx
  char *v13; // rdi
  char v14[2]; // [rsp+Ch] [rbp-10Ch] BYREF
  char v15; // [rsp+Eh] [rbp-10Ah]
  char v16[16]; // [rsp+10h] [rbp-108h] BYREF
  char s1[32]; // [rsp+20h] [rbp-F8h] BYREF
  char v18; // [rsp+40h] [rbp-D8h] BYREF
  char s[16]; // [rsp+50h] [rbp-C8h] BYREF
  __int128 v20; // [rsp+60h] [rbp-B8h]
  __int128 v21; // [rsp+70h] [rbp-A8h]
  __int128 v22; // [rsp+80h] [rbp-98h]
  __int128 v23; // [rsp+90h] [rbp-88h]
  __int128 v24; // [rsp+A0h] [rbp-78h]
  __int128 v25; // [rsp+B0h] [rbp-68h]
  __int128 v26; // [rsp+C0h] [rbp-58h]
  unsigned __int64 v27; // [rsp+D8h] [rbp-40h]

  v27 = __readfsqword(0x28u);
  puts(
    "                       (Knight's Adventure)                \n"
    "\n"
    "         O                                              \n"
    "        <M>            .---.                            \n"
    "        /W\\           ( -.- )--------.                  \n"
    "   ^    \\|/            \\_o_/         )    ^             \n"
    "  /|\\    |     *      ~~~~~~~       /    /|\\            \n"
    "  / \\   / \\  /|\\                    /    / \\            \n"
    "~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\n"
    "Welcome, traveler. A mighty dragon blocks the gate.\n"
    "Speak the secret incantation (10 runic letters) to continue.\n");
  *(_OWORD *)s = 0;
  v20 = 0;
  v21 = 0;
  v22 = 0;
  v23 = 0;
  v24 = 0;
  v25 = 0;
  v26 = 0;
  printf("Enter your incantation: ");
  if ( !fgets(s, 128, stdin) )
  {
    puts("\nSomething went awry. Fare thee well...");
    return 1;
  }
  s[strcspn(s, "\n")] = 0;
  if ( strlen(s) != 10 )
  {
    puts("\nScribe's note: The incantation must be exactly 10 runic symbols.");
    puts(aTheAncientDrag);
    return 1;
  }
  v3 = *__ctype_b_loc();
  v4 = s;
  do
  {
    v5 = v3[(unsigned __int8)*v4];
    if ( (v5 & 0x400) == 0 || (v6 = v3[(unsigned __int8)v4[1]], (v6 & 0x400) == 0) )
    {
      puts("\nThe runes fail to align. The incantation is impure.");
      puts(aTheAncientDrag);
      return 1;
    }
    if ( (v5 & 0x100) != 0 && (v6 & 0x100) != 0 || (v5 & 0x200) != 0 && (v6 & 0x200) != 0 )
    {
      puts("\nThe ancient seals do not resonate with your runes.");
      puts(aTheAncientDrag);
      return 1;
    }
    v4 += 2;
  }
  while ( v4 != &s[10] );
  if ( (s[0] ^ s[1]) != 36 )
  {
    puts("\nThe wards reject your Pair 1.");
    puts(aTheAncientDrag);
    return 1;
  }
  if ( s[1] != 106 )
  {
    puts("\nThe wards reject your Pair 1 second char.");
    puts(aTheAncientDrag);
    return 1;
  }
  if ( (s[3] ^ s[2]) != 56 )
  {
    puts("\nThe wards reject your Pair 2.");
    puts(aTheAncientDrag);
    return 1;
  }
  if ( s[3] != 83 )
  {
    puts("\nThe wards reject your Pair 2 second char.");
    puts(aTheAncientDrag);
    return 1;
  }
  v15 = 0;
  v9 = v16;
  *(_WORD *)v14 = __ROL2__(*(_WORD *)&s[4], 8);
  v10 = strlen(v14);
  v11 = s1;
  MD5(v14, v10, v16);
  do
  {
    v12 = (unsigned __int8)*v9;
    v13 = v11;
    v11 += 2;
    ++v9;
    sprintf(v13, "%02x", v12);
  }
  while ( &v18 != v11 );
  v18 = 0;
  v7 = strcmp(s1, "33a3192ba92b5a4803c9a9ed70ea5a9c");
  if ( v7 )
  {
    puts("\nThe dragon's eyes glow red... The final seal remains locked.");
    puts(aTheAncientDrag);
    return 1;
  }
  if ( (s[7] ^ s[6]) != 56 )
  {
    puts("\nThe wards reject your Pair 4.");
    puts(aTheAncientDrag);
    return 1;
  }
  if ( s[7] != 97 )
  {
    puts("\nThe wards reject your Pair 4 second char.");
    puts(aTheAncientDrag);
    return 1;
  }
  if ( (s[8] ^ s[9]) != 32 )
  {
    puts("\nThe wards reject your Pair 5.");
    puts(aTheAncientDrag);
    return 1;
  }
  if ( s[9] != 105 )
  {
    puts("\nThe wards reject your Pair 5 second char.");
    puts(aTheAncientDrag);
    return 1;
  }
  printf(
    "\n%s\n",
    "   The kingdom's gates open, revealing the hidden realm...    \n"
    "                         ( (                                 \n"
    "                          \\ \\                                \n"
    "                     .--.  ) ) .--.                         \n"
    "                    (    )/_/ (    )                        \n"
    "                     '--'      '--'                         \n"
    "    \"Huzzah! Thy incantation is true. Onward, brave knight!\" \n");
  printf("The final scroll reveals your reward: KCTF{%s}\n\n", s);
  return v7;
}
// 10B0: using guessed type __int64 __fastcall MD5(_QWORD, _QWORD, _QWORD);
```

understanding this code what it technically does is categories the 10 characters into pairs of 2. then it comments about each individual pair one by one which in a way is a clue to unlock the "incantation". 
```c
if ( (v5 & 0x400) == 0 || (v6 = v3[(unsigned __int8)v4[1]], (v6 & 0x400) == 0) )
    {
      puts("\nThe runes fail to align. The incantation is impure.");
      puts(aTheAncientDrag);
      return 1;
    }
```
Above snippet means that both characters in a pair must be alphabetic.
```c
if ( (v5 & 0x100) != 0 && (v6 & 0x100) != 0 || (v5 & 0x200) != 0 && (v6 & 0x200) != 0 )
    {
      puts("\nThe ancient seals do not resonate with your runes.");
      puts(aTheAncientDrag);
      return 1;
    }
```
Above snippet means that there should be both upper and lower case in each pair.  

Now,  

`if ( s[1] != 106 )` -> s[1] must be 'j' (ASCII value 106)

`if ( (s[0] ^ s[1]) != 36 )` this would mean `s[0] ^ 'j' == 36`. So we use ascii value of j and apply bitwise xor operation. `s[0] = 36 ^ 106` therefore `s[0] = 78` i.e 'N'

therefore pair 1 -> Nj

`if ( s[3] != 83 )` -> s[3] must be S (ASCII 83)

`if ( (s[3] ^ s[2]) != 56 )` applying similar logic as above we get `s[2] = 107` i.e 'k'.

therefore pair 2 -> kS

`if ( s[5] != 84 )` -> s[5] must be 'T' (ASCII value 84)

`*(_WORD *)v14 = __ROL2__(*(_WORD *)&s[4], 8);` this means the bytes s[4] and s[5] are swapped before hashing. hence The md5 hash of s[5]+s[4] must be "33a3192ba92b5a4803c9a9ed70ea5a9c". Brute-forcing, we find s[4] = 'f' (102), s[5] = 'T' (84).

therefore pair 3 -> fT

`if ( s[7] != 97 )` -> s[7] must be 'a' (ASCII value 97)

`if ( (s[7] ^ s[6]) != 56 )` so s[6] ^ 97 == 56, so s[6] = 56 ^ 97 = 89, which is 'Y'.

therefore pair 4 -> Ya

`if ( s[9] != 105 )` -> s[9] must be 'i' (ASCII value 105)

`if ( (s[8] ^ s[9]) != 32 )` so s[8] ^ 105 == 32, so s[8] = 32 ^ 105 = 73, which is 'I'.

therefore pair 5 -> Ii

so the final incantation is `NjkSfTYaIi`

<img width="883" height="469" alt="image" src="https://github.com/user-attachments/assets/93af941b-1700-4003-a476-e49aa605edaa" />


## Flag:

```
KCTF{NjkSfTYaIi}
```

## Concepts learnt:

learnt to trace the main function for the flag

## Resources:

https://dogbolt.org/?id=99490316-af5f-4ae8-8dde-33045e08d271#Hex-Rays=64


***

# 3. time

> 

## Solution:
I load up the program in decompiler to see whats there. Then I find two useful functions from there `main()` and `giveFlag()` 

```c
int __fastcall main(int argc, const char **argv, const char **envp)
{
  unsigned int v3; // eax
  int v5; // [rsp+10h] [rbp-10h] BYREF
  int v6; // [rsp+14h] [rbp-Ch]
  unsigned __int64 v7; // [rsp+18h] [rbp-8h]

  v7 = __readfsqword(0x28u);
  v3 = time(0);
  srand(v3);
  v6 = rand();
  puts("Welcome to the number guessing game!");
  puts("I'm thinking of a number. Can you guess it?");
  puts("Guess right and you get a flag!");
  printf("Enter your number: ");
  fflush(stdout);
  __isoc99_scanf("%u", &v5);
  printf("Your guess was %u.\n", v5);
  printf("Looking for %u.\n", v6);
  fflush(stdout);
  if ( v6 == v5 )
  {
    puts("You won. Guess was right! Here's your flag:");
    giveFlag();
  }
  else
  {
    puts("Sorry. Try again, wrong guess!");
  }
  fflush(stdout);
  return 0;
}
```
```c
unsigned __int64 giveFlag()
{
  FILE *stream; // [rsp+8h] [rbp-118h]
  char s[264]; // [rsp+10h] [rbp-110h] BYREF
  unsigned __int64 v3; // [rsp+118h] [rbp-8h]

  v3 = __readfsqword(0x28u);
  memset(s, 0, 0x100u);
  stream = fopen("/home/h3/flag.txt", "r");
  if ( stream )
  {
    fgets(s, 256, stream);
    fclose(stream);
    puts(s);
  }
  else
  {
    puts("Flag file not found!  Contact an H3 admin for assistance.");
  }
  return __readfsqword(0x28u) ^ v3;
}
```

after I analyse the code, i see that the code is using a flawed method for its "random" number generation, that is, the rand() function is seeded with srand(time(0)) and then rand() is called immediately to get the secret number. This make that secret number predictable.

The function time(0) returns the number of seconds since the Unix epoch. now if I run my own program that also calls srand(time(0)) and then rand() within the same second, I get the exact same "secret" number.

I write a exploit C program:  

```c
#include <stdio.h>   
#include <stdlib.h>  
#include <time.h>

int main() {
  srand(time(NULL)); 

  int secret_number = rand();

  printf("%u\n", secret_number);

  return 0;
}
```

I then compile the C program using gcc inside WSL (since the target file, time is ELF (linux env)). Then I give executable permission to both files using chmod +x.

Then after I run:  

<img width="1053" height="565" alt="image" src="https://github.com/user-attachments/assets/8ab9cee4-3ae0-412f-908e-697aaae51bfc" />


Hence I get the flag.



## Flag:

```

```

## Concepts learnt:

learned how predictable random number generation using srand(time(0)) and rand() can be exploited. By replicating the seeding and random call timing i got the secret no. Also got a revision about piping outputs.

## Resources:

https://dogbolt.org/?id=ab5d7224-ce7d-49c3-99e0-bdc807cf4d52#Hex-Rays=137

***
