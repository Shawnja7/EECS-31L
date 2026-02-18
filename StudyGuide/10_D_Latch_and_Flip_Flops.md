# D-Latch, D Flip-Flop, and JK Flip-Flop

These are the fundamental memory elements in digital design.
They all STORE a value — that's what makes them different from gates.

---

## What's the Difference Between a Latch and a Flip-Flop?

```
LATCH:      Level-sensitive — output changes WHILE the enable is high
FLIP-FLOP:  Edge-sensitive  — output changes ONLY at the clock edge
```

| Feature          | Latch                        | Flip-Flop                    |
|------------------|------------------------------|------------------------------|
| Triggered by     | Signal LEVEL (high or low)   | Signal EDGE (rising/falling) |
| When it updates  | Anytime enable = 1           | Only at posedge/negedge clk  |
| Sensitivity list | `always @(d, e)`             | `always @(posedge clk)`      |
| Transparent?     | Yes (while enabled)          | No (only at edge)            |

---

## D-Latch (Level-Sensitive)

A D-Latch has two inputs:
- `d` = the data input (what you want to store)
- `e` = enable (the "gate" that lets data through)

**How it works:**
- When `e = 1`: output `q` follows `d` (transparent — like a wire)
- When `e = 0`: output `q` holds its last value (latched — like a bucket)

```verilog
module D_latch(d, e, q, q_bar);
    input d, e;
    output reg q, q_bar;

    always @(d, e) begin
        if (e == 1) begin
            q = d;
            q_bar = ~q;
        end
        // NO else! When e=0, q and q_bar keep their old values
        // This is intentional — it creates the latch behavior
    end
endmodule
```

**Key things to notice:**
1. Sensitivity list is `@(d, e)` — NOT `@(posedge clk)` (no clock edge!)
2. There's NO `else` branch — this is what makes it a latch
3. `q` and `q_bar` are `reg` because they're assigned inside `always`
4. Uses blocking `=` (not `<=`)

**Timeline:**
```
e:     1   1   0   0   1   1
d:     0   1   1   0   0   1
q:     0   1   1   1   0   1
       ^   ^   ^       ^   ^
       |   |   |       |   |
    follows d  holds   follows d again
```

---

## D Flip-Flop (Edge-Sensitive)

A D Flip-Flop captures `d` ONLY at the clock edge.
Between edges, the output doesn't change no matter what `d` does.

```verilog
module D_FF(clk, d, q, q_bar);
    input clk, d;
    output reg q, q_bar;

    always @(posedge clk) begin
        q <= d;
        q_bar <= ~d;
    end
endmodule
```

**Key differences from D-Latch:**
1. Uses `@(posedge clk)` — edge-triggered, not level
2. Uses non-blocking `<=` — because it's sequential logic
3. Output ONLY changes at the rising edge of clk

**D Flip-Flop with Async Reset:**
```verilog
module D_FF_reset(clk, rst, d, q);
    input clk, rst, d;
    output reg q;

    always @(posedge clk or posedge rst) begin
        if (rst)
            q <= 0;       // reset wins, regardless of clock
        else
            q <= d;       // normal: capture d at clock edge
    end
endmodule
```

---

## JK Flip-Flop (Edge-Sensitive)

The JK Flip-Flop is like a "programmable" flip-flop. The J and K inputs
tell it what to do at each clock edge:

| J | K | What happens at clock edge | Name       |
|---|---|---------------------------|------------|
| 0 | 0 | Q stays the same          | No change  |
| 0 | 1 | Q becomes 0               | Reset      |
| 1 | 0 | Q becomes 1               | Set        |
| 1 | 1 | Q flips to opposite       | Toggle     |

**Memory trick:** Think of J = "Jump to 1" (Set) and K = "Kill to 0" (Reset)

```verilog
module JK_FF(JK, clk, q, q_bar);
    input [1:0] JK;       // JK is a 2-bit input: JK[1]=J, JK[0]=K
    input clk;
    output reg q, q_bar;

    always @(posedge clk) begin
        case (JK)
            2'b00 : q = q;       // no change
            2'b01 : q = 1'b0;    // reset
            2'b10 : q = 1'b1;    // set
            2'b11 : q = ~q;      // toggle
        endcase
        q_bar = ~q;
    end
endmodule
```

**Key things to notice:**
1. `JK` is declared as a 2-bit vector `[1:0]`
2. Uses `case` statement to select behavior
3. `@(posedge clk)` — edge-triggered like all flip-flops
4. `q_bar` is always the complement of `q`

---

## Comparison Table

| Feature          | D-Latch            | D Flip-Flop         | JK Flip-Flop        |
|------------------|--------------------|----------------------|----------------------|
| Inputs           | d, enable          | d, clk               | J, K, clk           |
| Trigger          | Level (e=1)        | Edge (posedge clk)   | Edge (posedge clk)  |
| Sensitivity      | `@(d, e)`          | `@(posedge clk)`     | `@(posedge clk)`    |
| Assignment       | Blocking `=`       | Non-blocking `<=`    | Blocking `=` (slides)|
| Can toggle?      | No                 | No                   | Yes (JK=11)         |
| Can hold?        | Yes (e=0)          | Yes (between edges)  | Yes (JK=00)         |

---

## Common Exam Questions

**Q: What creates an unintentional latch?**
An `if` without `else` in `always @(*)` — because when the condition
is false, the output must hold its value, which requires a latch.

**Q: What's the difference between a latch and a flip-flop?**
Latch = level-sensitive (transparent while enabled).
Flip-flop = edge-sensitive (only updates at clock edge).

**Q: In the JK FF code, why is `q_bar = ~q` outside the case?**
Because `q_bar` is ALWAYS the opposite of `q`, regardless of which
case was selected. It runs after the case updates `q`.
