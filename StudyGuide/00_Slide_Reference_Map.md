# Where to Find Each Topic in the Slides

Use this as a roadmap while studying. Open the study guide file for a topic,
then open the matching lecture PDF to see the professor's original examples.

Page numbers = slide numbers (each PDF page is one slide).

---

## 01 — Three Design Styles (Dataflow, Behavioral, Structural)

| Topic                                | Lecture         | Slides      |
|--------------------------------------|-----------------|-------------|
| Overview of all 3 styles             | **Lecture 1**   | 27-33       |
| Dataflow intro + half adder example  | **Lecture 1**   | 27-28       |
| Behavioral intro + half adder        | **Lecture 1**   | 29-31       |
| Structural intro + half adder        | **Lecture 1**   | 32-33       |
| Mixed-type description               | **Lecture 1**   | 34          |
| `assign` (continuous assignment)     | **Lecture 2**   | 18-20       |
| MUX with `assign` (ternary)          | **Lecture 2**   | 21          |
| MUX with `always` + `case`           | **Lecture 2**   | 23-24       |
| Dataflow description (detailed)      | **Lecture 4**   | 5-16        |
| AND-OR block example (dataflow)      | **Lecture 4**   | 6-9         |
| Behavioral description (detailed)    | **Lecture 4**   | 27-31       |
| `always` block rules                 | **Lecture 4**   | 32          |
| `initial` block rules                | **Lecture 4**   | 33          |
| Structural: module instantiation     | **Lecture 6**   | 7-9         |
| Structural: 4-bit adder example      | **Lecture 6**   | 8-9         |

---

## 02 — Concurrent vs Sequential Execution

| Topic                                         | Lecture         | Slides      |
|-----------------------------------------------|-----------------|-------------|
| Concurrent statements overview                | **Lecture 1**   | 27-28       |
| "Order doesn't matter" demo                   | **Lecture 4**   | 7-9         |
| Concurrent signal assignment (summary)        | **Lecture 4**   | 10          |
| Simulation of AND-OR (event-based execution)  | **Lecture 4**   | 11          |
| Concurrent implementation constructs          | **Lecture 4**   | 22-24       |
| `generate for` (concurrent replication)       | **Lecture 4**   | 23-24       |
| Combinational vs sequential circuits          | **Lecture 4**   | 29          |
| Sequential statements inside `always`         | **Lecture 4**   | 34, 39      |
| `if` statements (sequential)                  | **Lecture 4**   | 35          |
| `case` statements (sequential)                | **Lecture 4**   | 40-44       |

---

## 03 — Blocking vs Non-Blocking Assignments

| Topic                                         | Lecture         | Slides      |
|-----------------------------------------------|-----------------|-------------|
| Blocking vs non-blocking overview             | **Lecture 5**   | 24-25       |
| Blocking example with explanation             | **Lecture 5**   | 25          |
| Non-blocking example with explanation         | **Lecture 5**   | 25          |
| Side-by-side comparison example               | **Lecture 5**   | 26-27       |

---

## 04 — Delays in Concurrent Execution

| Topic                                         | Lecture         | Slides      |
|-----------------------------------------------|-----------------|-------------|
| Constant declaration + `#` delay intro        | **Lecture 4**   | 12          |
| AND-OR block WITH delay code                  | **Lecture 4**   | 13          |
| Simulation of AND-OR with delay (timeline)    | **Lecture 4**   | 14-16       |
| MUX 2x1 with delay (different paths)          | **Lecture 4**   | 17-18       |
| Simulation of MUX with delay                  | **Lecture 4**   | 19-21       |

---

## 05 — Loops in Verilog

| Topic                                         | Lecture         | Slides      |
|-----------------------------------------------|-----------------|-------------|
| Loop overview (for, while, repeat, forever)   | **Lecture 5**   | 16-18       |
| Loop summary table                            | **Lecture 5**   | 19          |
| More loop examples                            | **Lecture 5**   | 20          |
| Wait statement                                | **Lecture 5**   | 13          |
| Clock generation with wait                    | **Lecture 5**   | 14          |

---

## 06 — Common Mistakes in Verilog

| Topic                                               | Lecture         | Slides  |
|------------------------------------------------------|-----------------|---------|
| Missing semicolon, wrong case, missing `assign`, etc | **Lecture 3**   | 19      |
| `assign` inside `always` block mistake               | **Lecture 3**   | 20      |
| Same `reg` in multiple `always` blocks               | **Lecture 3**   | 21      |
| Incomplete sensitivity list                          | **Lecture 3**   | 23      |
| `assign` cannot go inside `always` (noted in MUX)    | **Lecture 2**   | 23      |

---

## 07 — How to Write a Testbench

| Topic                                         | Lecture         | Slides      |
|-----------------------------------------------|-----------------|-------------|
| Testbench overview (what it is)               | **Lecture 1**   | 49-50       |
| Testbench for half adder (full code)          | **Lecture 1**   | 51          |
| Testbench structure (no ports, reg/wire)      | **Lecture 3**   | 25-26       |
| Testbench for half adder (Lecture 3 version)  | **Lecture 3**   | 27          |
| Clock generation (always, forever)            | **Lecture 5**   | 14          |
| `initial` block explanation                   | **Lecture 4**   | 33          |

