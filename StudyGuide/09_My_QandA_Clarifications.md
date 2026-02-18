# My Q&A Clarifications

These are questions I had while studying, with clear answers.

---

## Q1: "What does 'left-hand side must be wire' mean in dataflow?"

In a dataflow `assign` statement, the variable on the **left side** of the `=`
(the one being assigned TO) must be a `wire`.

```verilog
assign Y = A & B;
//     ^
//     This is the "left-hand side" — it MUST be a wire
```

**Why?** Because `assign` creates a continuous connection — like physically
wiring two points together. Wires represent physical connections, so the
thing you're driving must be a `wire`.

**The opposite rule:** Inside an `always` block, the left-hand side must
be `reg`:

```verilog
always @(*) begin
    Y = A & B;
//  ^
//  This must be 'reg' because it's inside always
end
```

**Summary:**
| Context                | Left-hand side must be | Example               |
|------------------------|------------------------|-----------------------|
| `assign` (dataflow)    | `wire`                 | `assign Y = A & B;`  |
| `always` (behavioral)  | `reg`                  | `Y = A & B;`          |

---

## Q2: "Why do examples just say `input a` and `output s` without writing `wire` or `reg`?"

Because **`wire` is the default type** in Verilog.

When you write:
```verilog
input a;
output s;
```

Verilog secretly treats them as:
```verilog
input wire a;     // 'wire' is assumed automatically
output wire s;    // 'wire' is assumed automatically
```

You NEVER need to write `wire` explicitly — it's always the default.

**When DO you need to write something explicitly?**
Only when an output is assigned inside an `always` block. Then you must
say `reg`:

```verilog
output reg s;     // MUST write 'reg' because s is assigned in always
```

**Example showing both cases:**

```verilog
// Dataflow style — wire is default, no need to write it
module half_adder(input a, input b, output s, output c);
    assign s = a ^ b;    // s is wire (default) — works with assign
    assign c = a & b;    // c is wire (default) — works with assign
endmodule
```

```verilog
// Behavioral style — must explicitly declare reg
module half_adder(input a, input b, output reg s, output reg c);
    always @(*) begin
        s = a ^ b;       // s is reg — works inside always
        c = a & b;       // c is reg — works inside always
    end
endmodule
```

**Key takeaway:** `input` and `output` are wires by default. You only
add `reg` when you assign inside `always`.

---

## Q3: "Is the wire/reg stuff defined in the testbench?"

No — the `wire` vs `reg` type is defined in the **module itself**, not
in the testbench. But here's the twist:

**In the testbench, the rules FLIP:**
- DUT inputs → declared as `reg` in the testbench (because YOU drive them)
- DUT outputs → declared as `wire` in the testbench (because the DUT drives them)

```
                    TESTBENCH                          DUT (your module)
              ┌──────────────────┐              ┌──────────────────┐
              │                  │              │                  │
              │   reg a ─────────────────────────► input a (wire)  │
              │   reg b ─────────────────────────► input b (wire)  │
              │                  │              │                  │
              │   wire s ◄───────────────────────── output s       │
              │   wire c ◄───────────────────────── output c       │
              │                  │              │                  │
              └──────────────────┘              └──────────────────┘
```

**Why?**
- In the testbench, you assign values to `a` and `b` inside an `initial`
  block (procedural), so they must be `reg`
- The DUT drives `s` and `c`, so the testbench just observes them as `wire`

**Example:**
```verilog
// The module being tested
module half_adder(input a, input b, output s, output c);
    assign s = a ^ b;
    assign c = a & b;
endmodule

// The testbench
module half_adder_tb;
    reg a, b;           // reg because WE drive them in initial block
    wire s, c;          // wire because the DUT drives them

    half_adder uut(.a(a), .b(b), .s(s), .c(c));

    initial begin
        a = 0; b = 0; #10;    // we assign a and b — that's why they're reg
        a = 0; b = 1; #10;
        a = 1; b = 0; #10;
        a = 1; b = 1; #10;
        $finish;
    end
endmodule
```

---

## Q4: "What does `always @(a, b)` mean? And `@(*)`? And `@(posedge clk)`?"

The `@(...)` part is called the **sensitivity list**. It tells the
`always` block: "Hey, only wake up and run when THESE signals change."

