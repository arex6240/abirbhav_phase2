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

# 2. Nutrela Chunks

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

# 3. RAR of the Abyss

> Description:
Two philosophers peer into the networked abyss and swap a secret. Use the secret to decrypt the Abyss’ RAwR and pull your flag from the void.

## Solution:

Loaded up the `abyss.pcap` inside wireshark to see its contents. Since the name of the challenge contains the word RAR, I assumed that something has to be linked with RAR. So next I try opening the file with WinRAR, which when I do it asked me for a password. hmmmm interesting....

<img width="334" height="374" alt="image" src="https://github.com/user-attachments/assets/f8b32d5e-1bca-4c0a-a74f-64e59ce4ab3e" />


Next I go on a hunt for the password. 
I cat into the file to get something like this 

```
abirbhav@ROG-STRIX-G713IE:/mnt/f/abirbhav_phase2/challs/forensics/3 RAR of the Abyss$ cat abyss.pcap
�ò������h�55������]٧E'@c�

[�pingrequest���h�33������]٧E%@c�

�[pingreply���hZ66������]٧E(@f�


�       P��P �y���h�66������]٧E(@f�


P�      ����P ����he66������]٧E(@f�


�       P����P ����h�qq������]٧Ec@fw


�       P����P ��Camus: One must imagine Sisyphus happy but are we happy ?
���h�66������]٧E(@f�


P�      ���-P U���hi66������]٧E(@f�


�       P�-��P T���h�66������]٧E(@f�


P�      ���.P T���hG66������]٧E(@f�


�
P��P �w���h�66������]٧E(@f�


P�
����P ����h␦66������]٧E(@f�


�
P����P ����h�qq������]٧Ec@fw


�
P����P �Nietzsche: You will be happy after reading my latest work
���hK66������]٧E(@f�


P�
���.P R���h�66������]٧E(@f�


�
P�.��P Q���h,66������]٧E(@f�


P�
���/P Q���h@TT������]٧EF@e�


�
 P�O�tP �Rar!␦����!dE7�'�a�U��u�gC��f�Cܣ�_R�wOdp��&���ka*�sG����ѐqxr��]�_���o�
                                                                              ᖲ�[*���V�DP��k�T�w~�ǖ�����1���:o:��KOd�q�9���Z��.��|�aY�R��[�q8����}��A�!\�E�7L!�|��/�����ک))�{ja�ڝ��t���=����
                                           ���Aѝ%
                                                 X�������ϧ�y��k�q2�ģ�Ku��v
                                                                          g���\y{׽���R�J���hU66������]٧E(@f�


�
 P��P �s���h66������]٧E(@f�


P�
  ����P ����h466������]٧E(@f�


�
 P����P ����h�SS������]٧EE@f�


�
 P����P F0Camus: whats the password ?
���hP66������]٧E(@f�


P�
  ���P j���h�66������]٧E(@f�


�
 P���P i���h%66������]٧E(@f�


P�
  ���P i���h�66������]٧E(@f�


P��P �q���h�66������]٧E(@f�


����P ����h|66������]٧E(@f�


P����P ����hTT������]٧EF@f�


P����P u�Nietzsche: b3y0ndG00dand3vil
���h�66������]٧E(@f�


���P f���h66������]٧E(@f�


P���P e���h�66������]٧E(@f�


���P e���h66������]٧E(@f�


�P��P �o���hT66������]٧E(@f�


P�����P ����h�66������]٧E(@f�


�P����P ����h'EE������]٧E7@f�


�P����P j
Camus: thanks
���h66������]٧E(@f�


P����P r���hJ66������]٧E(@f�


�P���P q���h�66������]٧E(@f�


P����P q���h+**������B�

���**B�B�
       B�

B�
���hr55������]٧E'@c�

[pingrequest���h33������]٧E%@c�

        �pingreply���hP[[^��]٧EM@ފ�
����ul9��SSDP: M-SEARCH * HTTP/1.1
Man: "ssdp:discover"
���hPP������]٧EB@ޕ�����
lu.     HTTP/1.1 200 OK
ST: upnp:rootdevice
���h�[[^��]٧EM@މ�
                 ����vl9��SSDP: M-SEARCH * HTTP/1.1
Man: "ssdp:discover"
���hCPP������]٧EB@ޔ�����
                        lv.HTTP/1.1 200 OK
ST: upnp:rootdevice
���h**������B�
            B�



���hM**B�
         B�
B�


5�L%�CDNS_RESP:host12.local:1.2.3.4���h55������]٧E'@c�

pingrequest���ha33������]٧E%@c�
(
pingreply���h�[[^��]٧EM@ޅ�����zl9��SSDP: M-SEARCH * HTTP/1.1
Man: "ssdp:discover"
���hMPP������]٧EB@ސ�����lz.�HTTP/1.1 200 OK
ST: upnp:rootdevice
���h**������B�

���h�**B�B�
B�
���h55������]٧E'@c�

[�pingrequest���h�33������]٧E%@c�
1
�Kpingreply���h@@������]٧E2@��Q5�pDNS_QUERY:host17.local���h�GG������]٧E9��5�Q%�4DNS_RESP:host17.local:1.2.3.4���h+55������]٧E'@c�

[�pingrequest���hv33������]٧E%@c�
7
�Ipingreply���h�**������B�

���h'**B�B�
B�
���h�@@������]٧E2@��T5�nDNS_QUERY:host20.local���h5GG������]٧E9��5�T%+DNS_RESP:host20.local:1.2.3.4���h@@������]٧E2@��U5�jDNS_QUERY:host21.local���hlGG������]٧E9��5�U%�'DNS_RESP:host21.local:1.2.3.4���h
**������B�

���hk**B���
�
���h@@������]٧E2@��W5�bDNS_QUERY:host23.local���hjGG������]٧E9��5�W%�!DNS_RESP:host23.local:1.2.3.4���h**������B�

���hf**B�B�
B�
���h55������]٧E'@cp
␦
[�pingrequest���h<33������]٧E%@cr
L
␦�Bpingreply���[[^��]٧EM@�y�����l9��SSDP: M-SEARCH * HTTP/1.1
Man: "ssdp:discover"
���h�PP������]٧EB@ބ������.�HTTP/1.1 200 OK
ST: upnp:rootdevice
���h'55������]٧E'@ch

[�ingrequest���hg33������]٧E%@cj
R
�@ingreply���h�**������B�

"���h**B�BB�!
"B�
���h�55������]٧E'@c`

