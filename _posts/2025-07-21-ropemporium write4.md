---
title: "ropemporium - challenge 4 'write4' "
date: 2025-07-21 00:00:00 +0800
categories: [ropemporium]
tags: [pwn, return-oriented programming]
---

This time we are tasked with performing our first memory write.

 First glance
    ![checksec](/assets/ropemporium write4/checksec.png)

 gdb
    ![gdb](/assets/ropemporium write4/gdb.png)

We see that we are given some things to check out like usefulFunction, useful Gadget(our write gadget) and print_file.

 ropper

![ropper](/assets/ropemporium write4/ropper.png)

We use ropper -f write4 to find the relevant pop r14; pop r15; ret; gadget, this enables us to prepare our memory write.

 exploit
    ![exploit](/assets/ropemporium write4/exploit.png)

Finally we craft our full exploit chain:
    
1. pop r14; pop r15; ret -> setup the registers
2. mov qword ptr [r14], r15; ret; -> dereference r14 and write the value to that dereferenced pointer
3. ret; to realign the stack
4. pop rdi; ret; -> set rdi to the given adress where we wrote our "flag.txt"
5. jump to print which executes with the given argument in rdi and prints the flag

Conclusion

That was a nice entry to memory writing.
My code still looks awful.