Think of it like a doorbell. The code inside the `always` block is
sleeping. The sensitivity list is the doorbell — it only rings when
the listed signals change.

### `always @(a, b)` — Trigger on specific signals

```verilog
always @(a, b) begin
    y = a & b;
end
```

This means:
- **Watch `a` and `b`**
- Whenever `a` changes OR `b` changes → run the code inside
- If `a` and `b` stay the same, the block does NOTHING (it sleeps)

You can also write it with `or` instead of a comma — they mean the same thing:
```verilog
always @(a or b)     // same as @(a, b)
```

**The danger:** If you forget a signal, the block won't trigger when
that signal changes. For example:
```verilog
always @(a) begin       // OOPS — forgot 'b'!
    y = a & b;          // if only b changes, this block WON'T re-run
end                     // y will have a STALE value — BUG!
```

### `always @(*)` — Trigger on ALL signals (the safe option)

```verilog
always @(*) begin
    y = a & b;
end
```

The `*` is a **wildcard** — it means "watch EVERY signal that's read
inside this block." Verilog automatically figures out the list for you.

`@(*)` is equivalent to `@(a, b)` here, but you don't have to think
about it. **Always use `@(*)` for combinational logic** — it prevents
the "forgot a signal" bug.

### `always @(posedge clk)` — Trigger on clock edge

```verilog
always @(posedge clk) begin
    q <= d;
end
```

This means:
- **Only wake up on the RISING EDGE of `clk`**
- Rising edge = the moment `clk` goes from 0 to 1

```
clk:   0   1   0   1   0   1
       ┃   ↑   ┃   ↑   ┃   ↑
       ┃   │   ┃   │   ┃   │
       ┃   runs ┃   runs ┃   runs
       ┃   here ┃   here ┃   here
```

The code inside ONLY executes at those arrows — not when clk is
sitting at 1, not when it falls from 1→0, ONLY at the 0→1 transition.

**`negedge`** is the opposite — triggers on the FALLING edge (1→0):
```verilog
always @(negedge clk) begin
    // runs when clk goes from 1 to 0
end
```

### When to use which?

| Sensitivity list       | What it's for                    | Example circuit       |
|------------------------|----------------------------------|-----------------------|
| `@(*)`                 | Combinational logic (no memory)  | MUX, adder, AND gate  |
| `@(posedge clk)`       | Sequential logic (has memory)    | Flip-flop, counter    |
| `@(a, b)`              | Same as `@(*)` but manual        | Avoid — use `@(*)`    |
| `@(posedge clk or posedge rst)` | Sequential with async reset | Counter with reset |

### Complete example: D Flip-Flop with async reset

```verilog
module dff(input clk, input rst, input d, output reg q);

    always @(posedge clk or posedge rst) begin
        if (rst)
            q <= 0;        // reset has priority
        else
            q <= d;        // on clock edge, capture d
    end

endmodule
```

This block wakes up when:
- `clk` has a rising edge (0→1), OR
- `rst` has a rising edge (0→1)

If `rst` is high → q gets 0 (reset wins).
If `rst` is low and clock rises → q gets d (normal operation).

### Summary

```
@(a, b)       →  "run when a OR b changes"    (manual list)
@(*)          →  "run when ANYTHING changes"   (auto list — USE THIS)
@(posedge clk) → "run ONLY on rising clock edge" (for flip-flops)
@(negedge clk) → "run ONLY on falling clock edge"
```

---

## Q5: "What's the actual difference between a wire and a reg?"

The name `reg` is **misleading** — it does NOT always mean a hardware
register. It's just a Verilog keyword. Here's what they actually are:

### `wire` = a physical connection (like a real wire)

A wire doesn't store anything. It's just a connection between two points.
If nothing is driving it, it has no value.

Think of a real wire in a circuit — it doesn't "remember" anything.
It just carries whatever signal is being pushed through it RIGHT NOW.

```verilog
wire y;
assign y = a & b;    // y is always showing the CURRENT value of a & b
                      // the moment a or b changes, y changes instantly
```

If you disconnected `a & b` from the wire, `y` would go to an unknown
value (`x`) — it can't hold onto the old value.

### `reg` = a variable that can hold a value

A `reg` CAN store a value. It keeps its value until you explicitly
change it. Think of it like a variable in C — once you set `x = 5`,
it stays 5 until you change it.

