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

I loaded up the program in a decompiler to see what's there. I found the `main()` function which shows how the program works:

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

After analyzing the code, I see that the program uses `srand(time(0))` to seed the random number generator, then immediately calls `rand()` to get the secret number. This is predictable because `time(0)` returns the number of seconds since Unix epoch.

The  pathway should be to use GDB to read the random number from memory or a register before we provide our input.

I started GDB and set a breakpoint at `__isoc99_scanf`:

```bash
$ gdb ./time
(gdb) break __isoc99_scanf
Breakpoint 1 at 0x400780
(gdb) run
```

When the breakpoint hits (right before scanf asks for input), the random number `v6` is already stored in memory at `[rbp-0xC]`. I read it:

```bash
(gdb) up
#1  0x00000000004009d2 in main ()
(gdb) x/d $rbp-0xc
0x7fffffffdd84: 1804289383
(gdb) continue
```

Then I entered the number I just read (1804289383), and got the success message:

```
Your guess was 1804289383.
Looking for 1804289383.
You won. Guess was right! Here's your flag:
Flag file not found!  Contact an H3 admin for assistance.
```


## Alternate Solution:
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

# 4. VeridisQuo.apk

> 

## Solution:

I start by examining the source code of the provided APK file. In the `MainActivity.java` file, I noted that the application immediately calls a `cleanUp()` method from `Utilities.java` and sets the text to "Too slow!!".

```java
package byuctf.downwiththefrench;

import android.os.Bundle;
import android.widget.TextView;
import androidx.appcompat.app.AppCompatActivity;

public class MainActivity extends AppCompatActivity {
    /* access modifiers changed from: protected */
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        new Utilities(this).cleanUp();
        ((TextView) findViewById(R.id.homeText)).setText("Too slow!!");
    }
}
```

The `Utilities.java` file showed that `cleanUp()` clears the text of 28 TextViews (`flagPart1` to `flagPart28`).

```java
package byuctf.downwiththefrench;

import android.app.Activity;
import android.widget.TextView;

public class Utilities {
    private Activity activity;

    public Utilities(Activity activity2) {
        this.activity = activity2;
    }

    public void cleanUp() {
        ((TextView) this.activity.findViewById(R.id.flagPart1)).setText("");
        ((TextView) this.activity.findViewById(R.id.flagPart2)).setText("");
        ((TextView) this.activity.findViewById(R.id.flagPart3)).setText("");
        ((TextView) this.activity.findViewById(R.id.flagPart4)).setText("");
        ((TextView) this.activity.findViewById(R.id.flagPart5)).setText("");
        ((TextView) this.activity.findViewById(R.id.flagPart6)).setText("");
        ((TextView) this.activity.findViewById(R.id.flagPart7)).setText("");
        ((TextView) this.activity.findViewById(R.id.flagPart8)).setText("");
        ((TextView) this.activity.findViewById(R.id.flagPart9)).setText("");
        ((TextView) this.activity.findViewById(R.id.flagPart10)).setText("");
        ((TextView) this.activity.findViewById(R.id.flagPart11)).setText("");
        ((TextView) this.activity.findViewById(R.id.flagPart12)).setText("");
        ((TextView) this.activity.findViewById(R.id.flagPart13)).setText("");
        ((TextView) this.activity.findViewById(R.id.flagPart14)).setText("");
        ((TextView) this.activity.findViewById(R.id.flagPart15)).setText("");
        ((TextView) this.activity.findViewById(R.id.flagPart16)).setText("");
        ((TextView) this.activity.findViewById(R.id.flagPart17)).setText("");
        ((TextView) this.activity.findViewById(R.id.flagPart18)).setText("");
        ((TextView) this.activity.findViewById(R.id.flagPart19)).setText("");
        ((TextView) this.activity.findViewById(R.id.flagPart20)).setText("");
        ((TextView) this.activity.findViewById(R.id.flagPart21)).setText("");
        ((TextView) this.activity.findViewById(R.id.flagPart22)).setText("");
        ((TextView) this.activity.findViewById(R.id.flagPart23)).setText("");
        ((TextView) this.activity.findViewById(R.id.flagPart24)).setText("");
        ((TextView) this.activity.findViewById(R.id.flagPart25)).setText("");
        ((TextView) this.activity.findViewById(R.id.flagPart26)).setText("");
        ((TextView) this.activity.findViewById(R.id.flagPart27)).setText("");
        ((TextView) this.activity.findViewById(R.id.flagPart28)).setText("");
    }
}

```

