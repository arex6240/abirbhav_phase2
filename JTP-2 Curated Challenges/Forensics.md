# 1. Hide and seek

> Description:
Sakamoto’s at it again with a game of hide and seek, but this time, it’s not with Shin or his daughter. An old friend hid some secret data in this image. Can you find it before the others do?
Hint: Even in retirement, Sakamoto never loses at hide and seek. Maybe stegseek can help you keep up.

## Solution:

in the description itself it's mentioned to use stegseek. So I do the same. 

I load up stegseek in WSL and download `rockyou.txt`, a file having the collection of all possible weak passwords. stegseek bruteforces those passwords and finally we get the flag.

  <img width="1051" height="176" alt="image" src="https://github.com/user-attachments/assets/a5fc98a6-6538-4ec4-b64c-f7816be071ea" />


## Flag:

```
nite{h1d3_4nd_s33k_but_w1th_st3g_sdfu9s8}
```

## Concepts learnt:

learnt how to extract hidden data using stegseek which extracted the information using the possible passwords from `rockyou.txt` (all the common passwords in the world)


## Notes:
--

## Resources:

https://github.com/brannondorsey/naive-hashcat/releases/download/data/rockyou.txt

***

# 2. 

> Description:
One of my favorite foods is soya chunks. But as I was enjoying some Nutrela today, I noticed a few chunks weren’t quite right. Seems like something’s off with their structure. Could you help me fix these broken chunks so I can enjoy my meal again?

## Solution:
Since the description talks alot about chunks so I get a hint that I had to work with chunks.

I load up the file in hexed, where I see the header part as:  
<img width="678" height="826" alt="image" src="https://github.com/user-attachments/assets/e0224d59-5a20-41f6-85be-e2084ee66014" />

and footer part as:  
<img width="678" height="308" alt="image" src="https://github.com/user-attachments/assets/09e3f189-2319-4c11-ae30-fb9646309eae" />


on running `pngcheck` I notice that the 4 key parts of the file is wrong, which are the header (png has to be capital), ihdr (has to be caps), idat (has to be caps), and iend (caps).
Fixing these key issues made the file readable and I get the flag.  
<img width="611" height="102" alt="image" src="https://github.com/user-attachments/assets/a57e447c-73fe-490a-8147-5f1eb76f1451" />

<img width="586" height="70" alt="image" src="https://github.com/user-attachments/assets/dbc27af8-b68f-4185-aae4-b2a9a116fa84" />


```
abirbhav@ROG-STRIX-G713IE:/mnt/f/abirbhav_phase2/challs/forensics/2 Nutrela Chunks$ pngcheck -v nutrela.png
File: nutrela.png (538662 bytes)
  this is neither a PNG or JNG image nor a MNG stream
ERRORS DETECTED in nutrela.png
abirbhav@ROG-STRIX-G713IE:/mnt/f/abirbhav_phase2/challs/forensics/2 Nutrela Chunks$ pngcheck -v nutrela2.png
File: nutrela2.png (538662 bytes)
  chunk ihdr at offset 0x0000c, length 13:  first chunk must be IHDR
ERRORS DETECTED in nutrela2.png
abirbhav@ROG-STRIX-G713IE:/mnt/f/abirbhav_phase2/challs/forensics/2 Nutrela Chunks$ pngcheck -v nutrela2.png
File: nutrela2.png (538662 bytes)
  chunk IHDR at offset 0x0000c, length 13
    1000 x 1000 image, 24-bit RGB, non-interlaced
  chunk idat at offset 0x00025, length 538605:  illegal reserved-bit-set chunk
ERRORS DETECTED in nutrela2.png
abirbhav@ROG-STRIX-G713IE:/mnt/f/abirbhav_phase2/challs/forensics/2 Nutrela Chunks$ pngcheck -v nutrela2.png
File: nutrela2.png (538662 bytes)
  chunk IHDR at offset 0x0000c, length 13
    1000 x 1000 image, 24-bit RGB, non-interlaced
  chunk IDAT at offset 0x00025, length 538605
    zlib: deflated, 32K window, default compression
  chunk iend at offset 0x8381e, length 0:  illegal reserved-bit-set chunk
ERRORS DETECTED in nutrela2.png
abirbhav@ROG-STRIX-G713IE:/mnt/f/abirbhav_phase2/challs/forensics/2 Nutrela Chunks$ pngcheck -v nutrela2.png
File: nutrela2.png (538662 bytes)
  chunk IHDR at offset 0x0000c, length 13
    1000 x 1000 image, 24-bit RGB, non-interlaced
  chunk IDAT at offset 0x00025, length 538605
    zlib: deflated, 32K window, default compression
  chunk IEND at offset 0x8381e, length 0
No errors detected in nutrela2.png (3 chunks, 82.0% compression).
```

<img width="1000" height="1000" alt="image" src="https://github.com/user-attachments/assets/d640d0de-b7bc-4996-99b6-402eae1a4e79" />


## Flag:

```
nite{n0w_y0u_kn0w_ab0ut_PNG_chunk5}
```

## Concepts learnt:

learnt the criticality of IHDR, IDAT, IEND chunks and also about its case sensitivity.


***
