# Blocking vs Non-Blocking Assignments

This topic confuses almost everyone at first. Let's break it down simply.

---

## The Two Assignment Operators

Inside an `always` block, you can assign values using two different operators:

| Operator | Name          | Symbol | How it works                           |
|----------|---------------|--------|----------------------------------------|
| `=`      | Blocking      | `=`    | Updates the variable RIGHT NOW         |
| `<=`     | Non-blocking  | `<=`   | Schedules the update for LATER         |

---

## Blocking Assignment ( = )

**"Blocking" means: I block the next line from running until I'm done."**

It works exactly like normal programming. The variable gets its new value
IMMEDIATELY, and the next line sees that new value.

```verilog
always @(posedge clk) begin
    a = 5;       // a is NOW 5
    b = a;       // b gets 5 (the NEW value of a)
    c = b + 1;   // c gets 6 (because b is now 5)
end
```

**Step by step:**
1. `a` becomes `5` immediately
2. `b` reads `a` → sees `5` → becomes `5`
3. `c` reads `b` → sees `5` → becomes `6`

**Result:** a=5, b=5, c=6

---

## Non-Blocking Assignment ( <= )

**"Non-blocking" means: I DON'T block the next line. I just schedule my update for later."**

Here's the key: ALL the right-hand sides are read FIRST using the OLD values.
Then ALL the left-hand sides are updated AT THE END of the time step.

Think of it in two phases:
1. **READ phase:** Read all the right-hand side values (using current/old values)
2. **WRITE phase:** Update all the left-hand sides at the same time

```verilog
always @(posedge clk) begin
    a <= 5;      // SCHEDULE: a will become 5 (but not yet!)
    b <= a;      // SCHEDULE: b will become OLD value of a (not 5!)
    c <= b + 1;  // SCHEDULE: c will become OLD value of b + 1
end
```

**Let's say BEFORE this runs: a=0, b=0, c=0**

**READ phase (all at once using OLD values):**
- `a` will get → 5
- `b` will get → old `a` = 0
- `c` will get → old `b` + 1 = 0 + 1 = 1

**WRITE phase (all updates happen at the same time):**
- a=5, b=0, c=1

**Result:** a=5, b=0, c=1

**Compare to blocking:** a=5, b=5, c=6 — completely different!

---

## The Classic Example: The Swap

This is the best way to understand the difference.

**BLOCKING — Does NOT swap:**
```verilog
always @(posedge clk) begin
    a = b;    // a gets b's value immediately
    b = a;    // b gets a's value... which is now b! NO SWAP!
end
```
If a=3, b=7 before:
1. `a = b` → a becomes 7
2. `b = a` → b becomes 7 (a is already 7!)
Result: a=7, b=7 — BOTH are 7, swap failed!

**NON-BLOCKING — DOES swap:**
```verilog
always @(posedge clk) begin
    a <= b;   // schedule: a will get OLD b
    b <= a;   // schedule: b will get OLD a
end
```
If a=3, b=7 before:
- READ phase: a will get 7, b will get 3 (both read OLD values)
- WRITE phase: a=7, b=3
Result: a=7, b=3 — swap works!

---

## When to Use Which

This is the #1 rule to memorize:

```
+------------------------------------------+------------------+
|  Circuit Type                            |  Use             |
+------------------------------------------+------------------+
|  Combinational logic:  always @(*)       |  Blocking (=)    |
|  Sequential logic:     always @(posedge) |  Non-blocking (<=)|
+------------------------------------------+------------------+
```

**Combinational = no memory, no clock, output depends only on current inputs**
```verilog
// COMBINATIONAL — use blocking (=)
always @(*) begin
    sum   = a ^ b;
    carry = a & b;
end
```

**Sequential = has a clock, has memory (flip-flops)**
```verilog
// SEQUENTIAL — use non-blocking (<=)
always @(posedge clk) begin
    q <= d;
end
```

---

## NEVER Mix Them

Do NOT use both `=` and `<=` in the same `always` block.

```verilog
// BAD — never do this!
always @(posedge clk) begin
    a = b;       // blocking
    c <= a;      // non-blocking — DON'T MIX!
end
```

---

## Full Side-by-Side Example

**Blocking (=):**
```verilog
// Before: a=1, b=2, c=3
always @(posedge clk) begin
    a = b;       // a = 2 (immediately)
    b = c;       // b = 3 (immediately)
    c = a;       // c = 2 (a was already changed to 2!)
end
// After: a=2, b=3, c=2
```

**Non-blocking (<=):**
```verilog
// Before: a=1, b=2, c=3
always @(posedge clk) begin
    a <= b;      // a will get OLD b = 2
    b <= c;      // b will get OLD c = 3
    c <= a;      // c will get OLD a = 1
end
// After: a=2, b=3, c=1   (it's a rotation!)
```

---

## Summary Cheat Sheet

| Blocking `=`                        | Non-Blocking `<=`                      |
|-------------------------------------|----------------------------------------|
| Updates immediately                 | Updates at END of time step            |
| Next line sees NEW value            | Next line sees OLD value               |
| Like normal programming             | Like "everyone reads, then writes"     |
| Use for combinational `@(*)`        | Use for sequential `@(posedge clk)`    |
| Order of statements MATTERS         | Order of statements does NOT matter    |
