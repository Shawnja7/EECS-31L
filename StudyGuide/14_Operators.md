# Operators: Relational, Arithmetic, and Concatenation

---

## Relational Operators (Comparison)

These compare two values and return 1 (true) or 0 (false).

| Operator | Name                    | Example          | Result |
|----------|-------------------------|------------------|--------|
| `==`     | Equality                | `3 == 3`         | 1      |
| `!=`     | Inequality              | `3 != 4`         | 1      |
| `<`      | Less than               | `3 < 5`          | 1      |
| `<=`     | Less than or equal      | `5 <= 5`         | 1      |
| `>`      | Greater than            | `7 > 3`          | 1      |
| `>=`     | Greater than or equal   | `4 >= 4`         | 1      |
| `===`    | Identity (exact match)  | `1'bx === 1'bx`  | 1      |
| `!==`    | Not identity            | `1'bx !== 1'b0`  | 1      |

### The BIG Difference: `==` vs `===`

This is a common exam question!

**`==` (equality):** Compares only 0 and 1. If either side has `x` or `z`,
the result is **unknown (x)** — not true, not false.

**`===` (identity):** Compares ALL 4 values (0, 1, x, z) exactly.
`x` matches `x`, `z` matches `z`.

```
Expression           ==     ===
─────────────────────────────────
3'b101 vs 3'b101     1      1       (same — both agree)
3'b101 vs 3'b100     0      0       (same — both agree)
3'b10x vs 3'b10x     x      1       (DIFFERENT!)
3'b10x vs 3'b101     x      0       (DIFFERENT!)
3'b1z0 vs 3'b1z0     x      1       (DIFFERENT!)
```

**From your quiz:**
```
3'b101x === 3'b1011  →  FALSE (0)
```
Because `x` is not the same as `1` in identity comparison.
`===` checks exact match — `x` only matches `x`.

### Warning: `<=` is Two Things!

```verilog
a <= b;              // inside always: NON-BLOCKING assignment
if (a <= b)          // inside if: LESS THAN OR EQUAL comparison
```

Verilog knows which one you mean from context.

---

## Arithmetic Operators

| Operator | Description    | Example              | Result          |
|----------|----------------|----------------------|-----------------|
| `+`      | Add            | `4'd3 + 4'd2`       | `4'd5`          |
| `-`      | Subtract       | `4'd5 - 4'd3`       | `4'd2`          |
| `*`      | Multiply       | `4'd3 * 4'd2`       | `4'd6`          |
| `/`      | Divide         | `4'd8 / 4'd2`       | `4'd4`          |
| `%`      | Modulus         | `4'd7 % 4'd3`       | `4'd1`          |
| `**`     | Exponent       | `2 ** 3`             | `8`             |

---

## Concatenation `{ }`

Concatenation joins bits together into a bigger signal.
Use curly braces `{ }` with commas.

```verilog
wire [3:0] a = 4'b1010;
wire [3:0] b = 4'b0011;

wire [7:0] c = {a, b};        // c = 8'b1010_0011
wire [7:0] d = {b, a};        // d = 8'b0011_1010 (order matters!)
```

**Think of it like gluing bits together:**
```
{a, b} = {1010, 0011} = 1010_0011
 ^^^^    ^^^^
 left    right
```

### Concatenation with Single Bits

```verilog
wire a = 1'b1;
wire b = 1'b0;
wire [1:0] c = {a, b};        // c = 2'b10
```

---

## Replication `{N{...}}`

Repeats a pattern N times.

```verilog
wire [3:0] a = {4{1'b1}};           // a = 4'b1111
wire [7:0] b = {8{1'b0}};           // b = 8'b0000_0000
wire [5:0] c = {3{2'b10}};          // c = 6'b10_10_10
```

### Combining Replication and Concatenation

```verilog
reg a = 1'b1;
reg [1:0] b = 2'b00;

wire [7:0] y = {4{a}, 2{b}};
// {4{a}}  = {1,1,1,1}     = 4'b1111
// {2{b}}  = {00, 00}      = 4'b0000
// result  = {1111, 0000}  = 8'b1111_0000
```

---

## Logical vs Bitwise Operators (Bonus)

| Type    | Operators       | Works on          | Result          |
|---------|-----------------|-------------------|-----------------|
| Bitwise | `&` `|` `^` `~` | Each bit separately | Multi-bit result |
| Logical | `&&` `||` `!`   | Entire value as T/F | 1-bit result (0 or 1) |

```verilog
// Bitwise AND — operates on each bit
4'b1010 & 4'b1100  =  4'b1000

// Logical AND — treats each value as true/false
4'b1010 && 4'b1100  =  1'b1  (both are nonzero = both true)
```

---

## Quick Reference

```
==   equality (x if either has x/z)
===  identity (exact match including x and z)
!=   inequality
!==  not identity
{a,b}    concatenate a and b
{N{a}}   repeat a N times
```
