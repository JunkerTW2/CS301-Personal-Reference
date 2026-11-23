---
tags:
  - cs/architecture
  - cs/toy-machine
  - reference
aliases:
  - TOY Reference Card
  - TOY ISA
---

# TOY Instruction Set Reference

Part of [[00 - TOY Machine MOC]].

## Machine Specifications
- **Main Memory:** `256` words (`00` through `FF`), each **16 bits** wide.
- **Registers:** `16` general-purpose registers (`0` through `F`), each **16 bits** wide (two's complement).
- **Program Counter (`PC`):** **8 bits** wide (`00`–`FF`), holding the address of the next instruction (default start: `10`).

> [!warning] Hardwired Rules
> 1. **`R[0]` is always `0000`:** Reading `R[0]` always yields `0`. Any instruction attempting to write to `R[0]` leaves it unchanged (often used to discard results or create unconditional branches like `C015`).
> 2. **Memory-Mapped I/O at `FF`:** Loading from `M[FF]` reads from Standard Input; storing to `M[FF]` writes to Standard Output.

## Instruction Formats (16-bit)

| Format | Bits 15–12 | Bits 11–8 | Bits 7–4 | Bits 3–0 | Opcodes |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **RR** (Register-Register) | `opcode` | `d` (dest) | `s` (src 1) | `t` (src 2) | `0`–`6`, `A`–`B` |
| **A** (Address) | `opcode` | `d` (reg) | `addr` (8-bit address/constant) | *(part of `addr`)* | `7`–`9`, `C`–`F` |

## Complete Opcode Table

### Arithmetic & Bitwise Logical (Format RR)
| Opcode | Name | Pseudocode | Notes |
| :---: | :--- | :--- | :--- |
| `1` | **add** | `R[d] <- R[s] + R[t]` | 16-bit two's complement addition (wraps on overflow) |
| `2` | **subtract** | `R[d] <- R[s] - R[t]` | 16-bit two's complement subtraction |
| `3` | **and** | `R[d] <- R[s] & R[t]` | Bitwise AND |
| `4` | **xor** | `R[d] <- R[s] ^ R[t]` | Bitwise XOR |
| `5` | **shift left** | `R[d] <- R[s] << R[t]` | Shifts left by `R[t]` bits, pads right with `0` |
| `6` | **shift right** | `R[d] <- R[s] >> R[t]` | **Arithmetic (signed) shift:** preserves sign bit (bit 15) |

### Memory–Register Transfer
| Opcode | Format | Name | Pseudocode | Notes |
| :---: | :---: | :--- | :--- | :--- |
| `7` | A | **load address** | `R[d] <- addr` | Zero-extends 8-bit `addr` (`0000`–`00FF`) into `R[d]` |
| `8` | A | **load** | `R[d] <- M[addr]` | Direct addressing; `addr = FF` reads `StdIn` |
| `9` | A | **store** | `M[addr] <- R[d]` | Direct addressing; `addr = FF` writes `StdOut` |
| `A` | RR | **load indirect** | `R[d] <- M[R[t]]` | Uses low 8 bits of `R[t]` as address (`s` unused) |
| `B` | RR | **store indirect** | `M[R[t]] <- R[d]` | Uses low 8 bits of `R[t]` as address (`s` unused) |

### Control Flow
| Opcode | Format | Name | Pseudocode | Notes |
| :---: | :---: | :--- | :--- | :--- |
| `0` | RR | **halt** | `halt` | Stops fetch–increment–execute cycle (`d, s, t` unused) |
| `C` | A | **branch zero** | `if (R[d] == 0) PC <- addr` | Unconditional `goto addr` when `d = 0` (`C0xx`) |
| `D` | A | **branch positive** | `if (R[d] > 0) PC <- addr` | True for two's complement `0001`–`7FFF` |
| `E` | RR | **jump register** | `PC <- R[d]` | Used to return from functions (`s, t` unused) |
| `F` | A | **jump and link** | `R[d] <- PC; PC <- addr` | Saves return address in `R[d]`, then jumps to `addr` |

> [!tip] Where Sign Matters
> Two's complement interpretation only affects **two** instructions:
> - **`6` (shift right):** arithmetic shift (sign-extends leftmost bit).
> - **`D` (branch positive):** treats `0001`–`7FFF` as positive and `8000`–`FFFF` as negative (`<= 0`).
