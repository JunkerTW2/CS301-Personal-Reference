Computability and Decidability
#cs #theory-of-computation #computability #turing-machines #halting-problem #reference

---

## 🧭 The Core Question
> **"Can an algorithm solve this problem AT ALL, even if given infinite time and infinite memory?"**

This file covers the boundary between what is **solvable** and what is **mathematically impossible** for any computer.

*Related Note:* [[TOC - 2. Complexity and Intractability]]

---

## 1. Universality & The Church-Turing Thesis

### The Universal Turing Machine (UTM)
* **Earlier Machines:** A DFA or basic Turing machine was hardwired to do only *one* specific job (e.g., check $a^n b^n$).
* **Alan Turing’s Breakthrough:** A Turing machine can take **both an algorithm AND its input data** as text on the tape:
  $$\text{Tape Input: } \langle M, w \rangle \quad (M = \text{description of machine}, w = \text{input data})$$
* **The Result:** Software was born. A single physical machine (the UTM) can simulate *any other* machine simply by reading its code.

### The Church-Turing Thesis
> [!quote] The Church-Turing Thesis
> **Every physically realizable computing device can be simulated by a Turing Machine.**
> There is no computer (supercomputer, quantum computer, alien technology) that can compute things a simple paper-tape Turing machine cannot.

---

## 2. Decidability vs. Computability

In formal theory, we classify problems based on whether a machine can guarantee an answer:

| Term | Meaning | Outcome on Valid Input | Outcome on Invalid Input |
| :--- | :--- | :--- | :--- |
| **Decidable (Computable)** | An algorithm exists that **always halts** and gives a correct YES/NO answer. | Halts & **Accepts** | Halts & **Rejects** |
| **Undecidable (Uncomputable)** | **No algorithm can exist** that guarantees a correct answer for all inputs without potentially looping forever. | May halt or run forever | May halt or run forever |

---

## 3. Unsolvability (The Wall of Impossibility)

### A. The Halting Problem ($HALT$)
* **The Question:** Given a program $P$ and an input $x$, does $P(x)$ eventually halt (finish running), or does it run forever in an infinite loop?
* **Alan Turing's Proof (1936):** **$HALT$ is UNDECIDABLE.** No general algorithm can ever solve it.

#### Turing’s Proof by Contradiction (The Logic Trap)
Assume a magic function `halts(P, x)` exists that returns `true` if $P(x)$ halts, and `false` if it loops forever.

Now write this paradox function:
```java
public void paradox(String programCode) {
    if (halts(programCode, programCode)) {
        while (true) { } // Loop forever on purpose!
    } else {
        return;          // Halt immediately!
    }
}
```

Now call: `paradox(paradox)`:
* If `halts` says it halts $\rightarrow$ it intentionally **loops forever**!
* If `halts` says it loops $\rightarrow$ it intentionally **halts**!
* **Contradiction:** The function `halts()` cannot exist.

---

### B. The Totality Problem ($TOTAL$)
* **The Question:** Does a given program $P$ halt on **EVERY SINGLE POSSIBLE input** in the universe?
* **Verdict:** **Undecidable.**
* **Hardness Comparison:** 
  * Halting asks about **one** input: Does $P(x)$ halt?
  * Totality asks about **infinite** inputs: Does $P$ halt for $x_1, x_2, x_3, \dots$?
  * Totality is mathematically strictly "more impossible" than the Halting Problem.

---

## 4. Computability Reductions ($A \le B$)

Reductions are the primary tool used to prove that a new problem is mathematically impossible:

> [!info] Definition: $A \le B$
> Problem $A$ **reduces to** Problem $B$ if you can solve $A$ by using $B$ as a helper function (subroutine).
> **Meaning:** Problem $B$ is **at least as hard** as Problem $A$.

```text
    [ Instance a of A ] ──► [ Transform to instance b ] ──► [ Solve B ] ──► [ Solution to a ]
```

### The Unsolvability Rule:
$$\text{If } A \le B \text{ and } A \text{ is UNDECIDABLE } \implies B \text{ MUST BE UNDECIDABLE.}$$

* *Why?* If $B$ were solvable, you could use it to solve $A$. But we already know $A$ is impossible!
* **How to prove a new problem $X$ is impossible:**  
  Reduce the **Halting Problem** to $X$ ($\text{HALT} \le X$).

---

## 🚨 Exam Traps & Flashcards

1. **"Is an undecidable problem just really slow?"**  
   $\rightarrow$ **NO.** It has nothing to do with speed. It is mathematically *impossible* to guarantee an answer, even with trillions of years.
2. **"Does a Turing Machine have infinite states?"**  
   $\rightarrow$ **NO.** The states are **finite**. The *tape* (memory) is infinite.
3. **"If $A \le B$, which problem is harder?"**  
   $\rightarrow$ **$B$ is at least as hard as $A$** ($A \le B$).
