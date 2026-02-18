# The Three Design Styles in Verilog

In Verilog, there are THREE different ways to describe a circuit.
Think of it like giving someone directions to your house:

- **Dataflow** = "Turn left at the light, then right at the stop sign" (describe the path data takes)
- **Behavioral** = "Go to the house with the red door" (describe what it does, not how)
- **Structural** = "Connect pipe A to pipe B, then pipe B to pipe C" (describe how parts are wired together)

All three can describe the SAME circuit. They just look different in code.

---

## 1. Dataflow Description

**What is it?**
You use the `assign` keyword to describe how data flows from inputs to outputs
using Boolean expressions (AND, OR, XOR, etc.).

**Key rules:**
- Always uses `assign`
- Left-hand side must be a `wire` (NOT `reg`)
- All `assign` statements run at the SAME TIME (concurrent)
- Order of statements does NOT matter

**Example — Half Adder:**
```verilog
module half_adder(
    input  a,
    input  b,
    output S,
    output C
);

    assign S = a ^ b;    // XOR for sum
    assign C = a & b;    // AND for carry

endmodule
```

**Think of it like this:**
Each `assign` statement is a separate gate that is always "on" and always
watching its inputs. The moment an input changes, the output updates.

**Swapping the order makes NO difference:**
```verilog
    // This is IDENTICAL to the above:
    assign C = a & b;    // moved up — doesn't matter
    assign S = a ^ b;    // moved down — doesn't matter
```

---

## 2. Behavioral Description

**What is it?**
You use `always` blocks and write code that looks more like "programming"
(if/else, case statements, loops). You describe WHAT the circuit does,
not what gates it uses.

**Key rules:**
- Uses `always` blocks (and `initial` blocks in testbenches)
- Left-hand side must be `reg` (NOT `wire`)
- Needs a sensitivity list: `always @(...)` — tells Verilog WHEN to run
- `always @(*)` means "run whenever ANY input changes" (use this for combinational)
- `always @(posedge clk)` means "run on the rising edge of clk" (use this for sequential)

**Example — Half Adder (behavioral):**
```verilog
module half_adder(
    input      a,
    input      b,
    output reg S,
    output reg C
);

    always @(*) begin
        if (a != b) begin
            S = 1'b1;
            C = 1'b0;
        end
        else begin
            S = 1'b0;
            C = 1'b0;
            if (a == 1'b1)
                C = 1'b1;
        end
    end

endmodule
```

**Notice:**
- `S` and `C` are declared as `reg` because they're assigned inside `always`
- `begin`/`end` is like `{` `}` in C — groups multiple statements together
- `@(*)` = re-run this block whenever any signal used inside changes

---

## 3. Structural Description

**What is it?**
You build the circuit by connecting smaller pieces (gates or other modules)
together, like wiring a circuit on a breadboard.

**Key rules:**
- You instantiate (create copies of) gates or modules
- You connect them with `wire` signals
- Gate syntax: `gate_type instance_name(output, input1, input2);`
  - The OUTPUT always comes FIRST
- Module syntax: `module_name instance_name(.port(signal), ...);`

**Example — Half Adder (structural with gates):**
```verilog
module half_adder(
    input  a,
    input  b,
    output S,
    output C
);

    xor x1(S, a, b);    // XOR gate: S = a XOR b
    and a1(C, a, b);    // AND gate: C = a AND b

endmodule
```

**Example — 4-bit Adder (structural with modules):**
```verilog
module adder_4bit(
    input  [3:0] r1,
    input  [3:0] r2,
    input        cin,
    output [3:0] result,
    output       carry
);

    wire c1, c2, c3;    // internal wires to connect the chain

    // Method 1: Implicit (by position) — order MUST match module definition
    fulladder u0(r1[0], r2[0], cin, result[0], c1);
    fulladder u1(r1[1], r2[1], c1,  result[1], c2);
    fulladder u2(r1[2], r2[2], c2,  result[2], c3);
    fulladder u3(r1[3], r2[3], c3,  result[3], carry);

    // Method 2: Explicit (by name) — order does NOT matter
    // fulladder u0(
    //     .x(r1[0]),
    //     .y(r2[0]),
    //     .cin(cin),
    //     .sum(result[0]),
    //     .cout(c1)
    // );

endmodule
```

---

## Side-by-Side Comparison

| Feature          | Dataflow          | Behavioral              | Structural               |
|------------------|-------------------|-------------------------|--------------------------|
| **Keyword**      | `assign`          | `always` / `initial`    | gate/module instantiation|
| **Variable type**| `wire`            | `reg`                   | `wire` for connections   |
| **Execution**    | Concurrent        | Sequential inside block | Concurrent               |
| **Abstraction**  | Medium            | Highest                 | Lowest (closest to HW)   |
| **Best for**     | Combinational     | Complex logic, FSMs     | Hierarchical designs     |

---

## How to Identify Each Style (Quick Test)

Ask yourself:
1. Do you see `assign`? → **Dataflow**
2. Do you see `always` or `initial`? → **Behavioral**
3. Do you see gate names (`and`, `or`, `xor`) or module instances? → **Structural**
