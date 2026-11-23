---
tags:
  - cs/architecture
  - cs/toy-machine
  - data-structures
  - arrays
  - linked-lists
---

# Arrays and Linked Structures in TOY

Part of [[00 - TOY Machine MOC]] | Related: [[TOY Instruction Set Reference]].

## 1. Arrays via Indirect Addressing
TOY has no built-in array syntax (`a[i]`). Instead, arrays are stored as **contiguous blocks of memory** and accessed using:
- **`7` (load address):** `R[d] <- addr` (loads base address `a`)
- **`A` (load indirect):** `R[d] <- M[R[t]]` (reads from computed pointer)
- **`B` (store indirect):** `M[R[t]] <- R[d]` (writes to computed pointer)

### Address Arithmetic (`a[i]`)
If array `a` begins at base address `a` (stored in `R[A]`) and index `i` is in `R[I]`:
1. Compute element address: `R[P] <- R[A] + R[I]` (`1PAI`).
2. Read `a[i]`: `R[V] <- M[R[P]]` (`AV0P`).
3. Write `a[i]`: `M[R[P]] <- R[V]` (`BV0P`).

> [!tip] Passing Arrays to Functions
> Just like high-level languages pass an array reference rather than copying every element, a TOY caller places the **base address** (via opcode `7`) and **length** into agreed-upon registers before calling a function with `F`.

```text
; Reverse-print pattern: read into M[30..30+n-1], then print backwards
10: 7101   R[1] <- 0001      ; constant 1
11: 7A30   R[A] <- 0030      ; base address of array a[]
12: 7I00   R[I] <- 0000      ; i = 0 (count)
; ... loop: read into R[V], compute R[P] = R[A] + R[I], BVP0 (M[R[P]] <- R[V]), i++ ...
; ... loop backwards: i--, compute R[P] = R[A] + R[I], AV0P (R[V] <- M[R[P]]), write R[V] ...
```

---

## 2. Linked Structures in Machine Language
In high-level languages, a linked list node holds an item and an object reference `next`. At the machine level, a **reference (pointer) is simply a memory address**.

### Node Layout in TOY Memory
A singly-linked list node occupies **two consecutive memory words**:
- `M[nodeAddr + 0]`: **data item**
- `M[nodeAddr + 1]`: **`next` pointer** (memory address of the next node, or `0000` for `null`)

### Traversing a Linked List (`x = x.next`)
Suppose register `R[X]` holds the address of the current node (e.g., `C0`), and `R[1]` holds `0001`:
1. **Check for `null`:** `CX exit` (`if (R[X] == 0) goto exit`)
2. **Access `x.item`:** `AV0X` (`R[V] <- M[R[X]]`)
3. **Compute address of `x.next` field:** `1PX1` (`R[P] <- R[X] + 1`)
4. **Advance `x = x.next`:** `AX0P` (`R[X] <- M[R[P]]`)

```text
Memory Snapshot of a Linked List (Head = C0):
Addr | Value | Meaning
-----+-------+-----------------------------
C0   | 000A  | Node 1 item = 0x000A
C1   | 00C4  | Node 1 next = addr C4
...
C4   | 0014  | Node 2 item = 0x0014
C5   | 0000  | Node 2 next = null (0000)
```

> [!info] Arrays vs. Linked Structures at Hardware Level
> - **Arrays** require contiguous memory and compute offsets via addition (`base + i`).
> - **Linked structures** scatter nodes anywhere in memory and follow explicit address links (`M[node + 1]`), enabling dynamic insertion/deletion without shifting elements.

---

## 3. Why Learn Machine-Language Programming?
1. **Demystifying High-Level Languages:** Reveals how high-level constructs (loops, functions, object references, arrays) map directly to hardware operations.
2. **Performance & Hardware Awareness:** Explains the cost of memory indirection, register limits, and why certain idioms (e.g., bit shifts, contiguous arrays) map cleanly to CPU instructions.
3. **Specialized Domains & Systems:** Essential for writing compilers, operating systems, device drivers, embedded systems, and hand-tuned multimedia/scientific kernels.
4. **Foundation for Architecture & Theory:** Connects programming to [[TOY Virtual Machine Concepts|virtual machines]], hardware circuit design, and computability theory.
