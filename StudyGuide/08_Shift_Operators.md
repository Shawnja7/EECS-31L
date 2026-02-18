# Shift Operators ( << >> <<< >>> )

This is a heavily tested topic. Pay close attention to the examples.

---

## The 4 Shift Operators

| Operator | Name                    | What it does                          |
|----------|-------------------------|---------------------------------------|
| `<<`     | Logical Left Shift      | Shifts bits LEFT, fills right with 0  |
| `>>`     | Logical Right Shift     | Shifts bits RIGHT, fills left with 0  |
| `<<<`    | Arithmetic Left Shift   | Shifts left, fills right with LSB     |
| `>>>`    | Arithmetic Right Shift  | Shifts right, fills left with MSB     |

---

## How Shifting Works (Visual)

Think of your bits as boxes on a conveyor belt.

**Left shift (`<<`):**
Bits move LEFT. Whatever falls off the left side is GONE.
Empty spots on the right get filled with 0.

```
Before:    [1][0][1][0]
           ← ← ← ← ←     (shift left by 1)
After:     [0][1][0][0]

The '1' on the far left fell off. A '0' filled in on the right.
```

**Right shift (`>>`):**
Bits move RIGHT. Whatever falls off the right side is GONE.
Empty spots on the left get filled with 0.

```
Before:    [1][0][1][0]
            → → → → →     (shift right by 1)
After:     [0][1][0][1]

The '0' on the far right fell off. A '0' filled in on the left.
```

---

## Worked Examples from Your Quiz

### Example 1: Left Shift by 3

```verilog
reg [3:0] a;
a = 4'b1111;
a << 3;
```

Step by step:
```
Start:      1  1  1  1
Shift << 1: 1  1  1  0      (rightmost gets 0, leftmost 1 falls off)
Shift << 2: 1  1  0  0      (another 0 fills right, another bit falls off)
Shift << 3: 1  0  0  0      (one more)
```

**Answer: `4'b1000`**

---

### Example 2: Right Shift by 3

```verilog
reg [3:0] b;
b = 4'b1111;
b >> 3;
```

Step by step:
```
Start:      1  1  1  1
Shift >> 1: 0  1  1  1      (leftmost gets 0, rightmost 1 falls off)
Shift >> 2: 0  0  1  1      (another 0 fills left)
Shift >> 3: 0  0  0  1      (one more)
```

**Answer: `4'b0001`**

---

### Example 3: Left Shift by 1

```verilog
reg [3:0] d;
d = 4'b1010;
d << 1;
```

```
Start:      1  0  1  0
Shift << 1: 0  1  0  0      (1 fell off left, 0 filled right)
```

**Answer: `4'b0100`**

---

### Example 4: Right Shift by 1

```verilog
reg [3:0] e;
e = 4'b1010;
e >> 1;
```

```
Start:      1  0  1  0
Shift >> 1: 0  1  0  1      (0 fell off right, 0 filled left)
```

**Answer: `4'b0101`**

---

### Example 5: Arithmetic Right Shift ( >>> ) — SIGNED

This is the tricky one! `>>>` fills with the SIGN BIT (the leftmost bit).

```verilog
reg signed [3:0] c;
c = 4'b1010;          // sign bit = 1 (negative number)
c >>> 1;
```

```
Start:      1  0  1  0       (sign bit = 1)
Shift >> 1: 1  1  0  1       (fills with SIGN BIT = 1, not 0!)
            ^
            sign bit copied in
```

**Answer: `4'b1101`**

**Compare to logical right shift:**
```
c >> 1  → 0  1  0  1         (fills with 0)
c >>> 1 → 1  1  0  1         (fills with sign bit = 1)
```

---

### Example 6: Arithmetic Right Shift — Positive Number

```verilog
reg signed [3:0] f;
f = 4'b0110;          // sign bit = 0 (positive number)
f >>> 1;
```

```
Start:      0  1  1  0       (sign bit = 0)
Shift >> 1: 0  0  1  1       (fills with SIGN BIT = 0)
            ^
            sign bit = 0, so same as logical shift
```

**Answer: `4'b0011`**

When the sign bit is 0, `>>>` gives the same result as `>>`.
The difference only shows when the sign bit is 1.

---

## Master Reference Table

| Expression               | Input  | Result | Fill with | Why                          |
|--------------------------|--------|--------|-----------|------------------------------|
| `4'b1111 << 3`           | `1111` | `1000` | 0s        | Logical left, fill right     |
| `4'b1111 >> 3`           | `1111` | `0001` | 0s        | Logical right, fill left     |
| `4'b1010 << 1`           | `1010` | `0100` | 0s        | Logical left, fill right     |
| `4'b1010 >> 1`           | `1010` | `0101` | 0s        | Logical right, fill left     |
| `4'b1010 >>> 1` (signed) | `1010` | `1101` | MSB (1)   | Arithmetic right, sign = 1   |
| `4'b0110 >>> 1` (signed) | `0110` | `0011` | MSB (0)   | Arithmetic right, sign = 0   |
| `4'b1001 <<< 1` (slide)  | `1001` | `0011` | LSB (1)   | Per slide: fills with LSB      |
| `4'b1001 <<< 1` (Verilog)| `1001` | `0010` | 0         | Per IEEE: same as <<           |

---

## Rules to Memorize

1. **`<<`** shifts left, fills right with **0**
2. **`>>`** shifts right, fills left with **0**
3. **`<<<`** — CONFLICT between slides and actual Verilog:
   - **Slide (Lecture 3, slide 17):** fills right with LSB → `1001 <<< 1` = `0011`
   - **Actual Verilog (IEEE standard):** same as `<<`, fills with 0 → `1001 <<< 1` = `0010`
   - **For the exam:** follow whatever your professor expects (likely the slide)
4. **`>>>`** shifts right, fills left with **MSB** (leftmost bit / sign bit)
5. Left shift by N = multiply by 2^N (bits moving toward the big end)
6. Right shift by N = divide by 2^N (bits moving toward the small end)
7. Bits that shift "off the edge" are GONE forever

**Quick memory trick:**
- Logical (`<<`, `>>`) = always fill with **0**
- Arithmetic (`<<<`, `>>>`) = fill with the bit on the **opposite end**
  - `<<<` shifts left, fills from the RIGHT with the rightmost bit (LSB)
  - `>>>` shifts right, fills from the LEFT with the leftmost bit (MSB)

---

## Common Exam Trap

**Q:** `4'b1010 >>> 1` where the variable is NOT declared as `signed`.

```verilog
reg [3:0] x;              // NOT signed!
x = 4'b1010;
x >>> 1;                  // acts like >> because x is unsigned!
```

**Answer: `4'b0101`** (same as `>>`)

`>>>` only preserves the sign bit when the variable is declared `signed`.
On an unsigned variable, `>>>` behaves exactly like `>>`.
