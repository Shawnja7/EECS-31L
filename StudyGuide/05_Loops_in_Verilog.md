# Loops in Verilog

Verilog has 4 types of loops. They can ONLY be used inside
`always` or `initial` blocks (procedural context).

---

## 1. `for` Loop

Works just like a for loop in C/Java.

```verilog
integer i;    // loop variable must be 'integer', NOT 'reg'

always @(*) begin
    for (i = 0; i < 8; i = i + 1) begin
        out[i] = in1[i] & in2[i];
    end
end
```

**Note:** Verilog does NOT have `i++`. You must write `i = i + 1`.

**When is this synthesizable?**
Only when the loop bounds are FIXED constants (the synthesizer "unrolls" it
into 8 separate AND gates in the example above).

---

## 2. `while` Loop

Runs as long as a condition is true.

```verilog
integer count;

initial begin
    count = 0;
    while (count < 10) begin
        $display("count = %d", count);
        #10;
        count = count + 1;
    end
end
```

**Be careful:** If the condition never becomes false, you get an
INFINITE LOOP that hangs the simulation.

---

## 3. `repeat` Loop

Executes a FIXED number of times. No condition to check — just a count.

```verilog
initial begin
    repeat (5) begin
        #10 clk = ~clk;
    end
end
```

This toggles `clk` exactly 5 times, with 10 time units between each toggle.

**Difference from `for`:** `repeat` doesn't need a loop variable.
You just say "do this N times."

---

## 4. `forever` Loop

Runs FOREVER (infinite loop). Used for clock generation in testbenches.

```verilog
initial begin
    clk = 0;
    forever begin
        #5 clk = ~clk;    // toggle every 5 time units
    end
end
```

**This creates a clock with period = 10 time units:**
```
Time:  0   5   10  15  20  25  30  ...
clk:   0   1   0   1   0   1   0   ...
```

**WARNING:** You MUST have a delay (`#`) or event control (`@`) inside
`forever`. Otherwise it creates a zero-delay infinite loop that freezes
the simulator!

```verilog
// BAD — hangs the simulator!
forever begin
    clk = ~clk;    // no delay = infinite loop at time 0!
end

// GOOD — has a delay
forever begin
    #5 clk = ~clk;
end
```

---

## Side-by-Side Comparison

| Loop      | How it stops                    | Needs loop var? | Common use               |
|-----------|---------------------------------|-----------------|--------------------------|
| `for`     | When condition is false         | Yes (integer)   | Iterating over bits      |
| `while`   | When condition is false         | No (uses any)   | Conditional repetition   |
| `repeat`  | After N iterations              | No              | Fixed count operations   |
| `forever` | Never (runs until `$finish`)    | No              | Clock generation         |

---

## Complete Examples

**Using `for` to initialize a memory:**
```verilog
integer i;

initial begin
    for (i = 0; i < 16; i = i + 1) begin
        memory[i] = 8'b0;
    end
end
```

**Using `repeat` to wait for 10 clock cycles:**
```verilog
initial begin
    reset = 1;
    repeat (10) @(posedge clk);    // wait for 10 rising edges
    reset = 0;
end
```

**Using `forever` for a clock (most common pattern):**
```verilog
initial begin
    clk = 0;
    forever #5 clk = ~clk;    // period = 10
end
```

**Alternative clock using `always` (also common):**
```verilog
initial clk = 0;
always #5 clk = ~clk;         // same result, different style
```

---

## Key Rules

1. Loops can ONLY go inside `always` or `initial` blocks
2. Loop variables for `for` should be declared as `integer`
3. ALWAYS include a delay or event control in `forever` and `while` to avoid hangs
4. `for` loops with constant bounds are synthesizable (they get unrolled into hardware)
5. `forever`, `while`, and `repeat` are mostly used in testbenches (not synthesizable)