```verilog
reg y;
always @(posedge clk) begin
    y <= a & b;    // y gets updated ONLY on the clock edge
                   // between clock edges, y HOLDS its old value
end
```

### The confusing part: `reg` doesn't always become a register in hardware

This is the #1 thing that confuses people. The name `reg` suggests
"register" but:

- `reg` inside `always @(posedge clk)` → YES, becomes a real flip-flop/register
- `reg` inside `always @(*)` → NO, becomes combinational logic (just like a wire!)

```verilog
// This reg becomes a REAL register (flip-flop) in hardware
always @(posedge clk) begin
    q <= d;              // q holds its value between clock edges
end

// This reg becomes just COMBINATIONAL LOGIC — no register!
always @(*) begin
    y = a & b;           // y updates immediately when a or b changes
end                      // acts like a wire, even though it's declared reg
```

So `reg` is really just Verilog saying: "this variable is assigned
inside a procedural block (`always`/`initial`)." That's ALL it means.
Whether it becomes an actual register depends on whether the `always`
block is clocked or not.

### Side-by-side comparison

```
                    wire                         reg
                    ────                         ───
What it is:         A connection                 A variable
Stores a value?     No (just passes through)     Yes (holds until changed)
Used with:          assign                       always / initial
Drives:             Continuously                 On event (clock, signal change)
Default for:        input, output                nothing (must write explicitly)
```

### Real-world analogy

```
wire = a pipe carrying water
       → water flows through it RIGHT NOW
       → turn off the faucet and the pipe is empty
       → it doesn't "remember" the water

reg  = a bucket
       → you pour water in, it STAYS there
       → it holds the water until you pour new water in
       → it "remembers" what you put in it
```

### When does `reg` actually create a register in hardware?

| Code pattern                    | Hardware result        | Has memory? |
|---------------------------------|------------------------|-------------|
| `always @(*) y = a & b;`       | Combinational (gates)  | No          |
| `always @(posedge clk) q <= d;`| Flip-flop (register)   | Yes         |
| `assign y = a & b;`            | Combinational (wire)   | No          |

**The rule:** A real hardware register is only created when you use
`always @(posedge clk)` or `always @(negedge clk)`. Everything else
is just combinational logic, whether you call it `wire` or `reg`.

---

## Quick Rule Sheet: wire vs reg

```
RULE 1: wire is ALWAYS the default. You never need to write it.

RULE 2: Use reg when you assign inside always or initial blocks.

RULE 3: Use wire (or just leave the default) when you use assign.

RULE 4: In a testbench:
        - DUT inputs  → reg  (you drive them)
        - DUT outputs → wire (DUT drives them)

RULE 5: You CAN'T use assign on a reg.
        You CAN'T assign to a wire inside always.
```

| Where is the assignment? | Left-hand side type | Keyword needed? |
|--------------------------|---------------------|-----------------|
| `assign Y = ...`         | `wire`              | No (default)    |
| `always` block           | `reg`               | Yes, must write `reg` |
| `initial` block          | `reg`               | Yes, must write `reg` |
| Testbench DUT input      | `reg`               | Yes, must write `reg` |
| Testbench DUT output     | `wire`              | No (default)    |

---

## Q6: "Where do `.x` and `.y` come from in explicit port mapping? How is it different from positional?"

The `.x` and `.y` come from the **original module definition**. They are
the PORT NAMES that whoever wrote the module chose.

Say someone wrote a full adder module like this:

```verilog
module fulladder(input x, input y, input cin, output sum, output cout);
    assign sum = x ^ y ^ cin;
    assign cout = (x & y) | (x & cin) | (y & cin);
endmodule
//                  ^       ^       ^          ^          ^
//              these are the port names: x, y, cin, sum, cout
```

Now when you INSTANTIATE (use) that module, you need to connect YOUR
signals to THOSE ports. There are two ways:

### Method 1: Positional (by order) — ORDER MATTERS

```verilog
fulladder u0(r1[0], r2[0], cin, result[0], c1);
//           ↓      ↓      ↓    ↓          ↓
//           x      y      cin  sum        cout
```

You don't write the port names. Verilog matches them **left to right**
in the SAME ORDER as the module definition.

