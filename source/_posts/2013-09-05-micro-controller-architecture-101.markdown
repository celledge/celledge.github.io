---
layout: post
title: "Micro-Controller Architecture 101"
date: 2013-09-05 21:35
comments: true
categories: Firmware
---

Modern micro-controllers are basically Systems on a Chip (SOC) with
memory integrated into the package as well. The lack of external
memory buses allows for smaller packages and pin counts. It also
greatly simplifies the use of these parts.

Many smaller micros (PIC16, PIC24, AVR) are setup in what is known as
the Harvard Architecture. This means that the executable memory and
data memory are completely separated. Instructions cannot be executed
from the data memory, and the different memory sections can be
addressed in completely different ways.

Usually the executable memory is on Flash or ROM (rarely now
days). While the processor is able to directly fetch instructions from
Flash, there is typically no way to directly read variables from
it. Usually special retrieval instructions or address remapping are
needed. Some compilers will automatically use the constructs for any
constant variable reads.

Furthermore, Flash and RAM don't even need to have the same line
size. The PIC16F series has 14 bit instruction lines and 8 bit data
lines. The PIC24F series has 24 bit instruction lines and 16 bit data
lines. The AVR has 16 bit instruction lines and 8 bit data lines. The
odd flash word sizes can often make constant storage complicated. The
compilers for the two PICs usually store constants in just the bottom
8 or 16 bits of a line thus wasting big chunks of Flash if you use
lots of constants. If you want to make full use of the storage, you
must use the Flash peripheral registers instead of the simpler address
remapping that the compiler uses. While the alignments are much
cleaner on the AVR, all Flash data reads have to be done with a
special load instruction. So it falls in between the two PIC methods in
complexity.

Details on these operations are a topic for a future post. Next time I will cover Peripheral Registers, Flash Pages, and Memory Banks.
