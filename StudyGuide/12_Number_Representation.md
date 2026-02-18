# Number Representation in Verilog

Every number in Verilog follows a specific format. This shows up on
quizzes constantly.

---

## The Format

```
[size]'[base][value]
```

| Part      | What it means                    | Required? |
|-----------|----------------------------------|-----------|
| `size`    | Number of bits (in decimal)      | Optional  |
| `'`       | Apostrophe separator             | Required  |
| `base`    | b=binary, o=octal, d=decimal, h=hex | Required |
| `value`   | The actual number                | Required  |

---

## Examples

| Verilog literal | Size | Base    | Value  | Binary equivalent      |
|-----------------|------|---------|--------|------------------------|
| `4'b1010`       | 4    | binary  | 1010   | `1010`                 |
| `8'b1010`       | 8    | binary  | 1010   | `0000_1010` (padded)   |
| `4'hA`          | 4    | hex     | A      | `1010`                 |
| `8'hAB`         | 8    | hex     | AB     | `1010_1011`            |
| `8'd12`         | 8    | decimal | 12     | `0000_1100`            |
| `3'o7`          | 3    | octal   | 7      | `111`                  |
| `4'd5`          | 4    | decimal | 5      | `0101`                 |

---

## Rules to Know

### 1. If size is BIGGER than the value, pad with 0s on the left

```
8'b1010  →  0000_1010     (4 bits of value, padded to 8 with zeros)
8'hA     →  0000_1010     (A = 4 bits, padded to 8)
```

### 2. If size is SMALLER than the value, truncate from the left

```
2'b1010  →  10            (only keeps the rightmost 2 bits)
```

### 3. Underscore `_` is allowed for readability (ignored by Verilog)

```verilog
8'b1010_0011      // same as 8'b10100011
32'hDEAD_BEEF     // same as 32'hDEADBEEF
```

### 4. Base letters are case-insensitive

```verilog
4'B1010    // same as 4'b1010
8'HAB      // same as 8'hAB
```

### 5. No size = at least 32 bits (implementation dependent)

```verilog
'b1010     // binary 1010, but stored in 32+ bits
'd100      // decimal 100 in 32+ bits
```

### 6. No base = decimal by default

```verilog
42         // same as 'd42 (plain decimal number)
-7         // negative decimal
```

---

## Hex to Binary Quick Reference

You WILL need to convert hex to binary on the exam. Each hex digit = 4 bits.

| Hex | Binary | Hex | Binary |
|-----|--------|-----|--------|
| 0   | 0000   | 8   | 1000   |
| 1   | 0001   | 9   | 1001   |
| 2   | 0010   | A   | 1010   |
| 3   | 0011   | B   | 1011   |
| 4   | 0100   | C   | 1100   |
| 5   | 0101   | D   | 1101   |
| 6   | 0110   | E   | 1110   |
| 7   | 0111   | F   | 1111   |

**Example:** `8'hA3` → A=`1010`, 3=`0011` → `1010_0011`

---

## Special Values: x and z

| Character | Meaning                 | Example         |
|-----------|-------------------------|-----------------|
| `x`       | Unknown value           | `4'b10x1`       |
| `z`       | High impedance (disconnected) | `4'b10z1` |

- In **binary**, each `x` or `z` represents 1 bit
- In **hex**, each `x` or `z` represents 4 bits
- In **octal**, each `x` or `z` represents 3 bits

```verilog
4'b10x1    // bit 1 is unknown
8'hxA      // upper 4 bits are unknown: xxxx_1010
```

---

## Signed Numbers

You can declare signed numbers with a negative sign:

```verilog
-4'd3      // negative 3, stored in 4 bits (2's complement: 1101)
```

**Important:** The negative sign goes BEFORE the size, not in the value.

```verilog
-4'd3      // correct: negative 3
4'd-3      // WRONG: syntax error
```

---

## Common Exam Questions

**Q: What is `8'b1010` in binary?**
A: `0000_1010` (padded with zeros to fill 8 bits)

**Q: What is `4'hF` in binary?**
A: `1111` (F in hex = 15 in decimal = 1111 in binary)

**Q: What is `3'b10101` stored as?**
A: `101` (truncated — only the rightmost 3 bits kept)

**Q: Is `4'B1010` valid?**
A: Yes — base letter is case-insensitive.