then I note that the flag characters were present in the layout XML file (`res/layout/activity_main.xml`) before being cleared at runtime, so I inspected the XML file and found that each `TextView` contained a single character of the flag. but the main issue was that the IDs were not in order ,e.g., `flagPart1` was `}`. so it can be like that flagPart1 might be the last character...

Now, I note that the characters were positioned using `layout_marginBottom` for Y-axis and `layout_marginEnd` for X-axis. then I compared them I noticed the characters naturally formed two shapes,i.e, a vertical column and a horizontal row. The ones with higher X-values were stacked top to bottom, so I sort those by their Y-coordinate. The rest were spread left to right, so I sort them by their X-coordinate.

I wrote a Python script to reconstruct the flag based on the visual layout of the characters. It had consisted of two parts:
A vertical column on the left (sorted by Y-coordinate).
A horizontal row on the right (sorted by X-coordinate).  

```python
flag_parts = [
    # (id, marginEnd, marginBottom, text)
    (1, 216, 420, "}"),
    (2, 340, 616, "t"),
    (3, 332, 556, "a"),
    (4, 368, 676, "y"),
    (5, 252, 500, "c"),
    (6, 348, 636, "c"),
    (7, 364, 436, "d"),
    (8, 348, 496, "r"),
    (9, 336, 536, "n"),
    (10, 360, 456, "i"),
    (11, 276, 536, "0"),
    (12, 340, 516, "d"),
    (13, 232, 460, "k"),
    (14, 356, 656, "u"),
    (15, 320, 452, "p"),
    (16, 352, 476, "o"),
    (17, 300, 500, "c"),
    (18, 332, 596, "f"),
    (19, 308, 484, "e"),
    (20, 328, 436, "_"),
    (21, 292, 516, "e"),
    (22, 284, 536, "_"),
    (23, 268, 536, "f"),
    (24, 316, 468, "i"),
    (25, 260, 516, "_"),
    (26, 240, 480, "4"),
    (27, 224, 440, "e"),
    (28, 324, 576, "{"),
]

vertical = [p for p in flag_parts if p[1] >= 324]
horizontal = [p for p in flag_parts if p[1] < 324]

vertical_sorted = sorted(vertical, key=lambda x: -x[2])

horizontal_sorted = sorted(horizontal, key=lambda x: -x[1])

# 'b' comes from homeText
flag = "b" + "".join([p[3] for p in vertical_sorted]) + "".join([p[3] for p in horizontal_sorted])
print(flag)
```

Running the script produced the flag.

<img width="718" height="80" alt="image" src="https://github.com/user-attachments/assets/9f0e1e24-4b54-494e-8c13-92bf0f5a05d2" />

## Flag:

```
byuctf{android_piece_0f_c4ke}
```

## Concepts learnt:

understood how android layouts works and how code interacts with UI elements and extracting information directly from resource files without running the app. 

## Notes:

initially I constructed the flag with using the literally positions referred by the flagpart, but then on checking that didnt make any sense and flags normally do make sense, so I dived deeper into how to unscramle the text.
I chose 324 as the split because all vertical chars had marginEnd ≥ 324, while every horizontal one was clearly below it.



## Resources:

(https://developer.android.com/develop/ui/views/layout/declaring-layout)  

***

# 5. Dusty

## Solution:

### dust_noob
The `dust_noob` binary contains a `shinyclean::main` function that initializes a byte array and XORs it with `0x3F`. By replicating this logic in a py script, we can decrypt the flag.

```python
v3 = [0] * 23
initial_str = "{^HX|kyDym"
for i, c in enumerate(initial_str):
    v3[i] = ord(c)

v3[10] = 12
v3[11] = 12
v3[12] = 96
v3[13] = 124
v3[14] = 11
v3[15] = 109
v3[16] = 96
v3[17] = 104
v3[18] = 11
v3[19] = 10
v3[20] = 119
v3[21] = 30
v3.append(0x42) # v4[0] is 'B'

s = []
for x in v3:
    s.append(chr(x ^ 0x3F))

print("".join(s))
```
<img width="821" height="45" alt="image" src="https://github.com/user-attachments/assets/7ef57292-41c2-4847-b3c4-d04d6f4a2bed" />

### dust_intermediate
The `dust_intermediate` binary hides its password check in a tricky way.
The program has two parts running at the same time (threads). When I type a character, the main part sends it to a worker thread. The worker thread keeps track of a running total and uses it to look up a value in a special lookup table called an S-box (which has 256 numbers in it). It then checks if that value matches what it expects.
To solve this, I found the S-box data in the binary file (at position `0x61298`) and the list of expected values from the code. Then I reverse the process like for each expected value, we found where it appears in the S-box, compared it to the previous position, and that difference gave us the original character i need to type.

```python
import sys

def read_bytes(filepath, offset, length):
    with open(filepath, 'rb') as f:
        f.seek(offset)
        data = f.read(length)
        return list(data)

offset = 0x61298
s_box = read_bytes('dust_intermediate', offset, 256)

# Create inverse S-box
s_inv = [0] * 256
for i, x in enumerate(s_box):
    s_inv[x] = i

target_bytes = [
    0xEA, 0xD9, 0x31, 0x22, 0xD3, 0xE6, 0x97, 0x70,
    0x16, 0xA2, 0xA8, 0x1B, 0x61, 0xFC, 0x76, 0x68,
    0x7B, 0xCB, 0xB8, 0x27, 0x96
]

v12_prev = 117
flag = []

for b in target_bytes:
    v12 = s_inv[b]
    c = (v12 - v12_prev) % 256
    flag.append(chr(c))
    v12_prev = v12

print("".join(flag))
```
<img width="892" height="482" alt="image" src="https://github.com/user-attachments/assets/bbe95a20-0208-457d-af92-fb163b68ea8b" />

### dust_pro
The `dust_pro` binary asks us to enter a number. It then takes that number and breaks it down into 4 bytes. These 4 bytes become a key that repeats over and over to decrypt a hidden message using XOR.

At first, I thought I'd have to try to bruteforce, But then I realized that since CTF flags always start with `DawgCTF{` (like the previous flags), so I knew the decrypted message had to start with "Dawg". So if the encrypted bytes XOR the key gives me "Dawg", then the encrypted bytes XOR "Dawg" gives me the key

hence I took the first 4 encrypted bytes from the target array and XORed them with "Dawg". This gave me the 4-byte key: `0x8b, 0x68, 0x69, 0xd4`. after I had the key, I just repeated it to decrypt the entire flag.

```python
target = [
    0xcf, 0x9, 0x1e, 0xb3, 0xc8, 0x3c, 0x2f, 0xaf, 
    0xbf, 0x24, 0x25, 0x8b, 0xd9, 0x3d, 0x5c, 0xe3, 
    0xd4, 0x26, 0x59, 0x8b, 0xc8, 0x5c, 0x3b, 0xf5, 
    0xf6
]

key = [0x8b, 0x68, 0x69, 0xd4]

flag = []
for i, b in enumerate(target):
    flag.append(chr(b ^ key[i % 4]))

print("".join(flag))
```
<img width="820" height="44" alt="image" src="https://github.com/user-attachments/assets/4b9ed91c-2b7f-4dd8-96fc-733f053d1e36" />


## Flag:

```
DawgCTF{FR33_C4R_W45H!?}
DawgCTF{S0000_CL4?B!}
DawgCTF{4LL_RU57_N0_C4R!}
```

## Concepts learnt:

Spotting and undoing simple tricks like XOR and lookup tables  
Getting secret data out of the program files  
Finding where the program checks for the right answer  
Using what I know about flag formats to make guessing easier  


## Notes:

few deadends I reached are that I tried to Brute-force the flag or key instead of analyzing the code.Searched  for flag strings directly in the binaries with `strings` or `grep`. Looked for hardcoded answers or clues in error messages or unused functions.



## Resources:

Hex-Rays Decompiler  
`objdump`

***
