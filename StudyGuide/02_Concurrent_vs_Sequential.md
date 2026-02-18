# Concurrent vs Sequential Execution

This is one of the MOST important concepts in Verilog. It's what makes
Verilog different from languages like Python or C.

---

## The Big Idea

In real hardware, ALL gates run at the same time. There's no "first" or "second" —
electricity flows through everything simultaneously.

Verilog reflects this:
- Some statements run ALL AT THE SAME TIME (concurrent)
- Some statements run ONE AFTER ANOTHER (sequential)

---

## Concurrent = Runs at the same time

**What runs concurrently?**
- `assign` statements
- Separate `always` blocks (relative to each other)
- Module instantiations (structural)

**Example:**
```verilog
module example(
    input  a, b, c,
    output x, y
);

    assign x = a & b;     // These two run at the
    assign y = b | c;     // SAME TIME, always

endmodule
```

These two `assign` statements are like two separate gates on a circuit board.
They don't wait for each other. They're both always "on."

**Swapping their order changes NOTHING:**
```verilog
    assign y = b | c;     // Moved up — doesn't matter
    assign x = a & b;     // Moved down — doesn't matter
```

---

## Sequential = Runs one after another (top to bottom)

**What runs sequentially?**
- Statements INSIDE an `always` block
- Statements INSIDE an `initial` block

**Example:**
```verilog
module example(
    input      a, b, c,
    output reg x, y
);

    always @(*) begin
        x = a & b;     // Runs FIRST
        y = x | c;     // Runs SECOND (uses the NEW value of x)
    end

endmodule
```

Inside the `always` block, line 1 finishes before line 2 starts.
This is just like normal programming — top to bottom.

---

## The Tricky Part: Multiple `always` Blocks

Each `always` block runs sequentially INSIDE itself,
but CONCURRENTLY with other `always` blocks.

```verilog
module example(
    input      clk,
    input      a, b,
    output reg x, y
);

    // Block 1 — runs on its own
    always @(posedge clk) begin
        x = a & b;          // Sequential inside this block
    end

    // Block 2 — runs at the SAME TIME as Block 1
    always @(posedge clk) begin
        y = a | b;          // Sequential inside this block
    end

endmodule
```

Block 1 and Block 2 both trigger on `posedge clk` and execute simultaneously.
But within each block, statements go top to bottom.

---

## How to Label Statements as S (Sequential) or C (Concurrent)

**The rule is simple:**
- Inside `always` or `initial` → **S** (Sequential)
- Outside of any block (module level) → **C** (Concurrent)

**Example:**
```verilog
module test(input a, b, clk, output reg x, y);

    assign x = a & b;              // C — assign is always concurrent

    always @(posedge clk) begin
        y = a | b;                 // S — inside always block
        x = y & a;                 // S — inside always block
    end

    assign y = a ^ b;              // C — assign is always concurrent

endmodule
```

**Another example (from your quiz):**
```verilog
module seq_conc(port1, port2, port3, port4);

    always @(*) begin                          // --- always block starts ---
        for (i = 0; i < 10; i = i + 1)
        begin
            statement_1;          // S  (inside always, inside for loop)
            statement_2;          // S  (inside always, inside for loop)
        end
        statement_3;              // S  (inside always)
        statement_4;              // S  (inside always)
    end                                        // --- always block ends ---

    statement_5;                  // C  (outside always, at module level)

endmodule
```

---

## Quick Reference

| Code Location                          | Execution Type |
|----------------------------------------|----------------|
| `assign` statement                     | Concurrent     |
| Gate instantiation (`and g1(...)`)     | Concurrent     |
| Module instantiation (`dff u1(...)`)   | Concurrent     |
| Inside `always` block                  | Sequential     |
| Inside `initial` block                 | Sequential     |
| Inside `for`/`while` loop (in always) | Sequential     |
| Multiple `always` blocks (relative)   | Concurrent     |
