# EECS 31L Practice Exam 2 — ANSWER KEY

---

## Question 1: (A) 8'b11010101

```
x = 2'b11
y = 2'b01
{3{y}} = {01, 01, 01} = 6'b010101
{x, {3{y}}} = {11, 010101} = 8'b11_010101
```

Replication first (`{3{y}}` = repeat y three times), then concatenate with x.

---

## Question 2: (B)

`z` is assigned inside an `always` block, so it MUST be declared as `reg`.

- (A) Wrong — `wire z` can't be assigned in `always`
- (B) Correct — `reg z` is required for procedural assignment
- (C) Wrong — without explicit `reg`, output defaults to `wire`, which can't be assigned in `always`
- (D) Wrong — only (B) works

---

## Question 3: (B)

- **Module A** uses `always @(d or enable)` with `if (enable)` — this is **level-sensitive** (responds whenever enable is high). That's a **D-latch**. Also note: no `else` branch, so when `enable=0`, `q` holds its value = latch behavior.

- **Module B** uses `always @(posedge clk)` — this is **edge-sensitive** (only responds at the rising edge). That's a **D flip-flop**.

---

## Question 4: (A) 2'b00

In `casez`, both `?` and `z` are treated as don't-care.

`sel = 4'b10z0` — the `z` in the input is also treated as don't-care.

Check each case in order:
- `4'b1??0`: Does `10z0` match `1??0`? The `?` matches anything, and `z` in the input is don't-care. YES — match! `out = 2'b00`

First match wins. The later cases are never reached.

---

## Question 5: (A)

```
(a) 8'hA5 = A=1010, 5=0101 → 10100101 = 128+32+4+1 = 165
(b) 4'd13 = decimal 13
(c) 6'o17 = octal 17 = 001_111 = 8+4+2+1 = 15
(d) 8'b0000_1100 = 8+4 = 12
```

(B) is wrong because octal 17 = 15, not 17.
(C) is wrong because hA5 = 165, not 105.
(D) is wrong because 0000_1100 in binary = 12 in decimal.

---

## Question 6: (A) 6'b1101_10

```
data = 8'b1101_0110
         ^^^^      = data[7:4] = 4'b1101
              ^^   = data[1:0] = 2'b10

{data[7:4], data[1:0]} = {1101, 10} = 6'b110110
```

Bit positions:  7  6  5  4  3  2  1  0
                1  1  0  1  0  1  1  0

data[7:4] = bits 7,6,5,4 = 1101
data[1:0] = bits 1,0 = 10

---

## Question 7: (C) Time 35

Step by step:
1. Time 0: `a = 0`, `ready = 0`
2. The first `initial` block hits `wait(ready == 1)` — ready is 0, so it PAUSES
3. Time 25: second `initial` block sets `ready = 1`
4. The `wait` condition is now true, so it proceeds to `#10 a = 1`
5. Wait 10 more time units
6. Time 35: `a = 1`

`wait` pauses until the condition is true, THEN the `#10` delay kicks in.

---

## Question 8: (B)

```
(a) 4'b10x1 == 4'b10x1   → x  (== returns x whenever x or z is present)
(b) 4'b10x1 === 4'b10x1  → 1  (=== exact match, x matches x)
(c) 4'b10x1 === 4'b1011  → 0  (=== exact match, x does not match 1)
(d) 4'b10x1 == 4'b1011   → x  (== returns x because left side has x)
```

Key rule: `==` gives `x` if either operand has `x` or `z`. `===` compares exactly.

---

## Question 9: (B) `mem[2][3] = 1'b1;`

You cannot do a bit-select on a memory element in standard Verilog. You can only access entire words:

