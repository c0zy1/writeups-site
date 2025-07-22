---
title: "ropemporium - challenge 3 'callme' "
date: 2025-07-21 00:00:00 +0800
categories: [ropemporium]
tags: [pwn, return-oriented programming]
---

Now it's time to call some functions with multiple arguments.

 First glance
     ![checksec](/assets/ropemporium callme/checksec.png)

 gdb


![gdb](/assets/ropemporium callme/gdb.png)

As we can see we will again overflow read in the pwnme function to rop our way through 3 functions, as demanded in the challenge description

 ropper
    ![ropper](/assets/ropemporium callme/ropper.png)

Here we can see the gadgets we need, we could use pop rdi; pop rsi; pop rdx; ret; to have the easiest and shortest way but i chose to split it into 2 gadgets.

 exploit
    ![exploit](/assets/ropemporium callme/exploit.png)

Our final exploit once again automates the offset-finding, uses hardcoded adresses of the required functions and delivers arguments via rop-chaining by abusing given gadgets to set the registers and then call the required functions.

Conclusion

Now it's looking more like rop!