---

## 08 — Shift Operators ( << >> <<< >>> )

| Topic                                         | Lecture         | Slides      |
|-----------------------------------------------|-----------------|-------------|
| All 4 shift operators with examples table     | **Lecture 3**   | 17          |

This is THE key slide — it has the table showing:
- `A << 1`: 1010 → 0100 (fills with 0)
- `A >> 1`: 1010 → 0101 (fills with 0)
- `A <<< 1`: 1001 → 0011 (fills with LSB)
- `A >>> 1`: 1010 → 1101 (fills with MSB)

---

## 10 — D-Latch and Flip-Flops (D-FF, JK-FF)

| Topic                                         | Lecture         | Slides      |
|-----------------------------------------------|-----------------|-------------|
| D-Latch example (behavioral)                  | **Lecture 4**   | 36-38       |
| JK flip-flop example (with case statement)    | **Lecture 5**   | 9-11        |

---

## 11 — case / casex / casez Statements

| Topic                                         | Lecture         | Slides      |
|-----------------------------------------------|-----------------|-------------|
| `case` statement syntax and MUX example       | **Lecture 4**   | 40          |
| `case`/`casex`/`casez` comparison (a=0,1,z,x) | **Lecture 4**  | 41-44       |
| `case`/`casex`/`casez` (same examples, detail) | **Lecture 5**  | 4-8         |

---

## 12 — Number Representation

| Topic                                         | Lecture         | Slides      |
|-----------------------------------------------|-----------------|-------------|
| Number format (`N'Bvalue`) and rules          | **Lecture 1**   | 36-45       |
| Number examples table                         | **Lecture 2**   | 4-12        |

---

## 13 — Vectors, Arrays, Memory, and Parameters

| Topic                                         | Lecture         | Slides      |
|-----------------------------------------------|-----------------|-------------|
| `wire` vs `reg` data types                    | **Lecture 2**   | 25-30       |
| `wire` vs `reg` (also in Lecture 3)           | **Lecture 3**   | 5           |
| Vectors (bit-select, part-select)             | **Lecture 3**   | 6-7         |
| Arrays and memory                             | **Lecture 3**   | 7-10        |
| Parameters                                    | **Lecture 3**   | 11-12       |

---

## 14 — Operators (Relational, Arithmetic, Concatenation)

| Topic                                         | Lecture         | Slides      |
|-----------------------------------------------|-----------------|-------------|
| Relational operators (`==`, `===`, etc.)      | **Lecture 3**   | 14-15       |
| Arithmetic operators (+, -, *, concatenation) | **Lecture 3**   | 16          |

---

## 15 — Wait Statement

| Topic                                         | Lecture         | Slides      |
|-----------------------------------------------|-----------------|-------------|
| Wait statement syntax                         | **Lecture 5**   | 13          |
| Clock generation with wait/always             | **Lecture 5**   | 14          |

---

## Other Midterm References

| Topic                                         | Lecture         | Slides      |
|-----------------------------------------------|-----------------|-------------|
| Structural: binding and port instantiation    | **Lecture 5**   | 28-31       |
| Structural: module instantiation              | **Lecture 6**   | 7-9         |
| Midterm topics list                           | **Lecture 6**   | 4           |
| Midterm format                                | **Lecture 6**   | 3           |

---

## Quick Lookup: "I need to review ___"

| I need to review...              | Go to                              |
|----------------------------------|------------------------------------|
| What `assign` does               | Lecture 2, slide 18-20             |
| What `always` does               | Lecture 4, slide 32                |
| What `initial` does              | Lecture 4, slide 33                |
| `wire` vs `reg`                  | Lecture 2, slides 25-30            |
| `if` statements                  | Lecture 4, slide 35                |
| `case`/`casex`/`casez`           | Lecture 5, slides 4-8              |
| Blocking vs non-blocking         | Lecture 5, slides 24-27            |
| Shift operators                  | Lecture 3, slide 17                |
| Common errors                    | Lecture 3, slides 19-23            |
| How to write a testbench         | Lecture 1 slide 49-51, Lec 3 25-27 |
| Loops                            | Lecture 5, slides 16-19            |
| Delays with `#`                  | Lecture 4, slides 12-21            |
| Structural description           | Lecture 6, slides 7-9              |
| Port mapping (explicit vs order) | Lecture 5, slides 28-31            |
| Number format (`8'hAB`)          | Lecture 1, slides 36-45            |
| D-Latch                          | Lecture 4, slides 36-38            |
| JK Flip-Flop                     | Lecture 5, slides 9-11             |
| `case`/`casex`/`casez`           | Lecture 5, slides 4-8              |
| Vectors / bit-select             | Lecture 3, slides 6-7              |
| Arrays and memory                | Lecture 3, slides 7-10             |
| Parameters                       | Lecture 3, slides 11-12            |
| `==` vs `===`                    | Lecture 3, slides 14-15            |
| Concatenation `{a,b}`            | Lecture 3, slide 16                |
| `wait` statement                 | Lecture 5, slide 13                |