[�pingrequest���hc33������]٧E%@cb
X
�>pingreply���h�55������]٧E'@c\

[�pingrequest���h�33������]٧E%@c^
[
�=pingreply���h@@������]٧E2@�� �_5�LDNS_QUERY:host31.local���hGG������]٧E9 �� 5�_%DNS_RESP:host31.local:1.2.3.4���h**������BB�
!
&���h�**B� BB�%
&B�
!���hW**������BB�!
"
'���h�**B�!BB�&
'B�!
"���h%55������]٧E'@cL
#
[�"pingrequest���h33������]٧E%@cN
g
#�9"pingreply���ht[[^��]٧EM@�p�$�����l9��SSDP: M-SEARCH * HTTP/1.1
Man: "ssdp:discover"
���h:PP������]٧EB@�{�����$l�.�HTTP/1.1 200 OK
ST: upnp:rootdevice
���h�**������BB�$
%
*���hi**B�$BB�)
*B�$
%���h�[[^��]٧EM@�n�&�����l9��SSDP: M-SEARCH * HTTP/1.1
Man: "ssdp:discover"
���hqPP������]٧EB@�y�����&l�.�HTTP/1.1 200 OK
ST: upnp:rootdevice
���h@@������]٧E2@��'�f5�0DNS_QUERY:host38.local���h�GG������]٧E9'��'5�f%��DNS_RESP:host38.local:1.2.3.4���h�**������BB�'
(
-���h�**B�'BB�,
-B�'
(���h[[[^��]٧EM@�k�)�����l9��SSDP: M-SEARCH * HTTP/1.1
Man: "ssdp:discover"
���hPP������]٧EB@�v�����)l�.�HTTP/1.1 200 OK
ST: upnp:rootdevice
���h�@@������]٧E2@��*�i5�.DNS_QUERY:host41.local���hfGG������]٧E9*��*5�i%��DNS_RESP:host41.local:1.2.3.4
```

from the conversation it is clearly visible (unencrypted) that the password is `b3y0ndG00dand3vil`. I put this password for WinRAR next to find the flag inside it. lesssgoo

<img width="778" height="407" alt="image" src="https://github.com/user-attachments/assets/341a3354-ba0c-4b68-949f-e68260c448af" />


## Flag:

```
nite{thus_sp0k3_th3_n3tw0rk_f0r3ns1cs_4n4lyst}
```

## Concepts learnt:

opening pcap file with WinRAR for the flag. Hunting for the password in the conversation. 

## Notes:

--

## Resources:

--

***