```
Module definition:  fulladder(x,     y,     cin, sum,       cout)
Your instantiation: fulladder(r1[0], r2[0], cin, result[0], c1)
                              ↕      ↕      ↕    ↕          ↕
                              x=r1[0] y=r2[0] cin=cin sum=result[0] cout=c1
```

**The danger:** If you get the order wrong, signals connect to the
WRONG ports and your circuit is broken with NO error message.

```verilog
// OOPS — accidentally swapped cin and r2[0]
fulladder u0(r1[0], cin, r2[0], result[0], c1);
//           ↓      ↓    ↓
//           x      y    cin
//           OK    WRONG! cin went to y, r2[0] went to cin
```

### Method 2: Explicit (by name) — ORDER DOESN'T MATTER

```verilog
fulladder u0(
    .x(r1[0]),         // connect port 'x' to my signal 'r1[0]'
    .y(r2[0]),         // connect port 'y' to my signal 'r2[0]'
    .cin(cin),         // connect port 'cin' to my signal 'cin'
    .sum(result[0]),   // connect port 'sum' to my signal 'result[0]'
    .cout(c1)          // connect port 'cout' to my signal 'c1'
);
```

The `.portname(your_signal)` syntax means:
- `.x` = "the port called x IN THE ORIGINAL MODULE"
- `(r1[0])` = "connect it to MY signal r1[0]"

**Order doesn't matter** because you're explicitly naming each connection:

```verilog
// This is EXACTLY the same — just reordered
fulladder u0(
    .cout(c1),         // moved to top — doesn't matter!
    .cin(cin),
    .sum(result[0]),
    .y(r2[0]),
    .x(r1[0])
);
```

### Visual: Plugging wires into a chip

```
    YOUR SIGNALS                    FULLADDER MODULE
    ────────────                    ────────────────
                                   ┌──────────────┐
    r1[0]  ─────────── .x() ──────►│ x            │
    r2[0]  ─────────── .y() ──────►│ y        sum │──── .sum() ───── result[0]
    cin    ─────────── .cin() ────►│ cin     cout │──── .cout() ──── c1
                                   └──────────────┘
```

The `.portname()` is like labeling which plug on the chip you're
connecting your wire to.

### Side-by-side comparison

```verilog
// POSITIONAL — shorter but risky
fulladder u0(r1[0], r2[0], cin, result[0], c1);

// EXPLICIT — longer but safe and clear
fulladder u0(
    .x(r1[0]),
    .y(r2[0]),
    .cin(cin),
    .sum(result[0]),
    .cout(c1)
);
```

| Feature          | Positional            | Explicit (by name)      |
|------------------|-----------------------|-------------------------|
| Order matters?   | YES — must match      | NO — any order          |
| Easy to mess up? | Yes                   | No                      |
| Readable?        | Hard (need to check)  | Clear (ports labeled)   |
| Preferred?       | For small modules     | For everything else     |

---

## Q7: "Non-blocking says 'statements executed concurrently' — what does that mean?" (Lecture 5, Slide 24)

Slide 24 says:
- **Blocking (`=`):** "Statements are executed **sequentially** instead of concurrently"
- **Non-blocking (`<=`):** "Statements are executed **concurrently** instead of sequentially"

This sounds confusing, but it ties directly into how non-blocking works:

### Blocking = sequential (like normal code)

Each line WAITS for the previous line to finish before it runs.
Line 1 runs, THEN line 2 runs, THEN line 3 runs. One at a time.

```verilog
always @(posedge clk) begin
    a = 1;       // Step 1: runs FIRST, a becomes 1
    b = a;       // Step 2: runs SECOND, sees a=1, b becomes 1
    c = b;       // Step 3: runs THIRD, sees b=1, c becomes 1
end
// Result: a=1, b=1, c=1 (each line depends on the one before it)
```

It's like a line of dominoes — each one falls after the previous one.

### Non-blocking = concurrent (all at the same time)

ALL lines are evaluated at the SAME TIME using the OLD values.
No line waits for any other line. They all "run" simultaneously.

```verilog
// Before: a=0, b=0, c=0
always @(posedge clk) begin
    a <= 1;      // reads nothing, will assign 1
    b <= a;      // reads OLD a=0, will assign 0
    c <= b;      // reads OLD b=0, will assign 0
end
// Result: a=1, b=0, c=0 (all read at the same time, no waiting)
```

