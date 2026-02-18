# case, casex, and casez Statements

The `case` statement is like a switch statement in C. But Verilog has
THREE versions, and the differences are an exam favorite.

---

## Basic `case` Statement

```verilog
case (expression)
    value1 : statement1;
    value2 : begin statement2; statement3; end
    default : default_statement;
endcase
```

**Rules:**
1. Goes inside `always` or `initial` blocks only
2. Uses `===` (identity) for comparison — matches ALL 4 values (0, 1, x, z)
3. First match wins — stops checking after the first match
4. ALWAYS include `default` to avoid creating a latch
5. Single statements don't need `begin/end`, multiple do

**Example: 4-to-1 MUX**
```verilog
always @(*) begin
    case (sel)
        2'b00 : y = I0;
        2'b01 : y = I1;
        2'b10 : y = I2;
        2'b11 : y = I3;
        default : y = 0;
    endcase
end
```

---

## The Three Versions: case vs casez vs casex

The difference is how they treat `x` and `z` values:

| Statement | How it compares          | Treats as don't-care    |
|-----------|--------------------------|-------------------------|
| `case`    | Exact match (===)        | Nothing — x and z must match exactly |
| `casez`   | Ignores `z` bits         | `z` = don't care        |
| `casex`   | Ignores `x` AND `z` bits | `x` and `z` = don't care |

**"Don't care" means:** that bit position is IGNORED during comparison.
It matches anything.

---

## Worked Examples from the Slides

Your professor shows 4 scenarios. Here's each one:

### If `a = 1'b0`:

| Statement | Which executes? | Why                                    |
|-----------|-----------------|----------------------------------------|
| `case`    | statement1      | 0 === 0 → exact match                 |
| `casez`   | statement1      | 0 matches 0 → exact match             |
| `casex`   | statement1      | 0 matches 0 → exact match             |

All three agree when the value is a normal 0 or 1.

### If `a = 1'b1`:

| Statement | Which executes? | Why                                    |
|-----------|-----------------|----------------------------------------|
| `case`    | statement2      | 1 === 1 → exact match                 |
| `casez`   | statement2      | 1 matches 1 → exact match             |
| `casex`   | statement2      | 1 matches 1 → exact match             |

Again, all agree for normal values.

### If `a = 1'bz`:

| Statement | Which executes? | Why                                    |
|-----------|-----------------|----------------------------------------|
| `case`    | statement4      | z === z → exact match on the z item   |
| `casez`   | **statement1**  | z is don't-care → matches FIRST item (1'b0) |
| `casex`   | **statement1**  | z is don't-care → matches FIRST item (1'b0) |

**This is the tricky one!** In `casez` and `casex`, when `a` is `z`,
the `z` bit is ignored — so it matches ANYTHING. The first item in
the list wins because **first match wins**.

### If `a = 1'bx`:

| Statement | Which executes? | Why                                    |
|-----------|-----------------|----------------------------------------|
| `case`    | statement3      | x === x → exact match on the x item   |
| `casez`   | **statement3**  | x is NOT ignored by casez → matches x item |
| `casex`   | **statement1**  | x is don't-care → matches FIRST item (1'b0) |

**Key difference:** `casez` only ignores `z`, NOT `x`.
`casex` ignores BOTH `x` and `z`.

---

## The "Don't Care" Concept Visualized

Think of don't-care as a wildcard playing card:

```
case:   Every card must match exactly. No wildcards.
casez:  'z' cards are wild. They match anything.
casex:  Both 'x' and 'z' cards are wild. They match anything.
```

**Important:** Don't-care works BOTH ways:
- If the EXPRESSION has an x/z → that bit in the expression is wild
- If the CASE ITEM has an x/z → that bit in the item is wild

---

## Using z as Don't-Care in casez (Common Pattern)

You can use `z` (or `?`) in case items to mean "I don't care about this bit":

```verilog
always @(*) begin
    casez (instruction)
        4'b1??? : y = a;     // if first bit is 1, don't care about rest
        4'b01?? : y = b;     // if first two bits are 01
        4'b001? : y = c;     // if first three bits are 001
        default : y = d;
    endcase
end
```

`?` is the same as `z` in case items — just more readable.

---

## Summary Table

```
case:   STRICT comparison using ===
        x matches x, z matches z, 0 matches 0, 1 matches 1
        Nothing is treated as don't-care

casez:  z is don't-care (ignored in comparison)
        x still must match exactly
        First match wins

casex:  BOTH x and z are don't-care (ignored)
        First match wins
        Most flexible, least strict
```

| Input value | case matches...  | casez matches... | casex matches... |
|-------------|------------------|------------------|------------------|
| `1'b0`      | `1'b0` exactly   | `1'b0` exactly   | `1'b0` exactly   |
| `1'b1`      | `1'b1` exactly   | `1'b1` exactly   | `1'b1` exactly   |
| `1'bz`      | `1'bz` exactly   | First item (wild)| First item (wild)|
| `1'bx`      | `1'bx` exactly   | `1'bx` exactly   | First item (wild)|

---

## Common Exam Trap

**Q:** "Which statement is executed in `casez` if `a = 1'bz`?"

Many students pick statement4 (`1'bz`) because the value IS `z`.
But `casez` treats `z` as don't-care, so it matches the FIRST item
(`1'b0`) instead. **First match wins!**
