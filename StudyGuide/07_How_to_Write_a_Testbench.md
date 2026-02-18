# How to Write a Testbench

A testbench is a Verilog module whose ONLY job is to TEST another module.
It's like a lab bench where you plug in your circuit, feed it inputs,
and check the outputs.

---

## The 5 Steps to Every Testbench

```
Step 1: Declare the module (NO PORTS!)
Step 2: Declare signals (reg for inputs, wire for outputs)
Step 3: Instantiate the DUT (Device Under Test)
Step 4: Generate a clock (if needed)
Step 5: Apply test inputs with delays
```

---

## Step-by-Step Template

Here's the skeleton — memorize this structure:

```verilog
// Step 1: Module with NO ports
module my_testbench();

    // Step 2: Declare signals
    //   - Inputs to the DUT  → reg  (YOU drive them)
    //   - Outputs from DUT   → wire (DUT drives them)
    reg  clk, reset;
    reg  [3:0] a, b;
    wire [3:0] result;
    wire carry;

    // Step 3: Instantiate the DUT
    my_module uut(
        .clk(clk),
        .reset(reset),
        .a(a),
        .b(b),
        .result(result),
        .carry(carry)
    );

    // Step 4: Generate clock (if the DUT has a clock)
    initial clk = 0;
    always #5 clk = ~clk;       // period = 10 time units

    // Step 5: Apply test inputs
    initial begin
        // Initialize everything
        reset = 1;
        a = 4'b0000;
        b = 4'b0000;

        // Wait, then release reset
        #20;
        reset = 0;

        // Test case 1
        #10;
        a = 4'b0011;
        b = 4'b0101;

        // Test case 2
        #10;
        a = 4'b1111;
        b = 4'b0001;

        // End simulation
        #50;
        $finish;
    end

endmodule
```

---

## Breaking Down Each Step

### Step 1: Module with NO Ports

A testbench has NO inputs and NO outputs. It's self-contained.

```verilog
module my_testbench();       // <-- nothing in parentheses!
    ...
endmodule
```

**Why?** Because nobody is "testing the tester." The testbench is the
top-level module — it drives everything itself.

---

### Step 2: Declare Signals

**Rule:**
- Signals you DRIVE (feed into the DUT) → declare as `reg`
- Signals the DUT OUTPUTS → declare as `wire`

```verilog
    // These feed INTO the DUT — you control them
    reg a, b, sel;

    // These come OUT of the DUT — you just observe them
    wire out;
```

**Why `reg` for inputs?**
Because YOU are assigning values to them inside `initial`/`always` blocks,
and anything assigned in a procedural block must be `reg`.

**Why `wire` for outputs?**
Because the DUT drives them with its internal logic.

---

### Step 3: Instantiate the DUT

"Instantiate" = create a copy of the module you want to test.

```verilog
    // The module you're testing:
    // module mux2to1(input a, b, sel, output out);

    // Instantiate it:
    mux2to1 uut(
        .a(a),           // connect DUT port 'a' to testbench signal 'a'
        .b(b),           // connect DUT port 'b' to testbench signal 'b'
        .sel(sel),       // connect DUT port 'sel' to testbench signal 'sel'
        .out(out)        // connect DUT port 'out' to testbench signal 'out'
    );
```

**`.port_name(signal_name)`** = "connect this port to this signal"

- `uut` is the instance name (stands for "Unit Under Test")
- The `.portname(signal)` syntax is called "explicit port mapping"
- You can also use positional mapping: `mux2to1 uut(a, b, sel, out);`
  but explicit is safer and clearer

---

### Step 4: Generate a Clock

If your DUT has a clock input, you need to create a clock signal.

**Method 1: `initial` + `forever`**
```verilog
    initial begin
        clk = 0;
        forever #5 clk = ~clk;    // toggle every 5 → period = 10
    end
```

**Method 2: `initial` + `always`**
```verilog
    initial clk = 0;              // set starting value
    always #5 clk = ~clk;         // toggle every 5 → period = 10
```

Both create the same clock:
```
Time:  0   5   10  15  20  25  30  ...
clk:   0   1   0   1   0   1   0   ...
       ┃       ┃       ┃       ┃
       └───┐   └───┐   └───┐   └───┐
           │       │       │       │
       ────┘   ────┘   ────┘   ────┘
```

**Period = 2 x delay = 2 x 5 = 10 time units**

---

### Step 5: Apply Test Inputs

Use an `initial` block with `#` delays to set inputs at specific times.

```verilog
    initial begin
        // Time 0
        a = 0; b = 0; sel = 0;

        // Time 10
        #10;
        a = 0; b = 1; sel = 0;

        // Time 20
        #10;
        a = 1; b = 0; sel = 1;

        // Time 30
        #10;
        a = 1; b = 1; sel = 1;

        // Time 40 — end simulation
        #10;
        $finish;
    end
```

**`$finish`** = stop the simulation. Without this, the `always` clock
keeps running forever.

---

## Complete Testbench Examples

### Example 1: Testbench for a 2-to-1 MUX (no clock)

```verilog
module mux2to1_tb();

    // Signals
    reg a, b, sel;
    wire out;

    // Instantiate DUT
    mux2to1 uut(
        .a(a),
        .b(b),
        .sel(sel),
        .out(out)
    );

    // Test all input combinations
    initial begin
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

---

### Example 2: Testbench for a D Flip-Flop (with clock)

```verilog
module dff_tb();

    // Signals
    reg clk, d;
    wire q;

    // Instantiate DUT
    dff uut(
        .clk(clk),
        .d(d),
        .q(q)
    );

    // Clock generation: period = 20
    initial clk = 0;
    always #10 clk = ~clk;

    // Test stimulus
    initial begin
        d = 0;
        #25;          // wait past first clock edge
        d = 1;
        #20;          // hold for one full clock period
        d = 0;
        #20;
        d = 1;
        #20;
        $finish;
    end

endmodule
```

---

### Example 3: Testbench for a 4-bit Counter (with clock + reset)

```verilog
module counter4_tb();

    // Signals
    reg clk, rst;
    wire [3:0] count;

    // Instantiate DUT
    counter4 uut(
        .clk(clk),
        .rst(rst),
        .count(count)
    );

    // Clock: period = 10
    initial clk = 0;
    always #5 clk = ~clk;

    // Test
    initial begin
        rst = 1;              // start with reset asserted
        #15;                  // hold reset for a bit
        rst = 0;              // release reset — counter starts counting
        #100;                 // let it count for a while
        rst = 1;              // reset again
        #10;
        rst = 0;              // release and count again
        #50;
        $finish;
    end

endmodule
```

---

## Useful System Tasks

| Task          | What it does                                          |
|---------------|-------------------------------------------------------|
| `$finish`     | Ends the simulation                                   |
| `$display`    | Prints a message ONCE (like printf)                   |
| `$monitor`    | Prints EVERY TIME a listed signal changes             |
| `$time`       | Returns the current simulation time                   |
| `$dumpfile`   | Specifies a file to dump waveforms to                 |
| `$dumpvars`   | Dumps all variables to the waveform file              |

**Example using `$monitor`:**
```verilog
    initial begin
        $monitor("Time=%0t a=%b b=%b sel=%b out=%b",
                 $time, a, b, sel, out);
    end
```

---

## Testbench Checklist (Use This on the Exam)

1. Module has NO ports
2. DUT inputs are `reg`, DUT outputs are `wire`
3. DUT is instantiated with correct port mapping
4. Clock is generated (if needed) with `initial` + `always #N`
5. Test inputs are applied in an `initial` block with `#` delays
6. Simulation ends with `$finish`
