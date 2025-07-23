---
title: "ropemporium - challenge 5 'badchars' "
date: 2025-07-21 00:00:00 +0800
categories: [ropemporium]
tags: [pwn, return-oriented programming]
---

This challenge marks the first significant step in terms of difficulty.

 First glance
     ![checksec](/assets/ropemporium badchars/checksec.png)
     
 badchars :(
     ![badchars](/assets/ropemporium badchars/badchars.png)

So we can't use any of this chars in our rop chain but this is where we get creative. 
Let's do something like the following:

1. find the gadgets
2. encrypt our payload 
3. send our payload
4. modify our payload in memory
5. call print_file and get the flag

 gdb