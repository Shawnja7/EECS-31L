# Common Mistakes in Verilog (Error Identification)

Your professor will likely show you broken code and ask "what's wrong?"
Here are ALL the common mistakes, with the broken code AND the fix.

---

## Mistake 1: Missing Semicolon After Port List

**BROKEN:**
```verilog
module adder(a, b, sum)        // <-- missing semicolon!
    input [3:0] a, b;
    output [3:0] sum;
    assign sum = a + b;
endmodule
```

**FIXED:**
```verilog
module adder(a, b, sum);       // <-- semicolon added
    input [3:0] a, b;
    output [3:0] sum;
    assign sum = a + b;
endmodule
```

---
testing module error

## Mistake 2: Using `wire` Inside an `always` Block

Variables assigned inside `always` MUST be `reg`. `wire` is for `assign` only.

**BROKEN:**
```verilog
module mux(a, b, sel, out);
    input a, b, sel;
    output out;
    wire out;                   // <-- WRONG! Should be reg

    always @(a or b or sel) begin
        if (sel)
            out = a;
        else
            out = b;
    end
endmodule
```

**FIXED:**
```verilog
module mux(a, b, sel, out);
    input a, b, sel;
    output out;
    reg out;                    // <-- FIXED: changed to reg

    always @(a or b or sel) begin
        if (sel)
            out = a;
        else
            out = b;
    end
endmodule
```

---

## Mistake 3: Using `assign` Inside an `always` Block

`assign` is for continuous (concurrent) assignments OUTSIDE of procedural blocks.
Inside `always`, just use `=` or `<=`.

**BROKEN:**
```verilog
module test(A, B, Result);
    input A, B;
    output Result;
    reg Result;

    always @(A or B) begin
        assign Result = A & B;   // <-- WRONG! No 'assign' inside always
    end
endmodule
```

**FIXED:**
```verilog
module test(A, B, Result);
    input A, B;
    output Result;
    reg Result;

    always @(A or B) begin
        Result = A & B;          // <-- FIXED: removed 'assign'
    end
endmodule
```

---

## Mistake 4: Missing `assign` Keyword Outside `always`

If you're NOT inside an `always` block, you NEED the `assign` keyword.

**BROKEN:**
```verilog
module example(A, B, Y);
    input A, B;
    output Y;

    Y = A & B;                  // <-- WRONG! Missing 'assign'
endmodule
```

**FIXED:**
```verilog
module example(A, B, Y);
    input A, B;
    output Y;

    assign Y = A & B;           // <-- FIXED: added 'assign'
endmodule
```

---

## Mistake 5: Incomplete Sensitivity List

If you use `always @(a)` but the block also reads `b`, then changes
to `b` won't trigger the block. The output will be WRONG.

**BROKEN:**
```verilog
always @(a) begin              // <-- WRONG! 'b' is missing
    out = a & b;
end
```

**FIXED (option 1 — list all signals):**
```verilog
always @(a or b) begin         // <-- FIXED: added 'b'
    out = a & b;
end
```

**FIXED (option 2 — use wildcard, RECOMMENDED):**
```verilog
always @(*) begin              // <-- FIXED: * catches everything
    out = a & b;
end
```

---

## Mistake 6: Same `reg` Driven by Multiple `always` Blocks

A `reg` variable should only be assigned in ONE `always` block.
Two blocks driving the same signal = undefined behavior.

**BROKEN:**
```verilog
always @(posedge clk)
    count <= count + 1;        // Block 1 drives 'count'

always @(negedge clk)
    count <= count - 1;        // Block 2 ALSO drives 'count' — BAD!
```

**FIXED:**
```verilog
always @(posedge clk) begin
    if (direction)
        count <= count + 1;
    else
        count <= count - 1;
end
// Only ONE always block drives 'count'
```

---

## Mistake 7: Case Sensitivity

Verilog IS case-sensitive. `MyModule` and `mymodule` are DIFFERENT.

**BROKEN:**
```verilog
module MyAdder(a, b, sum);
    ...
endmodule

// Later, trying to instantiate:
myadder u1(.a(x), .b(y), .sum(z));    // <-- WRONG! 'myadder' != 'MyAdder'
```

**FIXED:**
```verilog
MyAdder u1(.a(x), .b(y), .sum(z));    // <-- FIXED: matches exactly
```

---

## Mistake 8: Using Parentheses Instead of Brackets for Bit Select

Parentheses `()` are for port lists. Brackets `[]` are for bit selection.

**BROKEN:**
```verilog
wire result;
result = data(3);              // <-- WRONG! Should use brackets
```

**FIXED:**
```verilog
wire result;
assign result = data[3];      // <-- FIXED: square brackets
```

---

## Mistake 9: Incomplete `if/else` Creates a Latch

If you write an `if` without an `else` in combinational logic,
the synthesizer infers a LATCH (unintended memory).

**BROKEN (creates a latch):**
```verilog
always @(*) begin
    if (sel)
        out = a;
    // No else! What happens when sel=0?
    // 'out' holds its previous value = LATCH
end
```

**FIXED:**
```verilog
always @(*) begin
    if (sel)
        out = a;
    else
        out = b;               // <-- FIXED: all cases covered
end
```

---

## Mistake 10: Missing `default` in `case` Statement

Same problem as above — if you don't cover all cases, you get a latch.

**BROKEN:**
```verilog
always @(*) begin
    case (sel)
        2'b00: out = a;
        2'b01: out = b;
        // What about 2'b10 and 2'b11? LATCH!
    endcase
end
```

**FIXED:**
```verilog
always @(*) begin
    case (sel)
        2'b00:   out = a;
        2'b01:   out = b;
        default: out = 0;     // <-- FIXED: catches all other cases
    endcase
end
```

---

## Mistake 11: Missing `begin`/`end` for Multiple Statements

If an `if`, `else`, `for`, or `always` has more than one statement,
you MUST wrap them in `begin`/`end`.

**BROKEN:**
```verilog
if (sel)
    a = 1;
    b = 0;     // <-- This is NOT inside the if! It always runs!
```

**FIXED:**
```verilog
if (sel) begin
    a = 1;
    b = 0;     // <-- Now both are inside the if
end
```

---

## Quick Reference: The Most Common Errors

| #  | Mistake                                  | Fix                                    |
|----|------------------------------------------|----------------------------------------|
| 1  | Missing `;` after port list              | Add the semicolon                      |
| 2  | `wire` assigned in `always`              | Change to `reg`                        |
| 3  | `assign` inside `always`                 | Remove `assign`                        |
| 4  | Missing `assign` outside `always`        | Add `assign`                           |
| 5  | Incomplete sensitivity list              | Use `@(*)`                             |
| 6  | Same reg in multiple `always` blocks     | Use only ONE `always` block            |
| 7  | Wrong case (`MyModule` vs `mymodule`)    | Match exactly                          |
| 8  | `()` instead of `[]` for bit select      | Use square brackets                    |
| 9  | `if` without `else` (combinational)      | Add `else` branch                      |
| 10 | `case` without `default`                 | Add `default`                          |
| 11 | Multiple statements without `begin/end`  | Add `begin`/`end`                      |
