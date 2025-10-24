<img width="1367" height="934" alt="Screenshot 2025-10-25 021601" src="https://github.com/user-attachments/assets/cfd748ac-45bd-4dfc-b7bf-b525635ee820" /># 1. Trivial Flag Transfer Protocol

> Figure out how they moved the flag.

## Solution:

First, I unload the file inside wireshark 4.4.2  

<img width="1919" height="1023" alt="Screenshot 2025-10-24 234253" src="https://github.com/user-attachments/assets/39d15889-3c52-4b74-878f-660b75b07fc7" />

I exported the TFTP object list next 

<img width="741" height="543" alt="Screenshot 2025-10-25 003105" src="https://github.com/user-attachments/assets/3b3e12be-94dc-4375-b5d2-ccf3cc78ab16" />


inside it I find instruction.txt and plan, both of which are ciphers which when diciphered translates to 

TFTPDOESNTENCRYPTOURTRAFFICSOWEMUSTDISGUISEOURFLAGTRANSFER.FIGUREOUTAWAYTOHIDETHEFLAGANDIWILLCHECKBACKFORTHEPLAN  
which can be simplified to :  
TFTP DOESNT ENCRYPT OUR TRAFFIC SO WE MUST DISGUISE OUR FLAG TRANSFER. FIGURE OUT A WAY TO HIDE THE FLAG AND I WILL CHECK BACK FOR THE PLAN

IUSEDTHEPROGRAMANDHIDITWITH-DUEDILIGENCE.CHECKOUTTHEPHOTOS  
which can be simplified to :
I USED THE PROGRAM AND HID IT WITH - DUEDILIGENCE. CHECK OUT THE PHOTOS

I then explored inside `program.deb` using WinRAR. 

<img width="785" height="403" alt="Screenshot 2025-10-25 004729" src="https://github.com/user-attachments/assets/5280df17-9ed0-42f6-bdf2-105d088bb978" />

<img width="1103" height="721" alt="Screenshot 2025-10-25 004738" src="https://github.com/user-attachments/assets/eb593e3a-1fb1-4826-92b2-4a80812ae22f" />

I noticed that steghide was used here which was likely used to hide data inside those 3 images. so I run steghide on each individual image using the passphrase `DUEDILIGENCE` (as it was diciphered earlier). Then I finally receive the flag

```
abirbhav@ROG-STRIX-G713IE:~$ which steghide
/usr/bin/steghide
abirbhav@ROG-STRIX-G713IE:~$ cd /mnt/c/Users/pc/Desktop/tftp obj
-bash: cd: too many arguments
abirbhav@ROG-STRIX-G713IE:~$ cd "/mnt/c/Users/pc/Desktop/tftp obj"
abirbhav@ROG-STRIX-G713IE:/mnt/c/Users/pc/Desktop/tftp obj$ steghide extract -sf picture1.bmp
Enter passphrase:
steghide: could not extract any data with that passphrase!
abirbhav@ROG-STRIX-G713IE:/mnt/c/Users/pc/Desktop/tftp obj$ steghide extract -sf picture2.bmp
Enter passphrase:
abirbhav@ROG-STRIX-G713IE:/mnt/c/Users/pc/Desktop/tftp obj$
abirbhav@ROG-STRIX-G713IE:/mnt/c/Users/pc/Desktop/tftp obj$ steghide extract -sf picture2.bmp
Enter passphrase:
steghide: could not extract any data with that passphrase!
abirbhav@ROG-STRIX-G713IE:/mnt/c/Users/pc/Desktop/tftp obj$ steghide extract -sf picture3.bmp
Enter passphrase:
wrote extracted data to "flag.txt".
abirbhav@ROG-STRIX-G713IE:/mnt/c/Users/pc/Desktop/tftp obj$ cat flag.txt
picoCTF{h1dd3n_1n_pLa1n_51GHT_18375919}
abirbhav@ROG-STRIX-G713IE:/mnt/c/Users/pc/Desktop/tftp obj$
```


## Flag:

```
picoCTF{h1dd3n_1n_pLa1n_51GHT_18375919}
```

## Concepts learnt:

learnt about wireshark and how can it be used to trace packets. Also learnt about a powerful new tool steghide.


## Notes:

trying to find data/ fragments of data inside each packet has been a waste of time. exporting idea came much later in my head when I reseached more. since I didnt have linux, it took a me a while to figure out how to explore program.deb but at the end WinRAR the GOAT helped. It also took me quite a few permutations to figure out the passphrase like was it DUEDILIGENCE or DUE DILIGENCE or duediligence etc etc...

## Resources:

