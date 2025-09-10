---
title: "ropemporium - challenge 2 'split' "
date: 2025-07-21 00:00:00 
categories: [ropemporium]
tags: [pwn, return-oriented programming]
---

The second challenge of ropemporium asks us to use one single gadget to call system and return safely to main.

 First glance    
    ![checksec](/assets/ropemporium split/checksec.png)

As we can see NX(non executable Stack) is activated which means we cannot use the stack to execute shellcode directly, therefore we have to use a technique called return-oriented programming.

 gdb 
    ![gdb](/assets/ropemporium split/gdb.png)
   
We can see that the pwnme function is available again which will be our entrypoint because it has once again a vulnerable read function.

We also could use the hardcoded adress of system and main to return to them if we would like to.

 rabin2
    ![rabin2](/assets/ropemporium split/rabin2.png)

Here we can see the adress of our desired string "/bin/cat flag.txt", in challenge1 we just had to  jump to ret2win which would do the rest for us. This time we have to set rdi, then jump to system and get the flag. 

 ropper
    ![ropper](/assets/ropemporium split/ropper.png)

Here we see our relevant gadget, pop rdi; ret;. This gadget will help us to set rdi(1st argument in x86_64 System V ABI) to the adress of the string 

 exploit
     ![exploit](/assets/ropemporium split/exploit.png)

Finally our exploit script will do the following:

1. start process(p= process(bin.path)), this opens a process by picking the path out of the binary object we created
2. send a cyclic pattern to force a crash, read the rip, set offset and print the offset
3. b'A' * 40 (offset) overflow the buffer until we reach the return adress
4. our gadget will be placed on byte 41-48(return adress), once the function returns 2 things will happen, first of all pop rip; then rsp +8, this means that the instruction pointer is loaded with the gadget (pop rdi; ret;) and the stack pointer is pointing a 8 bytes higher than when the ret started which means the rsp is now exactly at our str1("/bin/cat flag.txt")
5. now a ret gadget to realign the stack
6. jump to adress of system, system gets executed, 1 argument passed via rdi("/bin/cat flag.txt")
7. flag is printed


Conclusion

I have to start using a better structure for my pwntools scripts to make it more readable, taking hardcoded adresses by hand and only letting pwntools do the searching or extracting if necessary. 

