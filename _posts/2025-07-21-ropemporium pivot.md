---
title: "ropemporium - challenge 7 'pivot' "
date: 2025-07-21 00:00:00 +0800
categories: [ropemporium]
tags: [pwn, return-oriented programming]
---

This challenge teaches us how to escape limited stack space and pivot to a new stack to jump onto a fresh rop-chain.

 First glance
    ![checksec](/assets/ropemporium pivot/checksec.png)


starting the challenge
    ![challenge](/assets/ropemporium pivot/chall.png)

As we can see the challenge offers 2 read entries and gives us a clear direction how to approach it.

We are also told that 1. our foothold function is not yet resolved, as is our ret2win function, and 2. that we have to find the offset by examining the libpivot.so file.

We therefore have to understand how plt-stubs, got and lazy binding works. All of this is explained at https://ropemporium.com/guide.html. 


 static binary examination
    ![static](/assets/ropemporium pivot/static.png)

1. got entry for the not yet resolved foothold_function, this can be confirmed by firing up gdb and disas usefulFunction.
2. plt stub for foothold_function, when the function gets called for the first time this stub dereferences the pointer to check if there is a real adress behind the got entry, if not it calls the linker to resolve it, after that the got entry points to the resolved adress.
3. offsets of ret2win and foothold_function in the custommade libpivot.so, ret2win

 ropper(an example of a few of the gadgets we will use)
    ![ropper](/assets/ropemporium pivot/raxrdi.png)
    ![ropper](/assets/ropemporium pivot/callrax.png)
    ![ropper](/assets/ropemporium pivot/pivot.png)
 
Our plan will be like this:

1. give the big rop chain into the first read, catching the adress from the output
2. handing the stack pivot to the 2nd read and jumping to our first chain, printing the flag

 exploit
    ![exploit](/assets/ropemporium pivot/exploit.png)

This exploit does the following:

1. using all the static elements we found with readelf, nm, ropper/gdb to prepare all our gadgets.
2. build a chain(leak_call_chain) that does the following
2.1 place footplt(plt stub of foothold_function) at the return adress after the pivot
2.2 pop rax; ret; + footgot sends the got entry into rax 
2.3 the mov rax gadget will dereference the got entry of foothold after it has been resolved by our first attempt to call it and write the actual adress into rax
2.4 pop rbp; ret; + ret2win offset will send our offset to rbp to thenn add this onto rax which contains our foothold_function adress 
2.5 we use call rax to call the adress in rax which is exactly the adress of our ret2win

3. we start the process with process(bin.path), receive the prompt and read the adress in integer form for further usage
4. we send our leak_call_chain
5. we execute our stack smash with the stack pivot which does the following
6. pop rax; ret; + addr (this is where our big chain is waiting) will send this adress to rax, after that we exchange rax and rsp and hit a ret so our stack gets transfered to the adress where the rest of our chain is waiting, the ret of this gadget will load the got entry into rip and make it work

Conclusion

This time my code is way more readable although there is room for improvement.