[steghide tutorial](https://www.geeksforgeeks.org/installation-guide/how-to-install-steghide-tool-in-linux/)  
[steghide download](https://sourceforge.net/projects/steghide/)  
[cipher cracker](https://www.dcode.fr/en)  
[wireshark 4.4.2](https://www.wireshark.org/download.html)  


***

# 2. tunn3l v1s10n

> We found this file. Recover the flag.

## Solution:

Given file has no extension attached with it. So i use hexed.it to quickly know the file type which is windows bitmap (BMP File).
<Screenshot 2025-10-25<img width="1046" height="892" alt="Screenshot 2025-10-25 013347" src="https://github.com/user-attachments/assets/b0afc2e9-16c2-414c-b6e8-0bc9d5f523c6" />

Even after adding the extension the file wasnt opening, so I went to check out its hex again, and compared its hex side by side with 3 other bmp files with correct hex. Here I compared the fields which seemed to be commonly same for the working bmp files but broken for the test file. So I accordingly change the fields to obtain a bmp file which DOES open but still has some issues.

<Screenshot 2025-10<img width="681" height="277" alt="Screenshot 2025-10-25 014937" src="https://github.com/user-attachments/assets/0a499740-0461-4057-89ed-2d8daa2e2487" />
broken hex

<Screenshot 2025-10-2<img width="689" height="318" alt="Screenshot 2025-10-25 014943" src="https://github.com/user-attachments/assets/97136946-48ec-4f5e-8cb6-72f4a88fe27b" />
ref1

<Screenshot 2025<img width="692" height="353" alt="Screenshot 2025-10-25 014948" src="https://github.com/user-attachments/assets/f438ddaa-a8c5-4876-ab00-297bebd29ea2" />
ref2

<Screenshot<img width="673" height="335" alt="Screenshot 2025-10-25 014958" src="https://github.com/user-attachments/assets/d35e1b06-439a-4f0a-894d-de8902b38428" />
ref3

<Screenshot 2025-10-2<img width="678" height="350" alt="Screenshot 2025-10-25 015032" src="https://github.com/user-attachments/assets/d3e1a6b8-d682-46d0-837b-45ea3fee2cdf" />

fixed hex

finally I get this image : 

<img width="1818" height="484" alt="Screenshot 2025-10-25 015816" src="https://github.com/user-attachments/assets/88640007-4252-4060-8d0c-933c54a8aad3" />
-3.png)

since this is a decoy there was furthermore to be done. This image looks like it has been cropped so I will try to uncrop the image

```
abirbhav@ROG-STRIX-G713IE:/mnt/c/Users/pc/Desktop$ exiftool tunn3l_v1s10n.bmp
ExifTool Version Number         : 12.40
File Name                       : tunn3l_v1s10n.bmp
Directory                       : .
File Size                       : 2.8 MiB
File Modification Date/Time     : 2025:10:25 01:49:05+05:30
File Access Date/Time           : 2025:10:25 02:03:19+05:30
File Inode Change Date/Time     : 2025:10:25 02:02:59+05:30
File Permissions                : -rwxrwxrwx
File Type                       : BMP
File Type Extension             : bmp
MIME Type                       : image/bmp
BMP Version                     : Windows V3
Image Width                     : 1134
Image Height                    : 306
Planes                          : 1
Bit Depth                       : 24
Compression                     : None
Image Length                    : 2893400
Pixels Per Meter X              : 5669
Pixels Per Meter Y              : 5669
Num Colors                      : Use BitDepth
Num Important Colors            : All
Image Size                      : 1134x306
Megapixels                      : 0.347
```

image height is 306 (which is 0x132 in hex) and width is 1134 (which is 0x46E in hex). So next I find where these values are in hexeditor

 <img width="600" height="169" alt="Screenshot 2025-10-25 020903" src="https://github.com/user-attachments/assets/e881edf3-dac5-46f1-beae-32113c556f3b" />

here are the hex values for the parameters (last 2 characters are coming before the earlier ones)

so now I play with 0x132 (which controls the height to see at what height more information is revealed.)

after multiple trial and errors at around 850 height (which in hex is 0x352) we get the final image.

<img width="1367" height="934" alt="Screenshot 2025-10-25 021601" src="https://github.com/user-attachments/assets/7c940f5c-601f-490d-a714-2ec4edbff8ca" />


## Flag:

```
picoCTF{qu1t3_a_v13w_2020}
```

## Concepts learnt:

usage of hex editor to compare and fix files and also change the characteristics of the file directly.

## Notes:

Made alot of mistakes while trying to fix the hex values, so have to be more thorough with that in the future

## Resources:

[hexed.it](https://hexed.it/)
Google Gemini (for learning purposes only)


***

# 3. m00nwalk

> Decode this message from the moon.

## Solution:

- Include as many steps as you can with your thought process
- You **must** include images such as screenshots wherever relevant.

```
put codes & terminal outputs here using triple backticks

you may also use ```python for python codes for example
```

## Flag:

```
picoCTF{}
```

## Concepts learnt:

- Include the new topics you've come across and explain them in brief
- 

## Notes:

- Include any alternate tangents you went on while solving the challenge, including mistakes & other solutions you found.
- 

## Resources:

- Include the resources you've referred to with links. [example hyperlink](https://google.com)


***
