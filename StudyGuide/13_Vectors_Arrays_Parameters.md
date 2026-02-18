# Vectors, Arrays, Memory, and Parameters

---

## Vectors (Multi-bit Signals)

A vector is a signal with MORE THAN ONE BIT. Instead of `wire a` (1 bit),
you can have `wire [7:0] a` (8 bits).

### Declaring Vectors

```verilog
reg  [7:0] MB1;          // 8-bit reg, MSB=bit 7, LSB=bit 0
wire [0:7] MB2;          // 8-bit wire, MSB=bit 0, LSB=bit 7 (reversed!)
reg  [3:0] data;          // 4-bit reg
wire [3:0] word = 4'b1010; // 4-bit wire with initial value
reg  [7:0] total = 8'd12;  // 8-bit reg = 0000_1100
```

**`[7:0]` means:** bit 7 is the MSB (leftmost), bit 0 is the LSB (rightmost).
This is the most common convention.

### Bit-Select: Accessing ONE bit

Use square brackets with a single index:

```verilog
wire [3:0] data = 4'b1010;

a = data[3];     // a = 1  (the MSB)
b = data[0];     // b = 0  (the LSB)
c = data[2];     // c = 0
```

```
data:   [1] [0] [1] [0]
index:   3   2   1   0
```

### Part-Select: Accessing MULTIPLE bits

Use brackets with a range:

```verilog
reg [7:0] total = 8'b1010_0011;

bitslice1 = total[3:0];    // bitslice1 = 4'b0011 (lower 4 bits)
bitslice2 = total[7:4];    // bitslice2 = 4'b1010 (upper 4 bits)
```

```
total:     [1] [0] [1] [0] [0] [0] [1] [1]
index:      7   6   5   4   3   2   1   0
                            |           |
total[7:4] = 1010           total[3:0] = 0011
```

**From your quiz:** `data[15:8]` extracts the 8 MSBs from a 16-bit signal.

---

## Arrays (Collections of Elements)

An array is a COLLECTION of signals. Think of it like a list or table.

**Key difference from vectors:**
- Vector = one signal with multiple bits (like `reg [7:0] data`)
- Array = multiple signals (like a memory with many locations)

### Declaring Arrays

```verilog
reg amem [0:3];              // 4 elements, each 1 bit
reg [0:5] bmem [3:0];        // 4 elements, each 6 bits
reg cmem [3:0][2:0];         // 2D array: 4 rows x 3 columns, each 1 bit
```

**Reading the declaration:**
```
reg [0:5] bmem [3:0];
     ↑                ↑
     size of each     number of
     element          elements
     (6 bits)         (4 elements: index 3,2,1,0)
```

### Visual Representation

```
wire [3:0] areg;              // ONE 4-bit signal
areg = [ b3 | b2 | b1 | b0 ]

reg amem [0:3];               // FOUR 1-bit signals
amem[0] = [b]
amem[1] = [b]
amem[2] = [b]
amem[3] = [b]

reg [0:5] bmem [3:0];        // FOUR 6-bit signals
bmem[0] = [ b0 | b1 | b2 | b3 | b4 | b5 ]
bmem[1] = [ b0 | b1 | b2 | b3 | b4 | b5 ]
bmem[2] = [ b0 | b1 | b2 | b3 | b4 | b5 ]
bmem[3] = [ b0 | b1 | b2 | b3 | b4 | b5 ]

reg cmem [3:0][2:0];         // 4x3 grid of 1-bit signals
cmem[0][0]  cmem[0][1]  cmem[0][2]
cmem[1][0]  cmem[1][1]  cmem[1][2]
cmem[2][0]  cmem[2][1]  cmem[2][2]
cmem[3][0]  cmem[3][1]  cmem[3][2]
```

### From Your Quiz

```verilog
reg signed [3:0] anArray [0:4];
```

This means:
- `[3:0]` = each element is 4 bits, signed
- `[0:4]` = 5 elements (index 0, 1, 2, 3, 4)
- Answer: **5 elements, each is 4 signed bits**

---

## Parameters

A `parameter` is a **constant** — a value that doesn't change.
Like `#define` in C or `const` in JavaScript.

```verilog
parameter N = 3;
parameter WIDTH = 8;
parameter DELAY = 10;
```

### Why Use Parameters?

They make your code flexible and reusable:

```verilog
// WITHOUT parameters — hardcoded, inflexible
wire [3:0] areg;
reg amem [0:3];

// WITH parameters — change N once, everything updates
parameter N = 3;
wire [N:0] areg;          // 4-bit vector (N down to 0)
reg amem [0:N];            // 4 elements
reg [0:5] bmem [N:0];     // 4 elements, each 6 bits
reg cmem [N:0][2:0];      // 4x3 2D array
```

If you change `N = 7`, ALL of those declarations automatically resize.

### Parameters for Delays

```verilog
parameter DELAY = 5;

assign #DELAY y = a & b;     // delay of 5 time units
```

---

## Quick Reference

| Concept    | Syntax                        | What it creates              |
|------------|-------------------------------|------------------------------|
| Vector     | `reg [7:0] data;`            | One 8-bit signal             |
| Bit-select | `data[3]`                     | Access bit 3                 |
| Part-select| `data[7:4]`                   | Access bits 7 down to 4      |
| Array      | `reg mem [0:3];`             | 4 one-bit elements           |
| 2D Array   | `reg grid [3:0][2:0];`       | 4x3 grid of one-bit elements |
| Parameter  | `parameter N = 8;`           | Constant value = 8           |
