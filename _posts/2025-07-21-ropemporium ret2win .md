---
title: "ropemporium - challenge 1 'ret2win' "
date: 2025-07-21 00:00:00 +0800
categories: [ropemporium]
tags: [pwn, return-oriented programming]
---

The first challenge of ropemporium offers us any easy way to get into the art of return-oriented programming.

 First glance
    ![checksec](/assets/ropemporium ret2win/checksec.png)

As we can see NX(non executable Stack) is activated which means we cannot use the stack to execute shellcode directly, therefore we have to use a technique called return-oriented programming.

In this level we only perform one straight jump to get the flag so it doesnt really matter but it will in future challenges.

 gdb search
    ![gdb](/assets/ropemporium ret2win/gdb.png)

gdb gives us a few different pieces of information, ret2win is at 0x400756 and jumping to it will print a message congratulating us, it also calls system with "/bin/cat flag.txt" as its first argument stored in rdi. 

 putting it all together
    ![exploit](/assets/ropemporium ret2win/exploit.png)


Finally the exploit which does the following things:

 1. sends 40 bytes of junk(b'A' * 40) to reach the return pointer
 2. byte 41-48 are filled with the address of ret2win
 3. as soon as the function returns it will return to 0x400756(ret2win)


Conclusion

That was easy.

p.s. i don't know why the flag did not print i tried to figure it out but i couldn't. if you have any idea be sure to message me as i haven't had this issue with the other challenges.

