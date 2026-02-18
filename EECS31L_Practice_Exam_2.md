# EECS 31L Practice Exam 2

**Time: 60 minutes | 11 Questions**
Focus: Weak areas + D-Latch, case/casex/casez, number representation, operators, vectors/arrays, wait statements

---

## Question 1 (Replication & Concatenation)

What is the value of `result`?

```verilog
reg [7:0] result;
reg [1:0] x = 2'b11;
reg [1:0] y = 2'b01;
result = {x, {3{y}}};
```
11 01 01 01

- (A) 8'b11010101 this
- (B) 8'b11010111
- (C) 8'b01010111
- (D) 8'b11101010

---

## Question 2 (Wire vs Reg / Output Declarations)

Which of the following module headers and declarations is CORRECT for a module whose output `z` is assigned inside an `always` block?

- (A)
```verilog
module foo(a, b, z);
    input a, b;
    output z;
    wire z;
```

- (B)
```verilog
module foo(a, b, z); this, if you assign somethign it needs to be a reg
    input a, b;
    output z;
    reg z;
```

- (C)
```verilog
module foo(a, b, z);
    input a, b;
    output z;
```

- (D) Both (B) and (C) are correct

---

## Question 3 (D-Latch vs D Flip-Flop)

Consider the following two modules:

**Module A:**
```verilog
always @(d or enable)
    if (enable)
        q = d;
```

**Module B:**
```verilog
always @(posedge clk)
    q <= d;
```

Which statement is TRUE?

- (A) Module A is a D flip-flop and Module B is a D-latch
- (B) Module A is a D-latch and Module B is a D flip-flop this, posedge clk is a flip flop holds memory. where d latch
- (C) Both are D-latches
- (D) Both are D flip-flops

---

## Question 4 (case / casex / casez)

Given this code, what is the value of `out` when `sel = 4'b10z0`?

```verilog
always @(*) begin
    casez (sel)
        4'b1zz0: out = 2'b00;
        4'b10z0: out = 2'b01;
        4'b1010: out = 2'b10;
        default: out = 2'b11;
    endcase
end
```

- (A) 2'b00
- (B) 2'b01 this z is a dont care
- (C) 2'b10
- (D) 2'b11

---

## Question 5 (Number Representation)

Match each expression to its decimal value:

```
(a) 8'hA5
(b) 4'd13
(c) 6'o17
(d) 8'b0000_1100
```

- (A) (a)=165, (b)=13, (c)=15, (d)=12
- (B) (a)=165, (b)=13, (c)=17, (d)=12
- (C) (a)=105, (b)=13, (c)=15, (d)=12
- (D) (a)=165, (b)=13, (c)=15, (d)=1100

---

## Question 6 (Vectors and Bit-Select)

Given:
```verilog
reg [7:0] data = 8'b1101_0110;
```

What is the value of `{data[7:4], data[1:0]}`?

- (A) 6'b110110
- (B) 6'b110101
- (C) 6'b110110
- (D) 6'b110110

Wait — let me fix the choices:

- (A) 6'b1101_10
- (B) 6'b1101_01
- (C) 6'b0110_10
- (D) 6'b0110_01

---

## Question 7 (Wait Statement)

What happens when this code executes?

```verilog
reg a;
reg ready = 0;

initial begin
    a = 0;
    wait (ready == 1) #10 a = 1;
end

initial begin
    #25;
    ready = 1;
end
```

At what simulation time does `a` become 1?

- (A) Time 10
- (B) Time 25
- (C) Time 35
- (D) `a` never becomes 1

---

## Question 8 (Operators: == vs ===)

What does each expression evaluate to?

```
(a) 4'b10x1 == 4'b10x1
(b) 4'b10x1 === 4'b10x1
(c) 4'b10x1 === 4'b1011
(d) 4'b10x1 == 4'b1011
```

- (A) (a)=1, (b)=1, (c)=0, (d)=x
- (B) (a)=x, (b)=1, (c)=0, (d)=x
- (C) (a)=x, (b)=1, (c)=0, (d)=0
- (D) (a)=0, (b)=1, (c)=0, (d)=x

---

## Question 9 (Arrays and Memory)

Given this declaration:
```verilog
reg [7:0] mem [0:3];
```

