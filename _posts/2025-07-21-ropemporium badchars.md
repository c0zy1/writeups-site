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
3. send our encrypted payload
4. modify our payload in memory
5. call print_file and get the flag

ropper 

![ropper](/assets/ropemporium badchars/set.png)
![ropper](/assets/ropemporium badchars/write.png)
![ropper](/assets/ropemporium badchars/xor.png)

Here we see that we have all the gadgets we need, one to set our desired registers, another to perform the write and finally a xor gadget to decrypt in memory.

Lets put it all together and build a working exploit to print the flag.

 exploit
    ![exploit](/assets/ropemporium badchars/exploit.png)

This pwntools-script is built to do the following:

1. set all the variables and gadgets we need 
2. build a rop chain in 3 steps
3. step 1 : overflow the buffer and chain the pop regs gadget with the write gadget to get the payload safely into memory
4. step 2 : use a for loop to build a chain that iterates over the adress in memory and decrypts every single byte of "flag.txt" in memory
5. step 3 : use pop rdi; ret; to hand the adress of the decrypted string to print_file and get the flag


Conclusion

My code still looks horrible as i've been solving the challenges first and then proceeded to do writeups.

Next time i should complete 1-2 challenges, do writeups and check if my style is actually readable.