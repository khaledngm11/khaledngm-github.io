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

# 1-G&P
``Just Open the File and Capture the flag . Submission in MD5``

 First of all we have to download document file and lets use ``string`` command to check file content .

```
┌──(kali㉿kali)-[~/Downloads]
└─$ strings G\&P+lists.docx  | grep -E [a-f0-9]{32} 
Flag.txt877c1fa0445adaedc5365d9c139c5219PK
                                     
```
As we see the submission of flag is md5 so we used ``-E`` Flag to identify the chars and length of md5 hash .





# Thanks
