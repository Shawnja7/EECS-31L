# EECS 31L Midterm Practice Quiz — ANSWER KEY

---

## SECTION A: TRUE / FALSE

**1.** FALSE
Concurrent statements execute whenever their inputs change, regardless of their order in the code.

**2.** TRUE
`reg` types are assigned with procedural assignments (`=` or `<=`) inside `always` or `initial` blocks.

**3.** TRUE
`===` is the case equality operator; it compares all 4 logic values (0, 1, x, z) exactly. `==` returns `x` if any bit is `x` or `z`.

**4.** FALSE
`===` does an exact match — `x` does not equal `1`, so this is false.

**5.** FALSE
Non-blocking assignments are deferred — the right-hand side is evaluated immediately, but the assignment to the left-hand side happens at the end of the time step. It's blocking (`=`) that makes results visible immediately.

**6.** TRUE
A testbench instantiates the DUT and drives signals internally — it has no external ports.

**7.** FALSE
`assign` is for continuous (concurrent) assignments outside procedural blocks. Inside `always`, use `=` or `<=`.

**8.** FALSE
Only `reg` types can be assigned inside `always`/`initial` blocks. `wire` is assigned using `assign` statements.

**9.** TRUE
Verilog is case-sensitive. This is a common source of errors.

**10.** TRUE
Inside an `always` block, statements (including loop bodies) execute sequentially.

**11.** TRUE
Structural description connects primitive gates or other modules, similar to drawing a schematic.

**12.** FALSE
Dataflow uses `assign` statements (continuous assignment). `always` blocks are used in behavioral description.

**13.** TRUE
Concurrent `assign` re-evaluates whenever any right-hand side signal changes.

**14.** TRUE
Without a delay or event control, `forever` creates a zero-delay infinite loop that freezes the simulator.

**15.** TRUE
`casex` treats `x` and `z` as don't-care. `casez` treats only `z` (and `?`) as don't-care.

---

## SECTION B: MULTIPLE CHOICE

**1.** (D) 4'b1000
`<<` is logical left shift. `1111` shifted left by 3: the top 3 bits fall off, 3 zeros fill from the right → `1000`.

**2.** (D) 4'b0001
`>>` is logical right shift. `1111` shifted right by 3: the bottom 3 bits fall off, 3 zeros fill from the left → `0001`.

**3.** (A) 4'b1101
`>>>` is arithmetic right shift. For signed values, it fills with the MSB (sign bit). MSB is `1`, so: `1010` → `1101`.

**4.** (A) 4'b0100
`<<` logical left shift by 1. `1010` → shift left, MSB falls off, 0 fills LSB → `0100`.

**5.** (B) 4'b0101
`>>` logical right shift by 1. `1010` → shift right, LSB falls off, 0 fills MSB → `0101`.

**6.** (A) assign statement
`assign` is a dataflow construct, not behavioral. Behavioral uses `always`/`initial` blocks containing `if`, `case`, loops, etc.

**7.** (B) To execute code only once at the beginning of simulation

**8.** (C) An Array has 5 elements and each element is 4 signed bits
`[3:0]` = 4 bits per element. `[0:4]` = indices 0,1,2,3,4 = 5 elements. Each is signed.

**9.** (C) data[15:8]
MSBs are bits 15 down to 8. In Verilog, use `data[15:8]` (big index first when declared `[15:0]`).

**10.** (B) False
`===` compares exact bit values including x/z. Since `x ≠ 1`, the result is false (0).

**11.** (C) repeat
`repeat(N)` executes the body exactly N times. `for` and `while` check conditions. `forever` runs indefinitely.

**12.** (A) `c` gets the NEW value of `a` (which is `b`)
With blocking `=`, Line 1 completes before Line 2 starts. So `a` gets `b`'s value first, then `c` gets that new `a` value. Both end up equal to `b`.

**13.** (B) `c` gets the OLD value of `a`
With non-blocking `<=`, all right-hand sides are evaluated first (using current values), then all left-hand sides are updated at the end of the time step. So `c` gets the old value of `a`.

**14.** (B) `always #10 clk = ~clk;`
This toggles every 10 units → period = 20. Option A has no delay (infinite loop). Option D has no delay inside `forever` (infinite loop).

**15.** (C) Structural
Instantiating primitive gates (`and`, `or`) by name is structural description.

**16.** (A) Dataflow
`assign` with continuous assignment of expressions is dataflow style.

**17.** (B) Behavioral
Using an `always` block with a sensitivity list and procedural assignments is behavioral style.

**18.** (A) 8'b10101010
`{4{2'b10}}` replicates `10` four times → `10101010`.

**19.** (B) The output `out` is updated 5 time units after `a` or `b` changes
In a continuous assignment, `#5` models a propagation delay — the output reflects input changes after 5 time units.

