# Wait Statement

The `wait` statement pauses execution until a condition becomes true.

---

## Syntax

```verilog
wait (condition) statement;
wait (condition) #(delay) statement;
```

**How it works:**
1. Check the condition
2. If true → execute the statement (with optional delay)
3. If false → PAUSE and keep checking until it becomes true

---

## Examples

### Basic Wait

```verilog
wait (ready == 1) #10 a = b;
```

This means: "Wait until `ready` is 1, then wait 10 more time units,
then assign `b` to `a`."

### Wait with Complex Condition

```verilog
wait (i > 10 && j < 5) #10 a = b;
```

"Wait until `i > 10` AND `j < 5` are both true, then after 10 time
units, do `a = b`."

### Simple Delay (No Condition)

```verilog
#10;    // just wait 10 time units, no condition
```

This is the simplest form — just a pure delay.

---

## Clock Generation Using Wait/Always

From Lecture 5, slide 14 — using `always` with a delay to make a clock:

```verilog
module wait_statement(a, b, c);
    output reg a, b, c;

    initial begin
        a = 0;          // start clock at 0
    end

    always begin
        #10;            // wait 10 ns
        a = ~a;         // toggle
    end
endmodule
```

```
Time:  0    10    20    30    40    50
a:     0     1     0     1     0     1
       |-----|-----|-----|-----|-----|
       10ns   10ns

Period = 20 ns (two toggles = one full cycle)
```

This is equivalent to:
```verilog
initial a = 0;
always #10 a = ~a;     // shorthand version
```

---

## Wait vs # Delay

| Feature         | `wait(condition)`              | `#delay`                   |
|-----------------|--------------------------------|----------------------------|
| Pauses until    | Condition is true              | Time passes                |
| Depends on      | Signal values                  | Nothing (just time)        |
| Used for        | Waiting for events             | Fixed delays               |

---

## Key Rules

1. `wait` can only be used inside `always` or `initial` blocks
2. `wait` is for **simulation/testbenches** — it is NOT synthesizable
3. Always make sure the condition CAN eventually become true,
   otherwise you get a permanent hang
