
# 1. GDB baby step 1

> Can you figure out what is in the eax register at the end of the main function? Put your answer in the picoCTF flag format: picoCTF{n} where n is the contents of the eax register in the decimal number base. If the answer was 0x11 your flag would be picoCTF{17}.
Disassemble this.



## Solution:



```
abirbhav@ROG-STRIX-G713IE:/mnt/c/WINDOWS/system32$ cd /mnt/c/Users/pc/Desktop
abirbhav@ROG-STRIX-G713IE:/mnt/c/Users/pc/Desktop$ gdb ./debugger0_a
Command 'gdb' not found, but can be installed with:
sudo apt install gdb
abirbhav@ROG-STRIX-G713IE:/mnt/c/Users/pc/Desktop$ sudo apt install gdb
[sudo] password for abirbhav:
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following additional packages will be installed:
  libbabeltrace1 libboost-regex1.74.0 libc6-dbg libdebuginfod-common libdebuginfod1 libipt2 libsource-highlight-common
  libsource-highlight4v5
Suggested packages:
  gdb-doc gdbserver
The following NEW packages will be installed:
  gdb libbabeltrace1 libboost-regex1.74.0 libc6-dbg libdebuginfod-common libdebuginfod1 libipt2
  libsource-highlight-common libsource-highlight4v5
0 upgraded, 9 newly installed, 0 to remove and 32 not upgraded.
Need to get 18.8 MB of archives.
After this operation, 35.1 MB of additional disk space will be used.
Do you want to continue? [Y/n] y
Get:1 http://archive.ubuntu.com/ubuntu jammy-updates/main amd64 libdebuginfod-common all 0.186-1ubuntu0.1 [7996 B]
Get:2 http://archive.ubuntu.com/ubuntu jammy/main amd64 libbabeltrace1 amd64 1.5.8-2build1 [160 kB]
Get:3 http://archive.ubuntu.com/ubuntu jammy-updates/main amd64 libdebuginfod1 amd64 0.186-1ubuntu0.1 [12.8 kB]
Get:4 http://archive.ubuntu.com/ubuntu jammy/main amd64 libipt2 amd64 2.0.5-1 [46.4 kB]
Get:5 http://archive.ubuntu.com/ubuntu jammy/main amd64 libsource-highlight-common all 3.1.9-4.1build2 [64.5 kB]
Get:6 http://archive.ubuntu.com/ubuntu jammy/main amd64 libboost-regex1.74.0 amd64 1.74.0-14ubuntu3 [511 kB]
Ign:6 http://archive.ubuntu.com/ubuntu jammy/main amd64 libboost-regex1.74.0 amd64 1.74.0-14ubuntu3
Get:7 http://archive.ubuntu.com/ubuntu jammy/main amd64 libsource-highlight4v5 amd64 3.1.9-4.1build2 [207 kB]
Get:8 http://archive.ubuntu.com/ubuntu jammy-updates/main amd64 gdb amd64 12.1-0ubuntu1~22.04.2 [3920 kB]
Get:9 http://archive.ubuntu.com/ubuntu jammy-updates/main amd64 libc6-dbg amd64 2.35-0ubuntu3.11 [13.9 MB]
Get:6 http://archive.ubuntu.com/ubuntu jammy/main amd64 libboost-regex1.74.0 amd64 1.74.0-14ubuntu3 [511 kB]
Fetched 18.3 MB in 1min 2s (297 kB/s)
Preconfiguring packages ...
Selecting previously unselected package libdebuginfod-common.
(Reading database ... 79042 files and directories currently installed.)
Preparing to unpack .../0-libdebuginfod-common_0.186-1ubuntu0.1_all.deb ...
Unpacking libdebuginfod-common (0.186-1ubuntu0.1) ...
Selecting previously unselected package libbabeltrace1:amd64.
Preparing to unpack .../1-libbabeltrace1_1.5.8-2build1_amd64.deb ...
Unpacking libbabeltrace1:amd64 (1.5.8-2build1) ...
Selecting previously unselected package libdebuginfod1:amd64.
Preparing to unpack .../2-libdebuginfod1_0.186-1ubuntu0.1_amd64.deb ...
Unpacking libdebuginfod1:amd64 (0.186-1ubuntu0.1) ...
Selecting previously unselected package libipt2.
Preparing to unpack .../3-libipt2_2.0.5-1_amd64.deb ...
Unpacking libipt2 (2.0.5-1) ...
Selecting previously unselected package libsource-highlight-common.
Preparing to unpack .../4-libsource-highlight-common_3.1.9-4.1build2_all.deb ...
Unpacking libsource-highlight-common (3.1.9-4.1build2) ...
Selecting previously unselected package libboost-regex1.74.0:amd64.
Preparing to unpack .../5-libboost-regex1.74.0_1.74.0-14ubuntu3_amd64.deb ...
Unpacking libboost-regex1.74.0:amd64 (1.74.0-14ubuntu3) ...
Selecting previously unselected package libsource-highlight4v5.
Preparing to unpack .../6-libsource-highlight4v5_3.1.9-4.1build2_amd64.deb ...
Unpacking libsource-highlight4v5 (3.1.9-4.1build2) ...
Selecting previously unselected package gdb.
Preparing to unpack .../7-gdb_12.1-0ubuntu1~22.04.2_amd64.deb ...
Unpacking gdb (12.1-0ubuntu1~22.04.2) ...
Selecting previously unselected package libc6-dbg:amd64.
Preparing to unpack .../8-libc6-dbg_2.35-0ubuntu3.11_amd64.deb ...
Unpacking libc6-dbg:amd64 (2.35-0ubuntu3.11) ...
Setting up libdebuginfod-common (0.186-1ubuntu0.1) ...

Creating config file /etc/profile.d/debuginfod.sh with new version

Creating config file /etc/profile.d/debuginfod.csh with new version
Setting up libdebuginfod1:amd64 (0.186-1ubuntu0.1) ...
Setting up libsource-highlight-common (3.1.9-4.1build2) ...
Setting up libc6-dbg:amd64 (2.35-0ubuntu3.11) ...
Setting up libboost-regex1.74.0:amd64 (1.74.0-14ubuntu3) ...
Setting up libipt2 (2.0.5-1) ...
Setting up libbabeltrace1:amd64 (1.5.8-2build1) ...
Setting up libsource-highlight4v5 (3.1.9-4.1build2) ...
Setting up gdb (12.1-0ubuntu1~22.04.2) ...
Processing triggers for man-db (2.10.2-1) ...
Processing triggers for libc-bin (2.35-0ubuntu3.11) ...
abirbhav@ROG-STRIX-G713IE:/mnt/c/Users/pc/Desktop$ gdb ./debugger0_a
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
Reading symbols from ./debugger0_a...
(No debugging symbols found in ./debugger0_a)
(gdb) disassemble main
Dump of assembler code for function main:
   0x0000000000001129 <+0>:     endbr64
   0x000000000000112d <+4>:     push   %rbp
   0x000000000000112e <+5>:     mov    %rsp,%rbp
   0x0000000000001131 <+8>:     mov    %edi,-0x4(%rbp)
   0x0000000000001134 <+11>:    mov    %rsi,-0x10(%rbp)
   0x0000000000001138 <+15>:    mov    $0x86342,%eax
   0x000000000000113d <+20>:    pop    %rbp
   0x000000000000113e <+21>:    ret
End of assembler dump.
(gdb)
```