**20.** (C) Incomplete sensitivity list — `b` is missing
The always block uses both `a` and `b`, but only `a` is in the sensitivity list. Changes to `b` won't trigger re-evaluation. Should be `always @(a or b)` or `always @(*)`.

---

## SECTION C: SEQUENTIAL vs. CONCURRENT

**1.**
- Statement 1: **C** (concurrent — `assign` is always concurrent)
- Statement 2: **S** (sequential — inside `always` block)
- Statement 3: **S** (sequential — inside `always` block)
- Statement 4: **C** (concurrent — `assign` is always concurrent)

**2.**
- Statement 1: **S** (inside always block, inside for loop)
- Statement 2: **S** (inside always block, inside for loop)
- Statement 3: **S** (inside always block)
- Statement 4: **S** (inside always block)
- Statement 5: **C** (outside always block, at module level)

---

## SECTION D: ERROR IDENTIFICATION

**1.** Missing semicolon after the port list → should be `module adder(a, b, sum);`

**2.** `out` is declared as `wire` but is assigned inside an `always` block. It should be `reg out;` not `wire out;`.

**3.** Two errors:
1. `assign` keyword cannot be used inside an `always` block — remove it.
2. `Result` needs to be declared as `reg` since it's assigned in an `always` block.

**4.** The same `reg` variable (`count`) is being driven by two different `always` blocks. In Verilog, a `reg` should only be assigned in ONE `always` block.

---

## SECTION E: SIMPLE CODING — TESTBENCHES

**1.** Testbench for 2-to-1 MUX:
```verilog
module mux2to1_tb;
    reg a, b, sel;
    wire out;

    // Instantiate DUT
    mux2to1 uut(.a(a), .b(b), .sel(sel), .out(out));

    initial begin
        // Test all input combinations
        a = 0; b = 0; sel = 0; #10;
        a = 0; b = 1; sel = 0; #10;
        a = 1; b = 0; sel = 0; #10;
        a = 1; b = 1; sel = 0; #10;
        a = 0; b = 0; sel = 1; #10;
        a = 0; b = 1; sel = 1; #10;
        a = 1; b = 0; sel = 1; #10;
        a = 1; b = 1; sel = 1; #10;
        $finish;
    end
endmodule
```
Key points: No ports on testbench, inputs are `reg`, outputs are `wire`, use `#delay` between test vectors, end with `$finish`.

---

**2.** Testbench for D flip-flop with clock:
```verilog
module dff_tb;
    reg clk, d;
    wire q;

    // Instantiate DUT
    dff uut(.clk(clk), .d(d), .q(q));

    // Clock generation: period = 20 time units
    initial clk = 0;
    always #10 clk = ~clk;

    // Test stimulus
    initial begin
        d = 0; #25;
        d = 1; #20;
        d = 0; #20;
        d = 1; #20;
        d = 0; #15;
        $finish;
    end
endmodule
```
Key points: Clock initialized in `initial`, toggled with `always #10 clk = ~clk;`. Test inputs change at odd times relative to clock to avoid setup/hold issues.

---

**3.** Testbench for 4-bit counter with synchronous reset:
```verilog
module counter4_tb;
    reg clk, rst;
    wire [3:0] count;

    // Instantiate DUT
    counter4 uut(.clk(clk), .rst(rst), .count(count));

    // Clock: period = 10
    initial clk = 0;
    always #5 clk = ~clk;

    // Test stimulus
    initial begin
        rst = 1; #15;       // Assert reset
        rst = 0; #100;      // Let it count for a while
        rst = 1; #10;       // Reset again
        rst = 0; #50;       // Count again
        $finish;
    end
endmodule
```

---

## SHIFT OPERATORS CHEAT SHEET

| Expression | Input | Result | Rule |
|---|---|---|---|
| `4'b1111 << 3` | `1111` | `1000` | Logical left: fill right with 0s |
| `4'b1111 >> 3` | `1111` | `0001` | Logical right: fill left with 0s |
| `4'b1010 << 1` | `1010` | `0100` | Logical left: MSB falls off, 0 fills LSB |
| `4'b1010 >> 1` | `1010` | `0101` | Logical right: LSB falls off, 0 fills MSB |
| `4'b1010 >>> 1` (signed) | `1010` | `1101` | Arithmetic right: fill with MSB (sign bit=1) |
| `4'b0110 >>> 1` (signed) | `0110` | `0011` | Arithmetic right: fill with MSB (sign bit=0) |

**Key rule:** `<<` and `>>` ALWAYS fill with 0. `>>>` on signed values fills with the sign bit (MSB). `<<<` is the same as `<<` (fills with 0).
