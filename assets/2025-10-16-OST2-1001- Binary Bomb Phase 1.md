---
title: "OST2-1001: Binary Bomb Phase 1"
date: 2025-10-16 00:00:00 
categories: [OpenSecurityTraining2]
tags: [Reverse Engineering]
---

This post will cover the first phase of the binary bomb lab originally created by CMU.

I have done it while working through OST2-1001 Assembly on OpenSecurityTraining2.

At first we start the binary and step to the first relevant function call.

This function reads the first line from the text file we provide and returns a pointer to a memory location which gets passed to phase_1.

![bomb](/assets/ost2/binarybomb/callread.png)


After we step into phase_1 we can observe that strings_not_equal is being called with the a pointer to rdi and a pointer to rsi. The pointer to rdi represents our input while the pointer to rdi is a hardcoded string.

We can also see that right after the call to strings_not_equal a test eax, eax instruction will AND the eax register, set the SF, ZF and PF and throw away the numeric result.

This means that our input has to make this function return 0 otherwise test will not set the zero flag and we will jump to phase_1 +29 which will in return call bomb_explode. 

![bomb](/assets/ost2/binarybomb/stringsnotequal.png)


After we step into strings_not_equal we realize that its just a string comparison moving char by char with rax as the incrementing factor.

It moves one byte at the adress in rbx(where our input string is stored) into edx, and then moves the offset into eax which in this first case is 0. 

After that it calculates the adress of the first char at the hardcoded adress which is stored in rbp and compares it to dl which was previously set.

In the first example 0x49 (our input "I") gets compared with 0x49 and passes the test to not take the jump

![bomb](/assets/ost2/binarybomb/stringsnotcode.png)

After that we simply continue with the program execution and find ourselves defusing the first phase.

![bomb](/assets/ost2/binarybomb/defused.png)

GGs 