- (A) Legal — assigning a full word to `mem[0]`
- (B) ILLEGAL — `mem[2][3]` tries to select bit 3 of memory word 2. In Verilog-1995/2001, you cannot bit-select from a memory array directly. You'd need to copy to a temp reg first: `temp = mem[2]; temp[3] = 1'b1; mem[2] = temp;`
- (C) Legal — reading and adding full words, assigning to full word
- (D) Legal — reading a full word from memory

---

## Question 10: (A) `count` must be declared as `reg`

`count` is assigned inside an `always` block using `<=`, so it must be `reg`. The code has `output [3:0] count` but never declares `reg [3:0] count`.

- (A) Correct — this is the error
- (B) Wrong — for a synchronous reset (checked inside `posedge clk`), you do NOT need `rst` in the sensitivity list. The reset only happens on the clock edge.
- (D) Wrong — only (A) is an error

---

## Question 11: (B) a=1, b=1, c=0

Non-blocking: all right-hand sides are read FIRST (using current values), then all left-hand sides are written at the end of the time step.

Clock period = 10, so posedge at time 5, 15, 25...

**After posedge at time 5 (1st clock edge):**
- Read phase: a_old=0, b_old=0
- `a <= 1` → a will be 1
- `b <= a` → b will be a_old = 0
- `c <= b` → c will be b_old = 0
- Write phase: a=1, b=0, c=0

**After posedge at time 15 (2nd clock edge):**
- Read phase: a_old=1, b_old=0
- `a <= 1` → a = 1
- `b <= a` → b = a_old = 1
- `c <= b` → c = b_old = 0
- Write phase: a=1, b=1, c=0

**At time 20:** Between posedges, values hold from time 15. a=1, b=1, c=0.

This is a **shift register** pattern — the value `1` shifts through one register per clock cycle: a gets it first, then b one cycle later, then c one cycle after that.

---

---

# BONUS ANSWERS

---

## Bonus 1: (A) 3'b001

In `casex`, BOTH `x` and `z` are treated as don't-care — in BOTH the case items AND the selector.

`sel = 4'b1x10` — the `x` is don't-care.

Check first case: `4'b1010` — does `1x10` match `1010`? The `x` in sel matches anything, and `0` matches `0`. YES — first match wins!

In casex, `x` in the selector acts as "I don't care what this bit is" — so `1x10` matches `1010`, `1110`, `1010`, etc.

---

## Bonus 2: (C) A 2-to-1 MUX with a latch on `out`

The case statement only covers `2'b00` and `2'b01` — it's missing `2'b10`, `2'b11`, and has no `default`. When `sel` is `2'b10` or `2'b11`, `out` is not assigned, so it holds its previous value. That "holding" behavior requires a latch.

---

## Bonus 3: (A) 8'b0000_1010

When you assign a smaller value to a larger register, Verilog **zero-pads** on the left (MSB side).

```
4'b1010 → stored in 8 bits → 8'b0000_1010
```

It does NOT sign-extend (unless the value is declared as signed). It does NOT pad on the right.

---

## Bonus 4: (B) 4'h3

Convert hex to binary first:
```
16'hA3F0 = 1010_0011_1111_0000
           ^^^^_^^^^_^^^^_^^^^
bits:      15-12 11-8  7-4  3-0

bus[11:8] = 0011 = 4'h3
```

---

## Bonus 5: (C) 20 time units

```
Time 0:   clk = 0
          wait(clk==0) is true immediately → #15 → clk = 1 at time 15
Time 15:  clk = 1
          wait(clk==1) is true immediately → #5 → clk = 0 at time 20
Time 20:  clk = 0
          wait(clk==0) is true immediately → #15 → clk = 1 at time 35
...

clk: 0_______________1_____0_______________1_____0
     0               15    20              35    40
     |<---- 15 ---->|<- 5->|<---- 15 ---->|<- 5->|
     |<----- period = 20 ----->|
```

Period = 15 + 5 = 20 time units. (Asymmetric clock — high for 5, low for 15.)

---

## Bonus 6: (A) 8'b0000_1100

```
a = 4'b1100, b = 4'b0011
{a, b} = 8'b1100_0011

8'b1100_0011 >> 4:
Shift right by 4, fill left with 0:
8'b0000_1100
```

---

## Bonus 7: (A) 2'b00

In `casez`, `z` and `?` are both don't-care — in BOTH the case items AND the selector.

`sel = 4'b10z1` — the `z` in the input is treated as don't-care.

Check first case: `4'b10?1` — does `10z1` match `10?1`?
- Bit 3: 1=1 ✓
- Bit 2: 0=0 ✓
- Bit 1: z matches ? (both don't-care) ✓
- Bit 0: 1=1 ✓
YES — first match wins! `out = 2'b00`

---

## Bonus 8: (D) Q toggles

JK Flip-Flop truth table:
```
J  K  | Q(next)
------|---------
0  0  | Q (hold)
0  1  | 0 (reset)
1  0  | 1 (set)
1  1  | ~Q (toggle)
```

When J=1 and K=1, the output flips to its opposite value.
