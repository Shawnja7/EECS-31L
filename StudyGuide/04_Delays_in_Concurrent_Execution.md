# Assignment with Delays in Concurrent Execution

---

## What is a Delay?

The `#` symbol in Verilog represents a TIME DELAY.
It tells the simulator: "wait this many time units before doing something."

---

## Delays in `assign` Statements (Concurrent / Dataflow)

When you put `#` in an `assign` statement, it models a GATE DELAY —
how long it takes for a real gate to produce its output.

```verilog
assign #5 y = a & b;
```

**This means:**
"Whenever `a` or `b` changes, calculate `a & b`, then wait 5 time units,
THEN update `y`."

**It does NOT block other statements.** All `assign` statements are concurrent,
so they all run independently at the same time.

---

## Example: Two Gates in a Chain

```verilog
module example(
    input  a, b, c,
    output y
);

    wire mid;

    assign #10 mid = a & b;     // AND gate with 10ns delay
    assign #5  y   = mid | c;   // OR gate with 5ns delay

endmodule
```

**What happens when `a` changes at time 0?**

```
Time 0:   a changes
          → AND gate starts calculating
          → OR gate is not affected yet (mid hasn't changed)

Time 10:  mid updates (10ns delay from AND gate)
          → OR gate sees mid changed, starts calculating

Time 15:  y updates (5ns delay from OR gate)
```

**Total delay from `a` changing to `y` updating = 10 + 5 = 15ns**

This models real hardware — signals propagate through a chain of gates,
and each gate adds its own delay.

---

## Example: MUX with Different Delay Paths

```verilog
module mux_enable(
    input  a, b, sel, gbar,
    output y
);

    wire s1, s2, s3, s4, s5;
    time dly = 7;

    assign #dly s1 = ~gbar;          // NOT gate
    assign #dly s2 = ~sel;           // NOT gate
    assign #dly s3 = ~s2;            // NOT gate
    assign #dly s4 = a & s2 & s1;    // AND gate
    assign #dly s5 = b & s3 & s1;    // AND gate
    assign #dly y  = s4 | s5;        // OR gate

endmodule
```

**Different inputs have different total delays to reach y:**

| Input changes | Path through gates          | # of gates | Total delay    |
|---------------|-----------------------------|------------|----------------|
| `a` or `b`    | AND → OR                    | 2          | 2 x 7 = 14ns  |
| `gbar`        | NOT → AND → OR              | 3          | 3 x 7 = 21ns  |
| `sel`         | NOT → NOT → AND → OR        | 4          | 4 x 7 = 28ns  |

---

## Delays Inside `always` Blocks (Sequential)

Inside `always` or `initial` blocks, `#` means "pause execution here."

```verilog
initial begin
    a = 0;          // Time 0: a = 0
    #10;
    a = 1;          // Time 10: a = 1
    #20;
    a = 0;          // Time 30: a = 0
end
```

This is used in testbenches to create input patterns over time.

---

## Key Differences

| Context                | What `#` does                                |
|------------------------|----------------------------------------------|
| `assign #5 y = a & b` | Models gate propagation delay (concurrent)   |
| `#10;` inside always   | Pauses sequential execution for 10 units     |
| `#10` inside initial   | Pauses sequential execution for 10 units     |

---

## Important Notes

- Delays with `#` are for SIMULATION ONLY — they are NOT synthesizable
  (you can't put a `#5` delay on a real chip)
- In concurrent `assign`, the delay does NOT stop other `assign` statements
  from running — they are all independent
- If an input changes AGAIN before the delay expires, the old scheduled
  change is CANCELLED (this is called "inertial delay")
