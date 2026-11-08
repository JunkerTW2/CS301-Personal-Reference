# Complexity and Intractability
#cs #theory-of-computation #complexity #p-vs-np #intractability #sat #np-completeness #reference

---

## 🧭 The Core Question
> **"Given that a problem IS solvable, can it be solved in POLYNOMIAL TIME ($P$), or does it take EXPONENTIAL TIME ($NP$)?"**

This file covers the boundary between what is **practical** and what is **intractable** in the physical universe.

*Related Note:* [[TOC - 1. Computability and Decidability]]

---

## 1. The Foundations of Intractability

* **Problem Size ($n$):** The number of **bits** required to encode the input.
* **Worst-Case Guarantee:** Algorithms are classified by their worst possible performance on inputs of size $n$.

### Polynomial vs. Exponential Bounds

| Class | Math Definition | Asymptotic Examples | Practical Status |
| :--- | :--- | :--- | :--- |
| **Polynomial Time** | $T(n) \le a \cdot n^b$ for all inputs | $O(n), O(n \log n), O(n^2), O(n^3)$ | **Tractable / "Easy"** |
| **Exponential Time** | $T(n) \ge 2^{a \cdot n^b}$ for infinite inputs | $O(1.5^n), O(2^n), O(n!)$ | **Intractable / "Hopeless"** |

### 🌍 The Earth-Supercomputer Reality Check
Why faster hardware **cannot** defeat exponential growth:
* Pave the **entire surface of the Earth** with supercomputers ($10^{18}$ square inches).
* Run them continuously for the **lifetime of the Earth** ($10^{17}$ seconds).
* Total instructions executed: $\approx \mathbf{10^{51}}$ operations.
* A standard deck of 52 cards has $52! \approx \mathbf{10^{68}}$ shuffles.
* **Conclusion:** Hardware speedups are utterly powerless against exponential growth.

### The Extended Church-Turing Thesis
> Any computation that can be performed **efficiently** (polynomial time) on *any* physically realizable device can also be performed efficiently on a Turing Machine.

---

## 2. The Satisfiability Spectrum (The Fine Line)

A razor-thin boundary separates polynomial problems from exponential problems:

```text
    POLYNOMIAL (Easy)                       EXPONENTIAL (Intractable)
┌───────────────────────┐               ┌────────────────────────┐
│ 1. Linear Equations   │               │ 3. Integer Linear (ILP)│
│    (Gaussian Elim.)   │               │    (Variables = 0 or 1)│
└───────────┬───────────┘               └───────────▲────────────┘
            │                                       │
            ▼                                       │
┌───────────────────────┐               ┌───────────┴────────────┐
│ 2. Linear Inequalities│               │ 4. Boolean SAT         │
│    (Linear Prog. / LP)│               │    (Variables = T / F) │
└───────────────────────┘               └────────────────────────┘
```

1. **Linear Equations (Gaussian Elimination):** Real numbers, equalities $\rightarrow$ **$P$ (Polynomial)**.
2. **Linear Inequalities (Linear Programming / LP):** Real numbers, inequalities $\rightarrow$ **$P$ (Polynomial)** via Simplex/Ellipsoid.
3. **Integer Linear Programming (0/1 ILP):** Same as LP, but variables must be integers ($0$ or $1$) $\rightarrow$ **NP-Complete (Exponential)**.
4. **Boolean Satisfiability (SAT):** Boolean variables connected by AND/OR/NOT $\rightarrow$ **NP-Complete (Exponential)**.

### Other Classic "Twins"
* **Shortest Path (BFS):** Polynomial ($P$) $\longleftrightarrow$ **Longest Simple Path:** Intractable (NP-Complete).
* **Multiplication ($n^2$):** Polynomial ($P$) $\longleftrightarrow$ **Factorization (RSA):** Believed Intractable.

---

## 3. Search Problems & The Classes $P$ and $NP$

> [!info] Definition: Search Problem
> A problem where, if someone hands you a proposed solution, you can **CHECK** whether it is valid in **polynomial time**.

* **Class $P$:** Search problems where you can **FIND** a solution in polynomial time.
  * *(e.g., Sorting, Shortest Path, Linear Equations)*
* **Class $NP$:** The set of **ALL search problems**!
  * Problems where you can **CHECK** a solution in polynomial time.
  * *(e.g., Sudoku, SAT, 0/1 ILP, Factorization, Longest Path)*

