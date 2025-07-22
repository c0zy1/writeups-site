---
title: "ropemporium - challenge 6 'fluff' "
date: 2025-07-21 00:00:00 +0800
categories: [ropemporium]
tags: [pwn, return-oriented programming]
---

Today i will walk you through ropemporium challenge 6 "fluff".

There was nothing fluffy about this challenge as i'd say this is the biggest jump in complexity regarding the ropemporium series.

1. First glance
    ![checksec](/assets/ropemporium fluff/checksec.png)

as we can see NX(non-Executable stack) is set which means that we can't execute shellcode from the stack. 
(alternative to checksec would be rabin2 -I fluff)

2. Finding our write primitive, reverse engineering the path of execution

next up we inspect the questionable Gadgets section where we see 3 potential gadgets that don't seem to yield anything at first glance.



