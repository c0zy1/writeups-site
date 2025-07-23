---
title: "ropemporium - challenge 6 'fluff' "
date: 2025-07-21 00:00:00 +0800
categories: [ropemporium]
tags: [pwn, return-oriented programming]
---

Today i will walk you through ropemporium challenge 6 "fluff".

There was nothing fluffy about this challenge for me.

First glance
    ![checksec](/assets/ropemporium fluff/checksec.png)


Next up we will go on to find our write primitive and reverse engineer the path of execution. 

 gdb
    ![gdb](/assets/ropemporium fluff/gdb.png)


Here we are delivered the questionable Gadgets which don't seem to help us at first glance but after googling/chat gpt we find out that there is somewhat of a way in this gadget chain.

We could do something like the following:

1. find adresses in memory that store each byte we need to get the full "flag.txt" string 
2. build a rop chain that loops and uses the bextr-gadget to store the adress of the required byte at rbx
3. transfer this adress to al via xlatb gadget that takes the value of rbx(the adress) ads the value of al as an offset, dereferences this value and writes it to al 
4. use xstosb gadget to transfer the bytes, one by one, to our desired memory adress which we will prepare in rdi 
5. set the adress where our string is stored in rdi and call print_file to print the flag

Finding the required bytes

There are two methods i found(the python one is from a write up i examined):

1. manually search in gdb
    ![vmmap](/assets/ropemporium fluff/vmmap.png)
    ![find](/assets/ropemporium fluff/find.png)
2. small python script that does the work 
    ![search](/assets/ropemporium fluff/search.png)

Our final exploit would look like this:
    ![exploit](/assets/ropemporium fluff/exploit.png)


Explanation:

1. current_rax is the value of rax at the time of the crash, since we have to account for the fact that our xlatb-gadget will take the value of al and use it as an offset we have to subtract this value to negate this effect and get the adress right
2. we use base and the locations to build an array of the actual locations of "f l a g . t x t"
3. we order our gadgets print_file, pop rdi; ret; and the main write chain
4. chain begins with the offset to reach the return adress
5. we use a for loop to craft a specific write chain which does the following
    1. check if we are at the first letter, if not set current_rax to the previous ordinal value of the letter that came before since this value is still present in al from our write chain before
    2. first gadget, this one takes rcx and copies it to rbx with a bit mask that is taken form rdx, p64(0x4000) is the bit mask which is separated into 2 high bits and 2 low bits, 40-> 4*16 means write all 64 bits and, 00 means start at offset 0
    3. calculate or actual position, taking the current_rax either at crash time(defined above base, addr) for the first letter or the previous letter into account and the add rcx, 0x3ef2
    4. now with everything prepared we add the setal gadget and finally the pop_rdi + the adress(0x601068 + i) to then finally use our write gadget
    5. this loop produces a chain that will write f l a g . t x t one by one to the adresses 0x601068 0x601069 etc.
6. pop_rdi, addr, and print_file to get our flag


Conclusion

I got stuck for a long time and had to use a write-up to bridge the gap as i didn't come to a solide approach on how to split the flag.txt into single bytes, using single bytes that are present in the binary is my greatest learning from this challenge.