It's like three people all taking a photo of a whiteboard at the same
instant — they all see the SAME thing (the old values), regardless of
what anyone is about to write.

### Why "concurrent" matters for hardware

In real hardware, flip-flops all update at the SAME clock edge.
They don't take turns. When the clock ticks:
- ALL flip-flops read their inputs at the same instant
- ALL flip-flops update their outputs at the same instant

Non-blocking (`<=`) models this real hardware behavior.
That's WHY you use `<=` for sequential logic — it matches what the
actual flip-flops do.

```
Clock edge hits:
    ┌─────────────────────────────────────────┐
    │  ALL right-hand sides read AT ONCE      │  ← "concurrent read"
    │  (everyone sees the OLD values)         │
    └─────────────────────────────────────────┘
                        ↓
    ┌─────────────────────────────────────────┐
    │  ALL left-hand sides update AT ONCE     │  ← "concurrent write"
    │  (everyone gets their new value)        │
    └─────────────────────────────────────────┘
```

### The slide's point in one sentence

**Blocking = lines run one after another (sequential, like C code).**
**Non-blocking = all lines run at the same time (concurrent, like real flip-flops).**

---

## Q8: "If assign statements are concurrent (always on), why does the OR gate wait for the AND gate to finish?"

This is about this code:
```verilog
assign #10 mid = a & b;     // AND gate, 10ns delay
assign #5  y   = mid | c;   // OR gate, 5ns delay
```

You're right — **both gates ARE always on, running concurrently.**
The OR gate is NOT "waiting" for the AND gate. Here's what's actually
happening:

### Both gates are always watching their inputs

Think of each `assign` as a separate piece of hardware that is
ALWAYS running, ALWAYS watching:

```
AND gate: "I watch a and b. When either changes, I recalculate."
OR gate:  "I watch mid and c. When either changes, I recalculate."
```

They're both on. They're both independent. Neither one "calls" the other.

### So why does y update at time 15 instead of time 5?

Because the OR gate reacts to **its own inputs** — and its input is
`mid`, not `a`. The OR gate doesn't know or care about `a`. It only
watches `mid` and `c`.

Here's the timeline:

```
Time 0:    a changes
           │
           ├── AND gate sees a changed → starts calculating a & b
           │   (will finish in 10ns)
           │
           └── OR gate? It watches mid and c.
               Did mid change? NO (not yet).
               Did c change? NO.
               So the OR gate does NOTHING. It has no reason to wake up.

Time 10:   AND gate finishes → mid updates to new value
           │
           └── OR gate sees mid changed → NOW it wakes up!
               Starts calculating mid | c (will finish in 5ns)

Time 15:   OR gate finishes → y updates
```

### The key insight

The OR gate IS always on. But "always on" means "always WATCHING
its inputs." It doesn't mean it fires constantly — it fires when
its inputs change.

At time 0, `mid` hasn't changed yet. So the OR gate has nothing
to react to. It's watching, but nothing happened to `mid` yet.

At time 10, `mid` finally changes. NOW the OR gate sees a change
and reacts.

### Analogy

Think of two security cameras:

```
Camera 1 (AND gate): watches door A and door B
Camera 2 (OR gate):  watches hallway and window
```

Both cameras are always on, always recording. But:
- When someone opens door A, only Camera 1 reacts (because it watches doors)
- Camera 2 doesn't care about doors — it watches the hallway
- Only when someone ENTERS the hallway does Camera 2 react
- The person has to go through the door FIRST, then walk to the hallway

The cameras don't "wait for each other" — they each independently
react to their own inputs. But because the hallway is AFTER the door
(just like `mid` is the output of the AND gate), there's a natural
ordering of WHEN each one sees activity.

### What if c changed at time 0 instead?

```verilog
assign #10 mid = a & b;     // AND gate, 10ns delay
assign #5  y   = mid | c;   // OR gate, 5ns delay
```

If `c` changes at time 0:

```
Time 0:    c changes
           │
           ├── AND gate? Watches a and b. c didn't change. Does nothing.
           │
           └── OR gate sees c changed → starts calculating mid | c
               (will finish in 5ns)

Time 5:    y updates (only 5ns total — didn't go through AND at all!)
```

