---
tags:
  - cs/architecture
  - cs/java
  - cs/virtual-machine
  - implementation
---

# Implementing TOY in Java (`TOY.java`)

Part of [[00 - TOY Machine MOC]] | Concepts: [[TOY Virtual Machine Concepts]].

## 1. State Representation & Data-Type Mismatch
TOY uses **8-bit unsigned** addresses/`PC` and **16-bit two's complement** words. Java has no 8-bit unsigned type, and `short` (16-bit signed) promotes to `int` during arithmetic. Therefore, `TOY.java` stores state in Java `int` variables and enforces 8-bit / 16-bit semantics via **bit masking and casting**:

```java
private int pc;                 // 8-bit program counter (0x00..0xFF)
private int[] reg = new int[16];  // 16 16-bit registers
private int[] mem = new int[256]; // 256 16-bit memory words
```

---

## 2. Booting (Parsing `.toy` Files)
- Reads lines formatted as `addr: word` (two hex digits, colon, whitespace, four hex digits) using regular expressions, ignoring comments/blank lines.
- Parses hex strings into integers (`Integer.parseInt(hex, 16)`) and stores them into `mem[addr]`.
- Initializes `pc = 0x10` (unless overridden).

---

## 3. The Fetch–Increment–Execute Cycle

```java
while (true) {
    // 1. FETCH & INCREMENT
    int inst = mem[pc++];
    pc = pc & 0xFF;               // keep pc 8-bit (wrap FF -> 00)

    // 2. DECODE
    int op   = (inst >> 12) & 0xF;  // bits 15-12
    int d    = (inst >> 8)  & 0xF;  // bits 11-8
    int s    = (inst >> 4)  & 0xF;  // bits 7-4
    int t    =  inst        & 0xF;  // bits 3-0
    int addr =  inst        & 0xFF; // bits 7-0

    if (op == 0) break;             // halt

    // Enforce R[0] == 0 and handle StdIn (M[FF]) before execute
    // ...

    // 3. EXECUTE
    // (switch on op 1..15)
}
```

### Critical Bitwise & Semantic Details in `TOY.java`

> [!warning] Emulating 16-bit Hardware in 32-bit Java
> 1. **16-bit Truncation (`& 0xFFFF`):** After arithmetic/bitwise operations (`+`, `-`, `<<`), mask results so upper bits `16–31` do not leak into future instructions:
>    - `reg[d] = (reg[s] + reg[t]) & 0xFFFF;`
> 2. **Signed Right Shift (`6`):** Java `int >>` sign-extends bit 31, not bit 15. Cast `reg[s]` to `(short)` before shifting right, then mask `& 0xFFFF`:
>    - `reg[d] = ((short) reg[s] >> reg[t]) & 0xFFFF;`
> 3. **Branch if Positive (`D`):** Check signed 16-bit positivity via `(short) reg[d] > 0` (so `8000`–`FFFF` are correctly treated as negative, not positive 32-bit `int`s).
> 4. **8-bit Memory Address Masking (`& 0xFF`):** Indirect load/store (`A`, `B`) uses only the low 8 bits of `reg[t]` as the memory index: `reg[t] & 0xFF`.
> 5. **Enforcing `R[0] == 0`:** Reset `reg[0] = 0;` on every cycle (or after execute) so writes to register `0` are discarded.
> 6. **Memory-Mapped I/O (`0xFF`):**
>    - Before `8` (`load`) or `A` (`load indirect`), if the target address is `255` (`0xFF`), read a 4-digit hex integer from `StdIn` into `mem[255]`.
>    - After `9` (`store`) or `B` (`store indirect`), if the target address is `255` (`0xFF`), format and print `mem[255]` as 4 uppercase hex digits to `StdOut`.