## Flag:

```
picoCTF{549698}
```

## Concepts learnt:

learnt to disassemble a file using GDB 

## Notes:

this one was pretty straight forward. only mistake I did was I thought this was a windows executable so I tried GDB in CMD but it didnt work, so next I used WSL GDB where it gave the output.

## Resources:

[GDB usage tutorial](https://visualgdb.com/gdbreference/commands/disassemble)


***

# 2. ARMssembly 1

> For what argument does this program print `win` with variables 58, 2 and 3? File: chall_1.S Flag format: picoCTF{XXXXXXXX} -> (hex, lowercase, no 0x, and 32 bits. ex. 5614267 would be picoCTF{0055aabb})



## Solution:

![assembly to C](image.png)

Since I do not understand assembly at all, I first used a online converter to turn the assembly code into C, then I understood how it works : 
The C program has two functions, func and main. func does some bitwise operation to finally attain the value 77 stored in temp. The function then subtracts input value x from 77 and returns this result to main and hence prints I win. So in main if the result equals zero (meaning the input value was 77), the program prints “You win!”, otherwise it prints “You Lose :(” . Then I finally convert 77 to hex, lowercase, no 0x, and 32 bits form for the flag.
```
#include <stdio.h>
#include <stdlib.h>

int func(int x) {
    int a = 58;
    int b = 2;
    int c = 3;
    int temp = a << b;
    temp = temp / c;
    temp = temp - x;
    return temp;
}

int main(int argc, char **argv) {
    int val = atoi(argv[1]);
    int result = func(val);
    if (result == 0) {
        puts("You win!");
    } else {
        puts("You Lose :(");
    }
    return 0;
}
```

## Flag:

```
picoCTF{0000004d}
```

## Concepts learnt:

from this I learnt about how to convert code of one language to another, bitwise shifting, integer division, etc

## Notes:

tried to decompile using Ghidra but found it rather complicated so relied on an online tool which worked fine.

## Resources:

[ASSEMBLY to C](https://www.codeconvert.ai/assembly-to-c-converter?id=789f1119-b4ce-4b4a-b2ad-ef2f21c749d5)


***

# 3. Vault door 3

> This vault uses for-loops and byte arrays. The source code for this vault is here: VaultDoor3.java



## Solution:

First off, I started with converting JAVA to C, to understand the code better.  
```
#include <stdio.h>
#include <string.h>
#include <stdbool.h>

bool checkPassword(const char *password) {
    if (strlen(password) != 32) {
        return false;
    }
    char buffer[33]; // +1 for null terminator
    int i;

    for (i = 0; i < 8; i++) {
        buffer[i] = password[i];
    }
    for (; i < 16; i++) {
        buffer[i] = password[23 - i];
    }
    for (; i < 32; i += 2) {
        buffer[i] = password[46 - i];
    }
    for (i = 31; i >= 17; i -= 2) {
        buffer[i] = password[i];
    }
    buffer[32] = '\0';

    return strcmp(buffer, "jU5t_a_sna_3lpm12g94c_u_4_m7ra41") == 0;
}

int main() {
    char userInput[100];
    printf("Enter vault password: ");
    if (scanf("%99s", userInput) != 1) {
        printf("Input error.\n");
        return 1;
    }

    // Extract substring from userInput starting after "picoCTF{" and excluding the last character
    const char *prefix = "picoCTF{";
    size_t prefix_len = strlen(prefix);
    size_t input_len = strlen(userInput);

    if (input_len <= prefix_len + 1) {
        printf("Access denied!\n");
        return 0;
    }

    char input[100];
    size_t sub_len = input_len - prefix_len - 1;
    strncpy(input, userInput + prefix_len, sub_len);
    input[sub_len] = '\0';

    if (checkPassword(input)) {
        printf("Access granted.\n");
    } else {
        printf("Access denied!\n");
    }

    return 0;
}

```
![alt text](image-2.png)

The converted C code works like : for each character we type into the input, it compares it with a hardcoded string w.r.t its index. It also enforces that the password length is exactly 32 characters, orelse it would return false. Now the password obtaining logic works as follows -  
i) for first 8 characters we have to copy the hardcoded string directly  
ii) for next 8 characters it takes characters from 23-i of the string  
iii) for next 8 *even* characters from and including 16 to including 30 fills as 46-i  
iv) for every other remaining index running in decending order now from 31 to 17 are directly filled in (like the first 8 case)  


Now, finally by applying the operations above we receive the final string which is `jU5t_a_s1mpl3_an4gr4m_4_u_c79a21`  

![alt text](image-1.png)
## Flag:

```
picoCTF{jU5t_a_s1mpl3_an4gr4m_4_u_c79a21}
```

## Concepts learnt:

learnt to convert JAVA to C for personal simplicity. also learnt to have the patience to go through each individual iteration and figure out what it does lol and then finally create the required answer...

## Notes:

None

## Resources:

[JAVA to C](https://www.codeconvert.ai/free-converter?id=b2c5c5f0-f9b3-4c20-b9a7-d8924f99c441)


***