**The delay depends on WHICH input changed** — because different inputs
go through different paths. This is why the MUX example has different
delays for `a`, `gbar`, and `sel`.

### Summary

```
Q: "If they're concurrent, why does the OR gate wait?"
A: It DOESN'T wait. It just has nothing to react to yet.
   The OR gate watches mid, not a.
   mid doesn't change until time 10.
   So the OR gate naturally fires at time 10, not time 0.
   Both gates are always on — but each reacts to ITS OWN inputs.
```

---

## Q9: "How do you count the delay path in the MUX example? Why is gbar 3 gates?"

Here's the code again:
```verilog
assign #7 s1 = ~gbar;          // NOT gate
assign #7 s2 = ~sel;           // NOT gate
assign #7 s3 = ~s2;            // NOT gate
assign #7 s4 = a & s2 & s1;    // AND gate
assign #7 s5 = b & s3 & s1;    // AND gate
assign #7 y  = s4 | s5;        // OR gate
```

### The key rule: only count gates that ACTUALLY FIRE

When an input changes, you trace the path from THAT input to `y`.
Gates whose inputs DON'T change don't fire — they don't add delay.

### Case 1: `a` changes (everything else stays the same)

```
             s1 = ~gbar     → gbar didn't change → s1 stays the same
             s2 = ~sel      → sel didn't change  → s2 stays the same
a changes →  s4 = a & s2 & s1  → a changed! s4 recalculates (7ns)
             s5 = b & s3 & s1  → none of its inputs changed → stays same
             y  = s4 | s5      → s4 changed! y recalculates (7ns)
```

```
Time 0:   a changes
Time 7:   s4 updates (a went through the AND gate)
Time 14:  y updates (s4 went through the OR gate)

Path: a → s4(AND) → y(OR) = 2 gates = 14ns
```

s1 and s2 are already sitting at their values. They don't need to
recalculate because gbar and sel didn't change. So s4 only waits
for `a` — the ONE input that actually changed.

### Case 2: `gbar` changes (everything else stays the same)

```
gbar changes → s1 = ~gbar    → gbar changed! s1 recalculates (7ns)
               s2 = ~sel     → sel didn't change → stays same
               s3 = ~s2      → s2 didn't change  → stays same
```

At time 7, s1 updates. Now:
```
               s4 = a & s2 & s1  → s1 changed! s4 recalculates (7ns)
               s5 = b & s3 & s1  → s1 changed! s5 recalculates (7ns)
```

At time 14, both s4 and s5 update. Now:
```
               y = s4 | s5   → s4 and s5 changed! y recalculates (7ns)
```

At time 21, y updates.

```
Time 0:   gbar changes
Time 7:   s1 updates
Time 14:  s4 and s5 update (both saw s1 change)
Time 21:  y updates

Path: gbar → s1(NOT) → s4(AND) → y(OR) = 3 gates = 21ns
```

**gbar itself is just one NOT gate (s1). But the TOTAL path from gbar
to y goes through 3 gates: NOT → AND → OR.**

### Case 3: `sel` changes (the longest path)

```
sel changes → s2 = ~sel     → sel changed! s2 recalculates (7ns)
              s1 = ~gbar    → gbar didn't change → stays same
```

At time 7, s2 updates. Now TWO things happen in parallel:
```
              s3 = ~s2       → s2 changed! s3 recalculates (7ns)
              s4 = a & s2 & s1 → s2 changed! s4 recalculates (7ns)
```

At time 14, both s3 and s4 update. Now:
```
              s5 = b & s3 & s1 → s3 changed! s5 recalculates (7ns)
              y  = s4 | s5     → s4 changed! y recalculates (7ns)
```

At time 21, both s5 and y update. But WAIT — s5 just changed too!
```
              y = s4 | s5    → s5 changed! y recalculates AGAIN (7ns)
```

At time 28, y updates with the final value.

```
Time 0:   sel changes
Time 7:   s2 updates
Time 14:  s3 updates AND s4 updates (parallel — both saw s2)
Time 21:  s5 updates AND y updates (but y will change again!)
Time 28:  y updates AGAIN with final settled value

LONGEST path: sel → s2(NOT) → s3(NOT) → s5(AND) → y(OR) = 4 gates = 28ns
```

### Your trace was almost right! Here's where you went slightly off:

