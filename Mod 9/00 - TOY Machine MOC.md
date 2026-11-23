---
tags:
  - cs/architecture
  - cs/toy-machine
  - moc
aliases:
  - TOY Machine Index
---

# TOY Machine-Language Programming & Virtual Machine

> [!abstract] Big Picture
> This guide covers how high-level programming abstractions (functions, I/O streams, arrays, and linked structures) are built from a minimal 16-instruction machine language, and how that hardware architecture is implemented in software as a **virtual machine** (simulator).

## Concept Map

### Machine-Language Programming
- [[TOY Instruction Set Reference|TOY ISA & Instruction Reference Card]]
- [[TOY Functions and Standard I-O|Functions (Jump & Link) and Standard I/O (`M[FF]`)]]
- [[TOY Arrays and Linked Structures|Arrays, Linked Structures, and Why Learn Machine Language]]

### Virtual Machine Design & Implementation
- [[TOY Virtual Machine Concepts|Simulators, Booting/Dumping, Security Caution & TOY Family]]
- [[Implementing TOY in Java|TOY.java Simulator Architecture & Bitwise Implementation]]

## Core Principles
1. **Universality of a Minimal ISA:** Despite having only 16 instructions, 16 registers, and 256 words of memory, TOY can express standard control flow, data structures, and arbitrary algorithms (given sufficient memory/time).
2. **Code and Data Share Memory (Von Neumann):** Because instructions and data are both 16-bit words in the same memory array, programs can manipulate addresses dynamically—and bugs (like buffer overflows) can overwrite instructions.
3. **Programs Processing Programs:** A simulator takes another program as data input, forming the foundation for virtual machines, emulators, and compilers.
