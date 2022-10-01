+++
author = "Khaled Ngm"
title = "Digital forensics write-ups (CyberTalents)"
date = "2022-10-01"
description = ""
+++
#### We will discuss Some of digital forensics CTFs at CyberTalents (It will be updated)
# 1-G&P
``Just Open the File and Capture the flag . Submission in MD5``

 First of all we have to download document file and lets use ``string`` command to check file content .

```
┌──(kali㉿kali)-[~/Downloads]
└─$ strings G\&P+lists.docx  | grep -E [a-f0-9]{32} 
Flag.txt877c1fa0445adaedc5365d9c139c5219PK
                                     
```
As we see the submission of flag is md5 so we used ``-E`` Flag to identify the chars and length of md5 hash .

# 2-Hidden Message
``A cyber Criminal is hiding information in the below file . capture the flag ? submit Flag in MD5 Format``

 Download document file and lets use ``string`` command to check file content also.

```
┌──(kali㉿kali)-[~/Downloads]
└─$ strings -n 32 hidden_message.jpg 
 b1a1f2855d2428930e0c9c4ce10500d5
%&'()*456789:CDEFGHIJSTUVWXYZcdefghijstuvwxyz
&'()*56789:CDEFGHIJSTUVWXYZcdefghijstuvwxyz
                                                           
```

# 3-Partition Lost
``Our Company's CEO had a car accident. His HDD was damaged and he lost all his files and partitions. Can you help him to recover his important data``

 Download image file and lets use ``string`` command .

```
┌──(kali㉿kali)-[~/Downloads]
└─$ strings -n 8 partition-lost.img | grep -oiP "flag(.*)" | tail                                                                                                    1 ⚙
flag:0x%x)
flag:0x%x)
FLAG[%d] APPI_CALL_MO_SETUP_IND
flag_1[%x]/MMI_flag_2[%x]/PreState[%d]
FLAG
FLAG: FL_SJAVA_RUN_FLAG = SET
Flag[No matching mode]
Flag()
FLAG(701_L@b$_DR_DFIR)
FLAG(701_L@b$_DR_DFIR)
                                                           
```
submit the last one :)

# 4-I love images
``A hacker left us something that allows us to track him in this image, can you find it?``

 Download the image and lets use ``string`` command .

```
┌──(kali㉿kali)-[~/Downloads]
└─$ strings godot.png | tail                                                                                                                                         1 ⚙
%rG'
*TYlT_qP
vL.I
VMqa.%
pDuF(
op+P
q}'ZA0
O-T&
IEND
IZGECR33JZXXIX2PNZWHSX2CMFZWKNRUPU======
                                   
```
As we see It's a base32 , lets decode it . 
```
┌──(kali㉿kali)-[~/Downloads]
└─$ strings godot.png | tail -n 1 | base32 -d                                                                                                                    1 ⨯ 1 ⚙
FLAG{Not_Only_Base64} 
```

# Thanks
