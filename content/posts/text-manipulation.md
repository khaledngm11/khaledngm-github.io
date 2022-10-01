+++
author = "Khaled Ngm"
title = "Text Manipulation Using Linux Commands"
date = "2021-03-05"
description = ""
+++
## Linux Text Processing Tools
* grep
* cut
* sed
* sort
* uniq
* tr
* Examples ( most important )

  * Log file analysis
  * Frequency analysis
  * 

## 1. grep
**GREP** is a tool that uses Regular Expressions, and prints any lines which match a specified pattern .

`Syntax`
`grep [options] pattern [files]`


```
 Most important Options:
-c: Count the number of lines that match a pattern.
-h: Display the matched lines, but do not display the filenames.
-i: Ignore case for matching.
-l: Displays list of a filenames only.
-n: Display the matched lines with line numbers.
-v: Prints all the lines that do not match the pattern.
-e exp: Specifies expression with this option. Can use multiple times.
-f file: Takes patterns from a file.
-E: Treats pattern as an extended regular expression (ERE).
-w: Match whole word.
-o : Print only the matched parts of a matching line.
```
## 2. cut
**CUT**  is a command-line tool for cutting text and writing the result to standard output .

`Syntax`
`cut [options] [files]`
```
Most important Options: 
-b, --bytes=LIST        # select only these bytes
-c, --characters=LIST   # select only these characters
-d, --delimiter=DELIM   # use DELIM instead of TAB for field delimiter
-f, --fields=LIST       # select only these fields;  also print any line that 
```
## 3. sed
**SED**   A stream editor is used to perform basic text transformations , such as searching , editing , replacing , insertion or deletion  .

`Syntax`
`sed [options] [script] [files]`
```
 Most important Options: 
-n  : suppress automatic printing of pattern space
-e  : add the script to the commands to be executed
-f  : add the contents of script-file to the commands to be executed
-i  : Changes in orignal file
-r  : use extended regular expressions in the script
```
```
Operations:
s : for substitution
d : for deletion
p : Print out the pattern space (to the standard output). This command is usually only used in conjunction with the -n command-line option.
g : global replacement
i : Insert data  
a : Append data
```
## 4. sort
**SORT** is used to arrange lines numerically and alphabetically.

`Syntax`
`sort [options] [files]`
```
 Most important Options: 
-d, --dictionary-order : consider only blanks and alphanumeric characters
-f, --ignore-case : fold lower case to upper case characters
-M, --month-sort : compare (unknown) < 'JAN' < ... < 'DEC'
-h, --human-numeric-sort : compare human readable numbers (e.g., 2K 1G)
-n, --numeric-sort : compare according to string numerical value
-R, --random-sort : shuffle, but group identical keys.  See shuf(1)
-r --reverse : reverse the result of comparisons

```
## 5. uniq
**UNIQ** is used to filters out the repeated lines in a file.

`Syntax`
`uniq [options] [files]`
```
 Most important Options: 
-c, --count : prefix lines by the number of occurrences
-d, --repeated : only print duplicate lines, one for each group
-D  : print all duplicate lines
-i, --ignore-case :  ignore differences in case when comparing
-u, --unique :  only print unique lines

```
## 6. tr
**TR**  is a command line utility for translating or deleting characters, And support more complex translation .

