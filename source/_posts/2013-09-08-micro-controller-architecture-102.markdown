---
layout: post
title: "Micro-Controller Architecture 102"
date: 2013-09-08 08:37
comments: true
categories: Firmware
---

In addition to the Flash and RAM on the micro-controller, there is a
chunk of address space dedicated to the peripheral registers. These
registers provide the interface to all the peripherals which are
probably the reason that you chose the mirco-controller. There are two
ways that the peripheral registers can be addressed. The first is via
memory access. Usually there is a dedicated hole in the memory space
which addresses these registers instead of RAM. That means that load
and store instructions to these addresses will handle reading and
writing to the peripheral registers.

The second approach is via special instructions. The AVR uses this
approach to access the lowest registers. The intention is for these to
be the most commonly used ones, but it never seems to work out that
way. The limited number of registers that can be accessed this way is
because there are only a few bits available in the opcode to act as
addressing for the instruction. In order to access all the other
registers, they must be addressed by the first approach. On the AVR
this method only take one instruction while the first can take 3. Two
instructions to setup an address pointer and one for the load or store
immediate. Thankfully, the gcc compiler will handle figuring this one
out for you.

The small PICs have their own oddities as well. Instead of using 16
bit addresses to access RAM, they rely on a concept of memory
banks. In order to access memory, a bank has to be set which
determines what memory can be addressed using the load and store
instructions. For each bank, there is a chunk of addresses devoted to
registers, followed by RAM, and a small chunk of common RAM. The
common RAM is the only part that is the same no matter which bank is
selected. Accessing memory that is all in the same bank is compact,
but if several items need to be transfered between pages then a lot of
instructions are wasted shuttling the values between banks.

Typically Flash is read only by the instruction fetcher based off of
the program counter. However when writing to Flash you often need to
consider that it is composed of what are known as blocks and pages. A
block is a chunk of Flash memory which has to be erased as one unit. A
page is a piece of Flash which has to be written as one
unit. Typically a block contains more than one page. If you never plan
on writing self modifying code (bootloaders) then you will probably
never need to worry about this. But if you need to change portions of
the Flash you will need to handle erasing at the block level and
writing at the page level. If you wish to modify only parts of the
Flash, then you will have to store the previous values, erase the
block, and rewrite all the pages.
