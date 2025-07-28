---
title: "ropemporium - challenge 8 'ret2csu' "
date: 2025-07-21 00:00:00 +0800
categories: [ropemporium]
tags: [pwn, return-oriented programming]
---

The final challenge of ropemporium was a little bit easier than fluff and pivot.

It's sort of an introduction to ret2csu but missing the 2 stage part of leaking and then returning to libc to use the leaked gadgets to spawn a shell.

 First glance
    ![checksec](/assets/ropemporium ret2csu/checksec.png)


The challenge asks us to call a function with 3 parameters, basically the same challenge setup as callme just with fewer gadgets.

We find out that there are 'hidden' gadgets in the __libc_csu_init.

 gdb/ropper
    ![gadget](/assets/ropemporium ret2csu/gadget.png)
    ![ropper](/assets/ropemporium ret2csu/ropper.png)

With this 6 pop gadget, the mov rsi&rdx and our separate pop rdi-gadget we are equipped to build a script that calls the function with the 3 required parameters to get us the win.

 exploit
    ![exploit](/assets/ropemporium ret2csu/exploit.png)


This script works like this:

1. We use our ret2csu gadgets, first the 6 pop gadget -> ret, afterwards the full gadget until ret
2. Prepare rbx and rbp with 0 and 1 because of the cmp
3. prepare r12 with a small function like _fini since we are going to call and return after mov edi
4. prepare r13 as 0(could be random junk aswell doesn't matter)
5. prepare r14 and r15 with our desired paramteres
6. use the full gadget from mov rdx,r15 all the way down to the ret
7. use 7 qwords of junk to padd the 6 pops and the add rsp, 0x8 
8. use pop_rdi gadget to prepare rdi register with desired parameter
9. return to win function(0x400510)

Conclusion

A full and authentic ret2csu will be much harder since it will involve 2 stages as demonstrated in the paper on rompemporium.com/challenge/ret2csu.html 