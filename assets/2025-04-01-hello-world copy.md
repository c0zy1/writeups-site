---
title: "Heap Exploits - Part 1 : House of Force"
date: 2025-04-01 00:00:00 +0800
categories: [Heap Exploits]
tags: [Heap]
---

My humble beginnings with heap exploitation starts with the house of force.

The following post will introduce you to this technique and lead you through an example.

How it works in theory:

1. each of mallocs core functions, such as malloc() and free(), has an associated hook which takes the form of a writeable function pointer in glibcs data section
2. corrupting the top chunk size
3. calling malloc to allocate a large chunk that reaches into glibcs data section to overwrite the __malloc_hook, preferably with system()
4. place or search "/bin/sh\0" and prepare it
5. call malloc again, this time triggering the attacker controlled __malloc_hook and get a shell!