> [!warning] Name Gotcha
> **NP does NOT mean "Non-Polynomial".**  
> It stands for **Nondeterministic Polynomial** (a nondeterministic machine could "guess" the correct answer and verify it in polynomial time).

---

## 4. The Million-Dollar Question: Does $P = NP$?

$$\text{Does } P = NP? \quad \iff \quad \text{"Is FINDING a solution as easy as CHECKING a solution?"}$$

```text
       UNIVERSE 1: P ≠ NP (Believed True)              UNIVERSE 2: P = NP (Shocking)
           ┌──────────────────────┐                     ┌──────────────────────┐
           │      NP (Search)     │                     │                      │
           │  ┌────────────────┐  │                     │       P  =  NP       │
           │  │   P (Solved)   │  │                     │                      │
           │  └────────────────┘  │                     │                      │
           └──────────────────────┘                     └──────────────────────┘
```

* **If $P \ne NP$:** There are search problems that are fundamentally impossible to solve in polynomial time (brute force is required).
* **If $P = NP$:** Efficient algorithms exist for SAT, ILP, protein folding, cryptography cracking, etc. We just haven't discovered them yet.

---

## 5. Polynomial-Time Reductions ($A \le_P B$)

> [!info] Definition: $A \le_P B$
> Problem $A$ **polynomial-time reduces** to Problem $B$ if an instance of $A$ can be solved using a **polynomial number of calls to $B$**, plus polynomial work outside the calls.

### The Two Golden Rules:
1. **If $B \in P \implies A \in P$** *(If $B$ is easy, $A$ is easy).*
2. **If $A$ is intractable $\implies B$ is intractable** *(If $A$ requires exponential time, $B$ must too).*
3. **Transitivity:** If $A \le_P B$ and $B \le_P C$, then $A \le_P C$.

---

## 6. NP-Completeness (The Hardest Problems in $NP$)

> [!danger] Definition: NP-Complete
> A problem $B$ is **NP-Complete** if:
> 1. $B \in NP$ (it is a search problem; solutions can be checked in poly time).
> 2. **EVERY problem in $NP$ polynomial-time reduces to $B$** ($\forall A \in NP, \; A \le_P B$).

**The Consequence:**  
If *any single* NP-complete problem can be solved in polynomial time, then **$P = NP$** and *every* search problem can be solved in polynomial time!

### The Cook-Levin Theorem (1971)
* Proved that **Boolean Satisfiability (SAT)** is **NP-complete**.
* It was the very first problem proven to have this property.

### Karp’s 21 Reductions (1972)
Richard Karp used transitivity to show that dozens of famous problems across science are secretly all the same difficulty by reducing SAT down the chain:

$$\text{SAT} \;\le_P\; \text{3-SAT} \;\le_P\; \text{0/1 ILP} \;\le_P\; \text{Vertex Cover} \;\le_P\; \dots$$

```text
                  ┌───► 0/1 ILP
                  │
   SAT ──► 3-SAT ─┼───► Clique ──► Vertex Cover ──► Set Cover
                  │
                  └───► Hamiltonian Cycle ──► Traveling Salesperson (TSP)
```

---

## 7. Coping with NP-Completeness in Industry

When an engineer proves their problem is NP-complete, they don't give up. They **relax one of three requirements**:

1. **Abandon Optimality (Approximation Algorithms):**  
   Find a solution that is guaranteed to be within $2\times$ or $1.5\times$ of the optimal answer in polynomial time.
2. **Abandon Worst-Case Guarantees (SAT Solvers / Heuristics):**  
   Modern industrial SAT solvers (using conflict-driven clause learning) can solve real-world instances with millions of variables in seconds, even though their *worst-case* theoretical runtime is still exponential ($O(2^n)$).

---

## 🚨 Exam Traps & Flashcards

1. **"Is an NP-Complete problem undecidable?"**  
   $\rightarrow$ **NO.** It is completely solvable (e.g., checking all $2^n$ combinations). It is just *slow / intractable*.
2. **"Does $P \subseteq NP$?"**  
   $\rightarrow$ **YES.** Any problem that can be solved in polynomial time can obviously have its solution checked in polynomial time.
3. **"If someone proves SAT can be solved in $O(n^3)$ time, what happens?"**  
   $\rightarrow$ **$P = NP$ immediately.** All NP-complete problems become solvable in polynomial time.
