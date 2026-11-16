# Computer Architecture & Encoding

---

## Part 1: Bits, Numbers & Characters

### The Core Rule of Computation
> **Bits have NO inherent meaning.** 
> A sequence of bits (like `0100 0001`) can be an integer, a floating-point number, a piece of an image, or a letter. The **context** (the program reading it) decides what it means.

### Character Math
* **$n$ bits** can uniquely represent **$2^n$** different characters:
  * 4 bits = 16 values
  * 7 bits = 128 values (ASCII)
  * 8 bits (1 byte) = 256 values
  * 16 bits (2 bytes) = 65,536 values

### ASCII vs. Unicode vs. UTF-8
* **ASCII (1960s):** 
  * 7-bit standard (128 characters).
  * Encodes English alphabet, digits, punctuation, and control characters (e.g., `CR` for carriage return, `BS` for backspace).
  * Stored in modern machines as an 8-bit byte with a leading `0`.
* **Unicode (Modern):** 
  * 21-bit space supporting over 1 million characters across all world languages and emojis.
* **UTF-8 (Implementation of Unicode):**
  * **Variable-width encoding** (1 to 4 bytes per character).
  * **Backward compatible with ASCII:** English letters still only take **1 byte** (starts with a `0xxxxxxx` bit pattern).
* **Displaying Characters:** 
  * ASCII/Unicode only stores the **ID number** (e.g., `'A'` = 65).
  * The **Font file** holds the geometric blueprint that draws the pixels on your screen.

---

## Part 2: Hexadecimal (Base-16) Quick Guide

* One hex digit represents **4 bits** (a "nibble").
* Two hex digits represent **8 bits** (1 byte: `00` to `FF`).
* Four hex digits represent **16 bits** (1 TOY word: `0000` to `FFFF`).

### The Digits
$$\text{0, 1, 2, 3, 4, 5, 6, 7, 8, 9, A (10), B (11), C (12), D (13), E (14), F (15)}$$

### Rollover Rules (Place Values: Sixteens | Ones)
* `0E` (14) $\to$ `0F` (15) $\to$ **`10`** (16) $\to$ **`11`** (17)
* `1E` (30) $\to$ `1F` (31) $\to$ **`20`** (32) $\to$ **`21`** (33)

---

## Part 3: The TOY Machine Hardware

| Component | Size | What it holds / Does |
| :--- | :--- | :--- |
| **Main Memory** | 256 words $\times$ 16 bits | Holds **both** program instructions and data values. Addresses run from `00` to `FF`. |
| **Registers** | 16 registers $\times$ 16 bits | Ultra-fast scratchpad variables: `R[0]` to `R[F]`. <br>⚠️ **Rule:** `R[0]` is **always hardwired to `0000`**! |
| **ALU** | — | **Arithmetic Logic Unit:** The calculator engine (math, shifts, comparisons). |
| **PC** | 8 bits (2 hex digits) | **Program Counter:** Stores the *address* of the *next* instruction to run. |
| **IR** | 16 bits (4 hex digits)| **Instruction Register:** Stores the *instruction currently executing*. |

### The Heartbeat: Fetch-Increment-Execute Cycle
1. **Fetch:** Copy the 16-bit word at memory address `PC` into the `IR`.
2. **Increment:** Add 1 to the `PC` (`PC = PC + 1`).
3. **Execute:** Run the command in the `IR`.

---

## Part 4: TOY Instruction Reference

Every instruction is a **16-bit word** written as **4 hex digits**:

### 1. Register Format (RR) — `[op][d][s][t]`
*Used for register-to-register operations: `R[d] = R[s] operation R[t]`*

| Opcode | Command | Meaning / Pseudo-code |
| :---: | :--- | :--- |
| **`1`** | **Add** | `R[d] = R[s] + R[t]` |
| **`2`** | **Subtract** | `R[d] = R[s] - R[t]` |
| **`3`** | **Bitwise AND** | `R[d] = R[s] & R[t]` |
| **`4`** | **Bitwise XOR** | `R[d] = R[s] ^ R[t]` |
| **`5`** | **Shift Left** | `R[d] = R[s] << R[t]` |
| **`6`** | **Shift Right** | `R[d] = R[s] >> R[t]` (arithmetic, preserves sign) |

### 2. Address Format (A) — `[op][d][addr]`
*Used for moving data or branching: `addr` is an 8-bit hex memory address (`00`–`FF`)*

| Opcode | Command | Meaning / Pseudo-code |
| :---: | :--- | :--- |
| **`7`** | **Load Address** | `R[d] = addr` *(Puts the constant number `addr` into `R[d]`)* |
| **`8`** | **Load** | `R[d] = M[addr]` *(Copies value from memory into register)* |
| **`9`** | **Store** | `M[addr] = R[d]` *(Copies value from register into memory)* |
| **`C`** | **Branch Zero** | `if (R[d] == 0) PC = addr` |
| **`D`** | **Branch Positive** | `if (R[d] > 0) PC = addr` |

### 3. Control Instructions
| Opcode | Command | Meaning |
| :---: | :--- | :--- |
| **`0`** | **Halt** | Stop the machine (`0000`). |

---

## Part 5: Programming Tricks & Patterns in TOY

* **Unconditional Jump (Always jump to line `xx`):**
  * `C0xx` $\to$ Because `R[0]` is always zero, this jump *always* executes.
* **If ($A == B$) Jump to line `xx`:**
  * Subtract them: `21AB` (`R[1] = R[A] - R[B]`)
  * Check for zero: `C1xx` (If `R[1] == 0`, jump to `xx`)
* **If ($A > B$) Jump to line `xx`:**
  * Subtract them: `21AB` (`R[1] = R[A] - R[B]`)
  * Check for positive: `D1xx` (If `R[1] > 0`, jump to `xx`)

---

## Part 6: Big Conceptual Takeaways

* **Von Neumann Architecture:** Both instructions (code) and variables (data) live side-by-side in the **same main memory**.
* **Self-Modifying Code:** Because code is stored as numbers, a program can alter its own memory slots to rewrite its own instructions on the fly.
* **Security Risk:** Because hardware cannot distinguish data from code, malicious instructions hidden inside downloaded data files can trick the computer into running them if the `PC` is redirected there.
