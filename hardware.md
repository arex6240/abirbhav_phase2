# 1. IQ Test

> let your input x = 30478191278.
wrap your answer with nite{ } for the flag.
As an example, entering x = 34359738368 gives (y0, ..., y11), so the flag would be nite{010000000011}.

## Solution:

Since logic gate inputs can only take either 0 or 1, I first convert the given x into binary: 
so, x = 34359738368 = 11100011000101001000100101010101110

now for the given setup I see 36 inputs, but the given binary representation only has 35 places. So only way to keep the number same is to add a 0 in front of the binary representation. Therefore the final representation for the input will be : `011100011000101001000100101010101110`

now we take this input and solve the logic gate. 

<img width="798" height="1280" alt="image" src="https://github.com/user-attachments/assets/f7bb1868-8290-48ad-b756-ea47f5f00450" />


So the output is 100010011000

## Flag:

```
nite{100010011000}
```

## Concepts learnt:

A lot of patience to solve so many logic gates lol

## Notes:

--

## Resources:

--

***

# 2. I like logic

> i like logic and i like files, apparently, they have something in common, what should my next step be.

## Solution:

Here in the google drive folder we are provided with a `challenge.sal` file. On deeper analysis I got to know that this file can be read by the software `Saleae Logic 2`. After I import the file inside the software I was greeted with a interface like this:  

<img width="1919" height="1028" alt="image" src="https://github.com/user-attachments/assets/86ae6b06-4e34-4502-a137-28e7d295746f" />


Here I notice that only channel which contains a datastream is `D3`. So therefore I run an analyser on that stream, specifically the Async Serial in the following settings: 

<img width="523" height="571" alt="image" src="https://github.com/user-attachments/assets/03ae64b8-4575-45de-b25e-bcbe6c9c8d3d" />


Next I see the terminal for the following output : 

<img width="532" height="781" alt="image" src="https://github.com/user-attachments/assets/555347b3-c559-4102-b71a-3ca5a1e05ccb" />

<img width="517" height="732" alt="image" src="https://github.com/user-attachments/assets/f2521edb-6b81-4a9c-8dc2-0c31bec3a211" />

<img width="507" height="547" alt="image" src="https://github.com/user-attachments/assets/fdce78f6-d6e5-4ee5-b1ec-4f3bd541b047" />


The flag is clearly visible here which is : `FCSC{b1dee4eeadf6c4e60aeb142b0b486344e64b12b40d1046de95c89ba5e23a9925}`

also btw this text is from the novel The Lost World by Sir Arthur Conan Doyle 

## Flag:

```
FCSC{b1dee4eeadf6c4e60aeb142b0b486344e64b12b40d1046de95c89ba5e23a9925}
```

## Concepts learnt:

learnt how to use hardware analysis tools like Saleae Logic 2 to interpret and extract data from logic analyzer files. also learnt a little bit about analyzers like Async Serial, I2C, SPI, etc. 

## Notes:

I was blind that I did not see the terminal icon beside the data table, which was giving me incomplete data again and again so I tried multiple other settings like changing bitrate, different channels etc (i.e wasted alot of time). Lastly saw the terminal icon which gave me the complete data. 

## Resources:

Saleae Logic 2

***

# 3. Bare Metal Alchemist

> my friend recommended me this anime but i think i've heard a wrong name.

## Solution:

On opening the google drive link I found a file named `firmware.elf` from the challenge. Running `file firmware.elf` showed it’s an ELF32 binary for an Atmel AVR microcontroller, so I recognized it as a firmware reverse-engineering task. 
```
abirbhav@ROG-STRIX-G713IE:/mnt/c/Users/pc/Downloads$ file firmware.elf
firmware.elf: ELF 32-bit LSB executable, Atmel AVR 8-bit, version 1 (SYSV), statically linked, with debug_info, not stripped
```
I listed the ELF sections with `avr-objdump -h firmware.elf` and saw sections like .bin, .data, and .text. To inspect embedded strings, I extracted the .bin portion with:
```
dd if=firmware.elf bs=1 skip=$((0x94)) count=$((0x2e4)) of=text_section.bin 2>/dev/null
hexdump -C text_section.bin | sed -n '1,40p'
```
where the output was:  
```
abirbhav@ROG-STRIX-G713IE:/mnt/c/Users/pc/Downloads$ dd if=firmware.elf bs=1 skip=$((0x94)) count=$((0x2e4)) of=text_section.bin 2>/dev/null
hexdump -C text_section.bin | sed -n '1,40p'
00000000  0c 94 4b 00 0c 94 68 00  0c 94 68 00 0c 94 68 00  |..K...h...h...h.|
00000010  0c 94 68 00 0c 94 68 00  0c 94 68 00 0c 94 68 00  |..h...h...h...h.|
00000040  0c 94 71 00 0c 94 68 00  0c 94 68 00 0c 94 68 00  |..q...h...h...h.|
00000050  0c 94 68 00 0c 94 68 00  0c 94 68 00 0c 94 68 00  |..h...h...h...h.|
00000060  0c 94 68 00 0c 94 68 00  f1 e3 e6 e6 f1 e3 de f1  |..h...h.........|
00000070  cd 94 d6 fa 94 d6 fa d6  ca c8 96 fa d6 94 c8 d5  |................|
00000080  c9 96 fa 91 d7 c1 d0 94  cb ca fa c3 94 d7 c8 d2  |................|
00000090  91 d7 c0 d8 00 00 11 24  1f be cf ef d8 e0 de bf  |.......$........|
000000a0  cd bf 11 e0 a0 e0 b1 e0  e4 ee f2 e0 02 c0 05 90  |................|
000000b0  0d 92 ae 34 b1 07 d9 f7  21 e0 ae e4 b1 e0 01 c0  |...4....!.......|
000000c0  1d 92 a7 35 b2 07 e1 f7  0e 94 bb 00 0c 94 70 01  |...5..........p.|
000000d0  0c 94 00 00 8b b1 87 70  8b b9 85 b1 8c 7f 85 b9  |.......p........|
000000e0  08 95 1f 92 0f 92 0f b6  0f 92 11 24 2f 93 3f 93  |...........$/.?.|
000000f0  8f 93 9f 93 af 93 bf 93  80 91 53 01 90 91 54 01  |..........S...T.|
00000100  a0 91 55 01 b0 91 56 01  30 91 52 01 23 e0 23 0f  |..U...V.0.R.#.#.|
00000110  2d 37 58 f5 01 96 a1 1d  b1 1d 20 93 52 01 80 93  |-7X....... .R...|
00000120  53 01 90 93 54 01 a0 93  55 01 b0 93 56 01 80 91  |S...T...U...V...|
00000130  4e 01 90 91 4f 01 a0 91  50 01 b0 91 51 01 01 96  |N...O...P...Q...|
00000140  a1 1d b1 1d 80 93 4e 01  90 93 4f 01 a0 93 50 01  |......N...O...P.|
00000150  b0 93 51 01 bf 91 af 91  9f 91 8f 91 3f 91 2f 91  |..Q.........?./.|
00000160  0f 90 0f be 0f 90 1f 90  18 95 26 e8 23 0f 02 96  |..........&.#...|
00000170  a1 1d b1 1d d2 cf cf 93  df 93 00 d0 cd b7 de b7  |................|
00000180  78 94 84 b5 82 60 84 bd  84 b5 81 60 84 bd 85 b5  |x....`.....`....|
00000190  82 60 85 bd 85 b5 81 60  85 bd 80 91 6e 00 81 60  |.`.....`....n..`|
000001a0  80 93 6e 00 10 92 81 00  80 91 81 00 82 60 80 93  |..n..........`..|
000001b0  81 00 80 91 81 00 81 60  80 93 81 00 80 91 80 00  |.......`........|
000001c0  81 60 80 93 80 00 80 91  b1 00 84 60 80 93 b1 00  |.`.........`....|
000001d0  80 91 b0 00 81 60 80 93  b0 00 80 91 7a 00 84 60  |.....`......z..`|
000001e0  80 93 7a 00 80 91 7a 00  82 60 80 93 7a 00 80 91  |..z...z..`..z...|
000001f0  7a 00 81 60 80 93 7a 00  80 91 7a 00 80 68 80 93  |z..`..z...z..h..|
00000200  7a 00 10 92 c1 00 8a b1  88 6f 8a b9 84 b1 83 60  |z........o.....`|
00000210  84 b9 52 98 8b b1 87 70  8b b9 85 b1 8c 7f 85 b9  |..R....p........|
00000220  95 ea b9 2e 20 e0 c2 2e  20 e0 d2 2e 89 b1 98 2f  |.... ... ....../|
00000230  99 0f 89 27 82 ff 23 c0  88 e6 e8 2e 80 e0 f8 2e  |...'..#.........|
00000240  00 e0 f7 01 84 91 88 23  61 f0 e8 2f eb 25 85 3a  |.......#a../.%.:|
00000250  41 f0 89 b1 98 2f 99 0f  89 27 82 fd 18 c0 0e 94  |A..../...'......|
00000260  6a 00 1a 82 19 82 89 81  9a 81 8c 32 91 40 48 f4  |j..........2.@H.|
00000270  00 00 89 81 9a 81 01 96  9a 83 89 83 f4 cf 0e 94  |................|
00000280  6a 00 c1 14 d1 04 91 f2  0e 94 00 00 cf cf e0 53  |j..............S|
```
`dd` is used to isolate a binary segment from `firmware.elf` and save it as `text_section.bin`   
`hexdump` is used to read the raw binary data from text_section.bin and formats the data into the standard side-by-side hexadecimal and ASCII view (for the first 40 lines).

next I wrote a Python script that reads `200` bytes from `text_section.bin` starting at offset `0x68`, then for each `k` in `0..255` computes `kb = (k*2) & 0xFF` and builds `out = bytes((b ^ 0xA5) ^ kb for b in data)`. It tries to decode the entire `200-byte` out, and if decoding succeeds and the decoded string contains both `{` and `}`, it prints that string and exits the loop.

```
python3 - <<'PY'
try:
    with open('text_section.bin', 'rb') as f:
        buf = f.read()
except FileNotFoundError:
    print("text_section.bin not present.")
    raise SystemExit(1)

data = buf[0x68:0x68+200]

if 0 in data:
    data = data[:data.index(0)]

for k in range(256):
    kb = (k * 2) & 0xFF
    out = bytes(((b ^ 0xA5) ^ kb) for b in data)
    try:
        s = out.decode()
    except UnicodeDecodeError:
        continue
    
    if '{' in s and '}' in s:
        print(s)
        break
else:
    print("No flag")
```
Therefore I get the flag

<img width="633" height="537" alt="image" src="https://github.com/user-attachments/assets/9fd5ad4a-417e-4357-a7fa-305036539314" />


## Flag:

```
TFCCTF{Th1s_1s_som3_s1mpl3_4rdu1no_f1rmw4re}
```

## Concepts learnt:

learnt the basics of data encoding/decoding and got a practical intro to microcontroller reverse engineering and reading Arduino memory layouts and digging through `.hex`/`.bin` dumps to extract useful info. Also learnt about how python can be used in such scenarios.


## Resources:

https://hexed.it/  
https://github.com/NationalSecurityAgency/ghidra