You said: "s2 is not sel and s1 is not gbar, meaning thats 2 x 7
for those two already"

**The fix:** When gbar changes, s2 does NOT recalculate. s2 = ~sel,
and sel didn't change. s1 and s2 are PARALLEL and INDEPENDENT —
s1 depends on gbar, s2 depends on sel. They don't wait for each other.

```
When gbar changes:
  s1 fires (depends on gbar)  ✓  takes 7ns
  s2 does NOTHING (depends on sel, which didn't change)  ✗
```

### How to count: trace the path, not add all gates

```
DON'T: add up every gate in the circuit
DO:    trace from the changed input → through gates that fire → to y
       only count gates that are actually in that chain
```

### Visual: The paths through the circuit

```
gbar ──→ [NOT s1] ──┬──→ [AND s4] ──┬──→ [OR y]
                    │               │
                    └──→ [AND s5] ──┘
                              ↑
sel  ──→ [NOT s2] ──┬──→ [AND s4]
                    │
                    └──→ [NOT s3] ──→ [AND s5]

a ───────────────────────→ [AND s4]
b ───────────────────────→ [AND s5]
```

Count how many gates each input passes through to reach y:
```
a:    s4 → y                    = 2 gates = 14ns
b:    s5 → y                    = 2 gates = 14ns
gbar: s1 → s4 → y              = 3 gates = 21ns
sel:  s2 → s3 → s5 → y         = 4 gates = 28ns (longest path)
```

---

## Q10: "What does 'initialize memory' mean, and why use a for loop?"

### What IS memory in Verilog?

Memory is an **array of registers** — like a table where each row stores some bits.

```verilog
reg [7:0] memory [0:15];
//  ^^^^            ^^^^
//  each slot       16 slots total
//  is 8 bits       (index 0 to 15)
```

**Think of it like a filing cabinet:**
- The cabinet has 16 drawers (slots 0 through 15)
- Each drawer holds an 8-bit number
- `memory[3]` means "open drawer 3 and look at what's inside"

### Why initialize it?

When simulation starts, every slot contains **`x` (unknown)** — like every
drawer is filled with mystery junk. If your design tries to READ from a slot
before you WRITE to it, you get `x` values that spread everywhere.

```
Before initialization:
memory[0] = xxxxxxxx    ← unknown!
memory[1] = xxxxxxxx    ← unknown!
memory[2] = xxxxxxxx    ← unknown!
...all x's...

After initialization:
memory[0] = 00000000    ← clean!
memory[1] = 00000000    ← clean!
memory[2] = 00000000    ← clean!
...all zeros...
```

### Why a for loop?

Because writing 16 separate lines is painful and doesn't scale:

```verilog
// WITHOUT for loop — you'd have to write:
memory[0] = 8'b0;
memory[1] = 8'b0;
memory[2] = 8'b0;
// ... 13 more lines ...
memory[15] = 8'b0;

// WITH for loop — same thing in 3 lines:
integer i;
initial begin
    for (i = 0; i < 16; i = i + 1)
        memory[i] = 8'b0;
end
```

And if your memory has 1024 slots? The for loop handles it the same way —
just change `16` to `1024`. Without the loop you'd need 1024 lines.

### When would you use memory?

- **RAM/ROM models** — storing data that can be read/written
- **Lookup tables** — preloaded values for computation
- **Register files** — like in a CPU, a small block of registers

**Bottom line:** "Initialize memory" = set all slots in an array to a known
value (usually 0) so you don't get `x` garbage when you read from them.
This is done in an `initial` block because it's a one-time setup for simulation.

---

## Q11: "What does 'out holds its previous value = latch' mean?" (Mistake 9)

In combinational logic, every output should be determined ONLY by the current
inputs — no memory, no "remembering."

Look at this broken code:
```verilog
always @(*) begin
    if (sel)
        out = a;
    // No else! When sel=0, nothing assigns to 'out'
end
```

When `sel = 1`: `out = a` — no problem.
When `sel = 0`: there's no assignment. So `out` keeps whatever value it had
before. It **remembers** its old value.

That "remembering" IS a latch. A latch is a hardware element that stores a
value. The synthesizer sees that `out` needs to hold its value when `sel=0`,
so it builds a latch — extra hardware you didn't want.

