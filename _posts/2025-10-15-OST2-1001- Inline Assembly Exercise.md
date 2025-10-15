---
title: "OST2-1001: Inline Assembly Exercise"
date: 2025-10-15 00:00:00 
categories: [OpenSecurityTraining2]
tags: [Assembly, Shellcode]
---

This post will document my first experience with inline assembly. It is also a short writeup for the first exercise in the OST2 1001 Assembly course.

## Introduction

The last few days i've been refreshing and advancing my assembly knowledge and i started to work through the OST2 1001 Assembly course. This course challenged the learners to write some inline assembly in visual studio.

![exercise](/assets/ost2/exercise.png)


The exercise requires us to write assembly in direct bytes, words or qwords, this means the above assembly instructions have to be written in their byte representation.

4 important rules mentioned by the instructor are:

- raw bytes start with db(byte), dw(word) dd(doubleword also called dword)
- instructions are emitted in LSB (least-significant byte) order so if you have the Opcode 7405 as to be seen in the solution it has to be written 0574 to be correctly interpreted
- the bytes cannot start with a letter e.g. C3 has to be written as 0C3
- the bytes have to finish with an h to indicate that they are hex -> 0C3 -> 0C3h 

## The fun manual
Looking at the Intel manual we will go through one example how to build an assembly instruction in raw bytes.

Example:
mov eax, 0xAABBCCDD 

We want to tell the cpu to move a 32 bit immediate(0xAABBCCDD) into a 32 bit register(eax).

![exercise](/assets/ost2/manual.png)

In the Intel Manual we find that to do this we have to use the Opcode B8 followed by the imm32(32-bit immediate) we want to write into the register.

## Solution Source code

![exercise](/assets/ost2/solution.png)
![exercise](/assets/ost2/source.png)

Main is calling our raw bytes snippet and executing the commands we asked for, the other instructions can be built in the same way by looking up the opcodes in the intel manual and adhering to the 4 rules above. Not to difficult at this level of complexity.

db 9Eh -> SAHF instruction

dw 0574h -> JZ rel8 -> "jump 5 bytes from the location after this instruction if the zero flag is set" this jump will be taken because the SAHF instruction sets the zeroflag in this case

db 25h -> and eax

dd 00031337h  this is the immediate taken by the and in the line prior 

db 0C3h -> ret instruction (pop rip, rsp+8)

## Debugging

![exercise](/assets/ost2/disas.png)


As we can see it all worked out and our code is interpreted as expected.

## Conclusion

This course is incredibly detailed and motivates one to learn in an engaging way, the instructor is very kind and detail oriented. 

It's almost unbelievable that this material is free. 

Check it out at:

https://p.ost2.fyi/