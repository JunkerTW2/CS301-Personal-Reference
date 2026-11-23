---
tags:
  - cs/architecture
  - cs/toy-machine
  - functions
  - io
---

# Functions and Standard I/O in TOY

Part of [[00 - TOY Machine MOC]] | Related: [[TOY Instruction Set Reference]].

## 1. Functions (Subroutines) in Machine Language
High-level languages provide named functions with parameters, local variables, and `return` statements. In TOY, functions are implemented by convention using **jump and link (`F`)** and **jump register (`E`)**.

### Call & Return Mechanism
Because `PC` is already incremented during the **fetch** phase (pointing to the instruction *immediately following* the call), `F` captures the exact return address:
1. **Calling a function at `addr`:**
   - `Fd addr` executes `R[d] <- PC; PC <- addr`.
   - Conventionally, a designated link register (e.g., `R[F]`) stores the return address.
2. **Returning from the function:**
   - `Ed00` executes `PC <- R[d]`.
   - Control resumes at the instruction directly after the original `Fd addr` call.

```text
; Example Calling Convention (Function at F0, Return addr in R[F])
10: 7A03   R[A] <- 0003          ; First argument in R[A]
11: 7B05   R[B] <- 0005          ; Second argument in R[B]
12: FFF0   R[F] <- PC; PC <- F0  ; Call function at F0 (R[F] = 13)
13: 9CFF   write R[C]            ; Resume here using return value in R[C]
14: 0000   halt

; Subroutine at F0
F0: ...                          ; Compute result into R[C]
F5: EF00   PC <- R[F]            ; Return to caller (addr 13)
```

> [!info] Register Conventions & Recursion Limits
> - **Parameter & Return Passing:** Caller and callee must agree on which registers hold arguments (e.g., `R[A]`, `R[B]`), return values (e.g., `R[C]`), and the link address (e.g., `R[F]`).
> - **Nested Calls / Recursion:** Calling a second function inside a function overwrites the link register unless the callee saves `R[F]` into memory (or onto a software-managed **call stack** using [[TOY Arrays and Linked Structures|indirect addressing]]).

---

## 2. Standard Output & Standard Input (Memory-Mapped I/O)
Rather than having dedicated `READ` or `PRINT` opcodes, TOY reserves the last memory address (`FF`) for **memory-mapped I/O**.

### Standard Output (`write`)
- **Direct (`9dFF`):** Writes the 16-bit hex value in `R[d]` to `StdOut`.
- **Indirect (`BdFt`):** If `R[t]` holds `00FF`, `M[R[t]] <- R[d]` also writes to `StdOut`.
- *Consequence:* Main memory locations `00`–`FE` store values, whereas `FF` acts as an output port.

### Standard Input (`read`)
- **Direct (`8dFF`):** Reads the next 4-digit hex integer from `StdIn` into `R[d]`.
- **Indirect (`AdFt`):** If `R[t]` holds `00FF`, `R[d] <- M[R[t]]` reads from `StdIn`.
- **Sentinel-Controlled Loops:** Because standard input is an unbounded stream (not limited to 256 words of RAM), programs can process arbitrarily large datasets by reading until a sentinel value (such as `0000`) appears:

```text
; Read integers until 0000 and compute sum
10: 7C00   R[C] <- 0000          ; sum = 0
11: 8AFF   read R[A]             ; read next input
12: CA15   if (R[A] == 0) goto 15
13: 1CCA   R[C] <- R[C] + R[A]
14: C011   goto 11
15: 9CFF   write R[C]            ; output sum
16: 0000   halt
```

> [!note] Computational Significance
> Streaming via `M[FF]` means TOY is **not** restricted to inputs that fit inside its 256-word memory (e.g., computing running sums or printing arbitrarily many sequence terms).
