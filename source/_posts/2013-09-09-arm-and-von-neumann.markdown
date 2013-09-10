---
layout: post
title: "ARM &amp; von Neumann"
date: 2013-09-09 19:56
comments: true
categories: 
---

When the ARM architecture made its way into micro-controllers it also
made the von Neumann architecture more common in firmware
development. The main effect that this has is allowing execution of
instructions from memory. The unified memory model allows jumping into
Flash or RAM. Some processors even load the entire Flash into RAM
before on boot and execute only from RAM. This allows for the
potential to have self modifying code. Which while dangerous from a
safety standpoint, allows for some cool tricks. Sometimes the RAM and
Flash are different speeds which allows either faster execution when
code is in RAM or a shallower pipeline to flush on branches. This
architecture also means that data in Flash can be read as easily and
often as quickly as from RAM and constant structures compile cleanly.

Most ARM micro-controllers support using the ARM Thumb instruction set
which is a subset of the ARM opcodes which only use 16 bits instead of
the normal 32 bits. The reduced instruction set still provides all
operations but limits the number of registers that can be accessed. It
does sometimes require more instructions to do the same work, but for
the most part the code density is much higher in Thumb mode. ARM thumb
instructions can often have even higher density than 8 bit
micro-controllers due to the ability to handle 32 bit math operations
with a single operation.

The addition of ARM also brings lots of options for compilers. GCC,
LLVM, IAR, and several others provide varying levels of support for
micro-controllers and various levels of optimization for the Thumb
instruction set.
