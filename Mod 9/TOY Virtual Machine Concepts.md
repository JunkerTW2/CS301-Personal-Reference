---
tags:
  - cs/architecture
  - cs/virtual-machine
  - cs/security
  - simulation
---

# TOY Virtual Machine Concepts

Part of [[00 - TOY Machine MOC]] | Implementation: [[Implementing TOY in Java]].

## 1. Simulators vs. Translators
- **Simulator (Virtual Machine):** A program written in language $A$ running on machine $M_A$ that mimics every state transition (registers, memory, `PC`) of a target machine $M_B$ step-by-step.
  - *Pros:* Cheaper than building prototype hardware; easy to add debugging/tracing tools; runs legacy software on new hardware (backward compatibility).
  - *Cons:* Slower execution due to the simulation layer.
- **Translator (Compiler):** Converts a specific program written for $M_B$ into an equivalent program for $M_A$ that produces the same output, rather than mimicking $M_B$'s internal hardware state line-by-line.

---

## 2. Booting and Dumping
To operate a virtual machine (or real hardware), two lifecycle utilities are fundamental:
- **Booting:** Initializing the machine state—clearing registers/memory, loading the machine-language program (and initial data) into main memory, and setting `PC` to the starting instruction address (default `10` in TOY).
- **Dumping (Core Dump):** Printing the full internal snapshot of the machine—`PC`, all `16` registers (`R[0]`–`R[F]`), and all `256` memory words (`M[00]`–`M[FF]`)—before execution, after execution (e.g., verbose mode), or upon failure for debugging.

---

## 3. A Note of Caution: Buffer Overflow & Code Injection
In a **von Neumann (stored-program) machine**, there is no physical barrier separating data from executable instructions.

### The Vulnerability Scenario (Alice & Eve)
1. **Normal Program (Alice):** Reads an array of data from `StdIn` into memory starting at address `80`, stopping on sentinel `0000`.
2. **Malicious Input (Eve):** Supplies an input stream longer than the remaining memory (`80`–`FE`)—e.g., repeated `8888` words—without sending `0000`.
3. **Address Wraparound & Overwrite:**
   - Writing past `FE` hits `FF` (printing to `StdOut`), then **wraps around** (8-bit address overflow) back to `00`–`0F` and eventually `10+` (where the program instructions live!).
   - Eve can overwrite the program's instructions or return/branch targets with arbitrary machine code, hijacking control of the machine.

> [!danger] Security Takeaway
> Unchecked array bounds in a stored-program architecture allow data to corrupt control flow (**buffer overflow attack**). Modern systems mitigate this via runtime bounds checking, non-executable data memory protections (NX bit), and address space layout randomization.

---

## 4. Programs That Process Programs
Because programs are stored as numbers/text, a program can take *another program* as input or produce one as output:
- **Simulators / Virtual Machines:** Take a machine-language program as input and execute it (e.g., `TOY.java`, JVM).
- **Assemblers & Compilers:** Translate human-readable assembly or high-level source into machine code.
- **Bootstrapping:** Writing a simulator (or compiler) for TOY *in TOY itself*. Once a machine exists, it can simulate enhanced variants of itself (adding debugging or new features) without building new hardware.

---

## 5. The TOY Family of Computers
TOY represents a scalable architectural model across layers of abstraction:
- **TOY-8:** A stripped-down 8-bit model (`16` 8-bit memory words, `1` register, `8` instructions) small enough to build directly out of Boolean logic gates.
- **TOY:** 16-bit data/instructions, `256` words, `16` registers, `16` opcodes—large enough for real algorithms yet simple enough that its entire Java simulator fits in ~100 lines.
- **Real Architectures (32-bit / 64-bit):** Expand word size, register count, memory capacity, and opcode richness (floating point, privilege modes, interrupts), while preserving the exact same fetch–increment–execute core.