**Light switch analogy:**
- **With else (correct):** Switch up = light on, switch down = light off.
  Output always determined by current input.
- **Without else (latch):** Switch up = light on, switch down = light stays
  however it was last. The light "remembers" — that requires a latch.

**Fix:** Always assign `out` in every branch:
```verilog
always @(*) begin
    if (sel)
        out = a;
    else
        out = b;    // out ALWAYS gets a value — no latch needed
end
```

**Same problem with `case`:** If you don't cover all cases AND don't have a
`default`, the missing cases create latches too (Mistake 10).

---

## Q12: "How is `always #5 clk = ~clk` a period of 10, not 5?"

The `#5` is the delay between each **toggle**, not the full cycle.

```verilog
initial clk = 0;
always #5 clk = ~clk;
```

Trace it step by step:
```
Time 0:   clk = 0   (from initial)
Time 5:   clk = ~0 = 1   (toggle #1, waited 5)
Time 10:  clk = ~1 = 0   (toggle #2, waited 5)
Time 15:  clk = ~0 = 1   (toggle #3, waited 5)
Time 20:  clk = ~1 = 0   ...

clk:  0_____1_____0_____1_____0
      |  5  |  5  |  5  |  5  |
      |<--------->|
       one full cycle = 10
```

One full clock cycle = HIGH + LOW. Each half takes 5 time units:
- Half period = 5 (the `#5`)
- Full period = 5 + 5 = **10 time units**

**General rule:** `always #N clk = ~clk` gives a clock with period = **2N**.

---

## Q13: "How does `#5` relate to `=` vs `<=`? Does `=` make it instant?"

Yes. In `always #5 clk = ~clk`, the `=` assigns **instantly**. The `#5` is
what creates the timing gap. They do two separate jobs:

1. `#5` = "pause here for 5 time units"
2. `= ~clk` = "now instantly flip clk"

The `=` itself has zero delay. At time 5, clk snaps from 0 to 1 immediately.

**Would `<=` change anything here?**
No — `always #5 clk <= ~clk` behaves the same in this case because there's
only ONE assignment. The `=` vs `<=` difference only matters when you have
MULTIPLE assignments in the same block (like flip-flop code with several regs).

**How is this different from `assign` delays?**

| Context                     | What `#5` does                          | Assignment timing         |
|-----------------------------|-----------------------------------------|---------------------------|
| `always #5 clk = ~clk`     | Pauses execution for 5 units            | Instant after the pause   |
| `assign #5 y = a & b`      | Gate delay — output delayed 5 units     | 5 units after input changes |

In `always`: the **pause** comes first, then you assign instantly.
In `assign`: the input change triggers it, then the **output is delayed**.

---

## Q14: "How does non-blocking (`<=`) actually work? What do you mean 'end of time step'?"

Non-blocking works in TWO phases:

1. **READ phase (instant):** All right-hand sides are evaluated NOW using
   current values
2. **WRITE phase (end of time step):** All left-hand sides are updated
   simultaneously AFTER every read is done

### Example — Swap with `<=`

```verilog
// Before clock edge: a = 3, b = 7
always @(posedge clk) begin
    a <= b;    // READ b = 7 (save it for later)
    b <= a;    // READ a = 3 (still 3! hasn't changed yet)
end
// END OF TIME STEP: write a = 7, write b = 3
// Result: a = 7, b = 3  ← they SWAPPED!
```

Both reads happen first, THEN both writes happen together at the end.
That's why `a` still has its old value when `b <= a` reads it.

### Compare — NO swap with `=`

```verilog
// Before clock edge: a = 3, b = 7
always @(posedge clk) begin
    a = b;     // READ b=7, WRITE a=7 IMMEDIATELY
    b = a;     // READ a=7 (already changed!), WRITE b=7
end
// Result: a = 7, b = 7  ← no swap, just copied
```

With blocking, each line finishes completely before the next one runs.
So `a` is already 7 by the time `b = a` executes.

### Summary

| | Blocking `=` | Non-blocking `<=` |
|---|---|---|
| Read | Immediate | Immediate |
| Write | Immediate (before next line) | Deferred (end of time step) |
| Order matters? | YES | NO (all writes happen together) |
| Use for | Combinational logic (`@(*)`) | Sequential logic (`@(posedge clk)`) |