`Syntax`
`tr [options] [set1] [set2]`
```
 Most important Options: 
-c : complements the set of characters in string.i.e., operations apply to characters not in the given set
-d : delete characters in the first set from the output.
-s : replaces repeated characters listed in the set1 with single occurrence
-t : truncates set1
```
## Example ( Logs File analysis )
First , you can use your apache2 logs file lacate in `/var/log/apache2/access.log`
or you can download sample of logs file [here](https://raw.githubusercontent.com/elastic/examples/master/Common%20Data%20Formats/apache_logs/apache_logs).
```
83.149.9.216 - - [17/May/2015:10:05:03 +0000] "GET /presentations/logstash-monitorama-2013/images/kibana-search.png HTTP/1.1" 200 203023 "http://semicomplete.com/presentations/logstash-monitorama-2013/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_9_1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/32.0.1700.77 Safari/537.36"
83.149.9.216 - - [17/May/2015:10:05:43 +0000] "GET /presentations/logstash-monitorama-2013/images/kibana-dashboard3.png HTTP/1.1" 200 171717 "http://semicomplete.com/presentations/logstash-monitorama-2013/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_9_1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/32.0.1700.77 Safari/537.36"
83.149.9.216 - - [17/May/2015:10:05:47 +0000] "GET /presentations/logstash-monitorama-2013/plugin/highlight/highlight.js HTTP/1.1" 200 26185 "http://semicomplete.com/presentations/logstash-monitorama-2013/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_9_1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/32.0.1700.77 Safari/537.36"
83.149.9.216 - - [17/May/2015:10:05:12 +0000] "GET /presentations/logstash-monitorama-2013/plugin/zoom-js/zoom.js HTTP/1.1" 200 7697 "http://semicomplete.com/presentations/logstash-monitorama-2013/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_9_1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/32.0.1700.77 Safari/537.36"
83.149.9.216 - - [17/May/2015:10:05:07 +0000] "GET /presentations/logstash-monitorama-2013/plugin/notes/notes.js HTTP/1.1" 200 2892 "http://semicomplete.com/presentations/logstash-monitorama-2013/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_9_1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/32.0.1700.77 Safari/537.36"
83.149.9.216 - - [17/May/2015:10:05:34 +0000] "GET /presentations/logstash-monitorama-2013/images/sad-medic.png HTTP/1.1" 200 430406 "http://semicomplete.com/presentations/logstash-monitorama-2013/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_9_1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/32.0.1700.77 Safari/537.36"
```
It's a sample of logs that contain [IP addrss , Time , request type , path , HTTP version , status code , length , host , and user agent]
* ### What is the number of hosts that visited website ?
  * First we have to use cut command with space delimeter and field 1 and that will print results like this .

  `cat log.txt | cut -d '' -f 1`
  ```
  92.115.179.247
  92.115.179.247
  92.115.179.247
  5.10.83.53
  46.119.114.245
  173.231.106.34
  66.169.220.99
  66.249.73.135

  ```
  * Then we have to delete duplicated lines with uniq command -d flag and wc command with -l flag to count the lines .
  
  ``cat log.txt | cut -d ' ' -f 1 | uniq -d | wc -l ``
* ### What is the most IP that visited website and what is the count of requests ?
  * We will use the previous command to cut IPs .
  * Then we should sort the lines before count as only sequential lines will be counted .

  `` cat log.txt | cut -d ' ' -f 1 | sort ``
  ```
  99.252.100.83
  99.252.100.83
  99.252.100.83
  99.252.100.83
  99.252.100.83
  99.252.100.83
  99.252.100.83
  99.33.244.41
  99.33.244.41
  99.33.244.41
  99.33.244.41
  99.33.244.41
  99.33.244.41
  ```
  * After that we can pipe result with uniq -c to count the sequentioal repeted lines .

  ``cat log.txt | cut -d ' ' -f 1 | sort | uniq -c``
  ```
   2 98.245.87.136
   5 98.248.53.169
   10 98.252.226.135
   1 98.64.39.111
   2 98.85.3.179
   1 99.100.25.83
   5 99.101.69.97
   2 99.11.114.240
   1 99.146.78.10
  ```
  * As we realize we need to rearrange results with sort -n(numerical) -r (reverse order).
  
  ``cat log.txt | cut -d ' ' -f 1 | sort | uniq -c | sort -nr``

  ```
    482 66.249.73.135
    364 46.105.14.53
    357 130.237.218.86
    273 75.97.9.59
    113 50.16.19.13
    102 209.85.238.199
  ``` 
* ### Are there any malicious attack ?
* ### What is the most user agents that visited website ?
## Example ( Frequency Analysis )
* ### What is frequency analysis ?
In statistics, frequency is the number of times an event occurs. Frequency Analysis is an important area of statistics that deals with the number of occurrences of letters .

In cryptanalysis, frequency analysis (also known as counting letters) is the study of the frequency of letters or groups of letters in a ciphertext. The method is used as an aid to breaking classical ciphers , you can read [more](https://www3.nd.edu/~busiforc/handouts/cryptography/letterfrequencies.html)

consider we have [hello world] , 
then frequency of [l] is 3 and frequency of [o] is two etc.

* ### What is our goal ?
  * first of all , We get a cipher text and our goal is to calculate frequency of each letter in order.
  * Then we have a standard frequency for most common letters ``ETAOINSHRDLCUMWFGYPBVKJXQZ`` .
  * finally we change most common letter in cipher text with standard most common letter to get plain text .
* ###  Try to decrypt this cypher text using frequency analysis .
```
YSZ MZYSWQWPWIH FZSVUQ KRZBNZUOH XUXPHTVT RZPVZT WU YSZ KXOY YSXY VU XUH PXUINXIZ, ZXOS PZYYZR SXT VYT WLU GZRTWUXPVYH. YSZ MWTY WFEVWNT YRXVY YSXY PZYYZRT SXEZ VT YSZ KRZBNZUOH LVYS LSVOS YSZH XGGZXR VU X PXUINXIZ. OPZXRPH VU ZUIPVTS YSZ PZYYZR "B" XGGZXRT KXR PZTT KRZBNZUYPH YSXU, TXH, "X". VU YVMZT IWUZ FH, VK HWN LXUYZQ YW KVUQ WNY YSZ KRZBNZUOVZT WK PZYYZRT LVYSVU X PXUINXIZ, HWN SXQ YW KVUQ X PXRIZ GVZOZ WK YZAY XUQ OWNUY ZXOS KRZBNZUOH. UWL, SWLZEZR, LZ SXEZ OWMGNYZRT YSXY OXU QW YSZ SXRQ LWRD KWR NT. FNY VU KXOY, LZ QWU'Y ZEZU UZZQ YW QW YSVT TYZG, XT KWR MWTY PXUINXIZT YSZRZ XRZ QXYXFXTZT WK YSZ PZYYZR KRZBNZUOVZT, LSVOS SXEZ FZZU OXPONPXYZQ FH PWWDVUI XY MVPPVWUT WK YZAYT, XUQ XRZ YSNT EZRH SVISPH XOONRXYZ.
```
  1. Try to get most common repeated letters in this cypher text using grep , sort and uniq commands .

  ``cat frequency.txt | grep -o . | sort | uniq | sort -nr``

   we will get most common letters that will be ``ZYXUSWVTRPNOKHQILGFEBMDAB`` if are there any characters with zero frequency put them at the last and 
   maybe some characters will have the frequency ... you have to try to change position every time to get plain text , after I tried I got the right        
   frequency , ``ZYXWVUTSRQPONMLKIHGFEDCAB`` and you can use online [tools](https://md5decrypt.net/en/Letters-frequency-analysis/) to decrypt it

  2. Finally I have to change each character with equivalent character with tr command .

 ``cat frequency.txt | tr 'ZYXWVUTSRQPONMLKIHGFEDCAB' 'ETAOINSHRDLCUMWFGYPBVKJXQZ'``

**finally I got the plain text**
```
THE METHODOLOGY BEHIND FREQUENCY ANALYSIS RELIES ON THE FACT THAT IN ANY LANGUAGE, EACH LETTER HAS ITS OWN PERSONALITY. THE MOST OBVIOUS TRAIT THAT LETTERS HAVE IS THE FREQUENCY WITH WHICH THEY APPEAR IN A LANGUAGE. CLEARLY IN ENGLISH THE LETTER "Q" APPEARS FAR LESS FREQUENTLY THAN, SAY, "A". IN TIMES GONE BY, IF YOU WANTED TO FIND OUT THE FREQUENCIES OF LETTERS WITHIN A LANGUAGE, YOU HAD TO FIND A LARGE PIECE OF TEXT AND COUNT EACH FREQUENCY. NOW, HOWEVER, WE HAVE COMPUTERS THAT CAN DO THE HARD WORK FOR US. BUT IN FACT, WE DON'T EVEN NEED TO DO THIS STEP, AS FOR MOST LANGUAGES THERE ARE DATABASES OF THE LETTER FREQUENCIES, WHICH HAVE BEEN CALCULATED BY LOOKING AT MILLIONS OF TEXTS, AND ARE THUS VERY HIGHLY ACCURATE.
```