Which of the following statements is ILLEGAL?

- (A) `mem[0] = 8'hFF;`
- (B) `mem[2][3] = 1'b1;`
- (C) `mem[0] = mem[1] + mem[2];`
- (D) `wire [7:0] out = mem[1];`

---

## Question 10 (Error Identification)

Find ALL errors in this code:

```verilog
module counter(clk, rst, count);
    input clk, rst;
    output [3:0] count;

    always @(posedge clk) begin
        if (rst)
            count <= 4'b0000;
        else
            count <= count + 1;
    end
endmodule
```

- (A) `count` must be declared as `reg`
- (B) Sensitivity list should include `rst`
- (C) Cannot use `+` operator in always block
- (D) Both (A) and (B)

---

## Question 11 (Blocking vs Non-Blocking with Delays)

What are the values of `a`, `b`, and `c` at time 20?

```verilog
reg a, b, c;

initial begin
    a = 0; b = 0; c = 0;
end

always @(posedge clk) begin
    a <= 1;
    b <= a;
    c <= b;
end
```

Assume clock period = 10 (posedge at time 5, 15, 25, ...) and all regs start at 0.

- (A) a=1, b=0, c=0
- (B) a=1, b=1, c=0
- (C) a=1, b=1, c=1
- (D) a=0, b=0, c=0

---

---

# BONUS QUESTIONS (Additional Topics)

---

## Bonus 1 (casex behavior)

Given `sel = 4'b1x10`, what does this code output?

```verilog
always @(*) begin
    casex (sel)
        4'b1010: out = 3'b001;
        4'b1110: out = 3'b010;
        4'b1x10: out = 3'b011;
        default: out = 3'b000;
    endcase
end
```

- (A) 3'b001
- (B) 3'b010
- (C) 3'b011
- (D) 3'b000

---

## Bonus 2 (D-Latch: Latch Inference)

What hardware does this code create?

```verilog
always @(*) begin
    case (sel)
        2'b00: out = a;
        2'b01: out = b;
    endcase
end
```

- (A) A 4-to-1 MUX
- (B) A 2-to-1 MUX
- (C) A 2-to-1 MUX with a latch on `out`
- (D) A decoder

---

## Bonus 3 (Number Representation — Padding)

What is the value stored in `x`?

```verilog
reg [7:0] x;
x = 4'b1010;
```

- (A) 8'b0000_1010
- (B) 8'b1111_1010
- (C) 8'b1010_0000
- (D) 8'b1010_1010

---

## Bonus 4 (Vectors — Part Select)

Given:
```verilog
wire [15:0] bus = 16'hA3F0;
```

What is `bus[11:8]`?

- (A) 4'hA
- (B) 4'h3
- (C) 4'hF
- (D) 4'h0

---

## Bonus 5 (Wait + Clock Generation)

What is the clock period generated by this code?

```verilog
reg clk;
initial clk = 0;
always begin
    wait (clk == 0) #15 clk = 1;
    wait (clk == 1) #5 clk = 0;
end
```

- (A) 5 time units
- (B) 15 time units
- (C) 20 time units
- (D) 10 time units

---

## Bonus 6 (Concatenation + Shift Combined)

What is the value of `result`?

```verilog
reg [3:0] a = 4'b1100;
reg [3:0] b = 4'b0011;
reg [7:0] result;
result = {a, b} >> 4;
```

- (A) 8'b0000_1100
- (B) 8'b0011_0000
- (C) 8'b1100_0000
- (D) 8'b0000_0011

---

## Bonus 7 (casez with z input)

Given `sel = 4'b10z1`, what does this code output?

```verilog
always @(*) begin
    casez (sel)
        4'b10z1: out = 2'b00;
        4'b1001: out = 2'b01;
        4'b1011: out = 2'b10;
        default: out = 2'b11;
    endcase
end
```

- (A) 2'b00
- (B) 2'b01
- (C) 2'b10
- (D) 2'b11

---

## Bonus 8 (JK Flip-Flop)

In a JK flip-flop, what happens when J=1 and K=1?

- (A) Q holds its value (no change)
- (B) Q is set to 1
- (C) Q is set to 0
- (D) Q toggles (flips to opposite)
