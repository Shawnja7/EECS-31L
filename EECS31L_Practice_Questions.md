# EECS 31L Midterm Practice Quiz — QUESTIONS

---

## SECTION A: TRUE / FALSE

**1.** In Verilog, the order of concurrent statements (e.g., `assign` statements) affects the behavior of the model.
no 0 line 24 in three design styles

**2.** A `reg` variable can only be assigned inside an `always` or `initial` block.
True

**3.** The `===` operator in Verilog can compare values that include `x` and `z` bits.
yes its the logical operator that also evaluates x and z bits


**4.** `3'b101x === 3'b1011` evaluates to True.
false

**5.** Non-blocking assignments (`<=`) execute sequentially, meaning the result is visible to the next statement immediately.
false right hand side is exectuted immediately, but left hand side happens at end of time step, 

**6.** In a testbench module, the module has no input or output ports.
true

**7.** You can use the `assign` keyword inside an `always` block.
false you cannot 

**8.** A `wire` can be used on the left-hand side of a procedural assignment (`=` or `<=`) inside an `always` block.
only a reg wires can only be assigned in always blocks

**9.** Verilog is case-sensitive, meaning `MyModule` and `mymodule` are different identifiers.
true

**10.** A `for` loop inside an `always` block in Verilog executes sequentially.
anything inside an always block is sequential so true 

**11.** In structural design description, you instantiate gates or modules to build the circuit.
true

**12.** In dataflow description, the `always` block is the primary construct used.
false its the assign, always is behavioral, 
true always re assigns 
**13.** A concurrent assignment statement is executed as many times as the input value changes.

**14.** The `forever` loop in Verilog requires a delay statement (like `#5`) inside it; otherwise it causes an infinite loop that hangs the simulation.
true,

**15.** In a `casex` statement, both `x` and `z` values are treated as don't-care conditions.
true, casez ignore only z bits and case doesnt ignore anything 
---

## SECTION B: MULTIPLE CHOICE

**1.** Using the following Verilog code:
```verilog
reg [3:0] a;
a = 4'b1111;
a << 3;
```
What is the value of `a`?
1000
- (A) 4'b1110
- (B) 4'bxxxx
- (C) 4'b0001 
- (D) 4'b1000 this

---

**2.** Using the following Verilog code:
```verilog
reg [3:0] b;
b = 4'b1111;
b >> 3;
```
0001
What is the value of `b`?

- (A) 4'b1110
- (B) 4'b1000
- (C) 4'bxxxx
- (D) 4'b0001 this 
- (E) 4'bzzzz

---

**3.** Using the following Verilog code:
```verilog
reg signed [3:0] c;
c = 4'b1010;
c >>> 1;
```
What is the value of `c`?
msb = 1
1101
- (A) 4'b1101 this
- (B) 4'b0101
- (C) 4'b1010
- (D) 4'b0100

---

**4.** Using the following Verilog code:
```verilog
reg [3:0] d;
d = 4'b1010;
d << 1;
```

What is the value of `d`?

- (A) 4'b0100 this
- (B) 4'b1010
- (C) 4'b0101
- (D) 4'b1100

---

**5.** Using the following Verilog code:
```verilog
reg [3:0] e;
e = 4'b1010;
e >> 1;
```
What is the value of `e`?
0101
- (A) 4'b1101
- (B) 4'b0101 this
- (C) 4'b1010
- (D) 4'b0100

---

**6.** Which of the following is NOT a basic statement of behavioral description in Verilog?

- (A) assign statement this, dataflow, not structural, 
- (B) always block 
- (C) if statement
- (D) case statement

---

**7.** What is the purpose of the `initial` block in Verilog?

- (A) To initialize wire variables 
- (B) To execute code only once at the beginning of simulation run only once at time 0
- (C) To define the sensitivity list for always blocks
- (D) To create continuous assignments

---

**8.** What can you say about this declaration?
```verilog
reg signed [3:0] anArray [0:4];
```

- (A) An Array has 4 elements and each element is 5 signed bits
- (B) An array of 12 elements
- (C) An Array has 5 elements and each element is 4 signed bits 
- (D) All the answers are correct

c
---

**9.** Given a 16-bit vector `data[15:0]`, which of the following Verilog expressions correctly extracts the 8 most significant bits (MSBs)?

- (A) data[8:15]
- (B) data[0:7]
- (C) data[15:8] bit 15, bit 14 goes down, -> -> -> to bit 0
- (D) data[7:0]

---

**10.** What is the result of this operation: `3'b101x === 3'b1011`?

- (A) True
- (B) False
false
=== needs all to be true
---

**11.** Which loop construct executes its body a fixed number of times without a condition check?

- (A) for
- (B) while
- (C) repeat this 
- (D) forever

---

**12.** What is the difference between blocking (`=`) and non-blocking (`<=`) in the following code?
```verilog
always @(posedge clk) begin
    a = b;    // Line 1
    c = a;    // Line 2
end
```

- (A) `c` gets the NEW value of `a` (which is `b`) this
- (B) `c` gets the OLD value of `a`
- (C) `a` and `c` are updated simultaneously 
- (D) This code produces a compilation error

---

**13.** What happens if the same code uses non-blocking instead?
```verilog
always @(posedge clk) begin
    a <= b;   // Line 1
    c <= a;   // Line 2
end
```

- (A) `c` gets the new value of `a` (which is `b`)
- (B) `c` gets the OLD value of `a` this 
- (C) This causes a syntax error
- (D) Both assignments are ignored

---

**14.** Which of the following correctly generates a clock with a period of 20 time units in a testbench?

- (A) `always clk = ~clk;`
- (B) `always #10 clk = ~clk;` this 
- (C) `assign #10 clk = ~clk;`
- (D) `initial forever clk = ~clk;`

---

**15.** Which design description style does this code use?
```verilog
and g1(out1, a, b);
or  g2(out2, out1, c);
```

- (A) Dataflow
- (B) Behavioral
- (C) Structural this 
- (D) Testbench

---

**16.** Which design description style does this code use?
```verilog
assign sum = a ^ b;
assign carry = a & b;
```

- (A) Dataflow this 
- (B) Behavioral
- (C) Structural
- (D) Testbench

---

**17.** Which design description style does this code use?
```verilog
always @(a or b) begin
    sum = a ^ b;
    carry = a & b;
end
```

- (A) Dataflow
- (B) Behavioral this 
- (C) Structural
- (D) Testbench

---

**18.** What is the value of `result`?
```verilog
reg [7:0] result;
result = {4{2'b10}};
```

- (A) 8'b10101010 this
- (B) 8'b00001010
- (C) 8'b10100000
- (D) 8'b01010101

---

**19.** What does `#5` mean in this code?
```verilog
assign #5 out = a & b;
```

- (A) The simulation runs for 5 time units then stops
- (B) The output `out` is updated 5 time units after `a` or `b` changes this 
- (C) The assignment executes 5 times
- (D) There is a 5-unit delay before simulation starts 

---

**20.** What's wrong with this code?
```verilog
module test(a, b, out);
    input a, b;
    output out;
    reg out;

    always @(a) begin
        out = a & b;
    end
endmodule
```

- (A) `out` should be a wire, not reg
- (B) Can't use `&` operator in always block
- (C) Incomplete sensitivity list — `b` is missing this 
- (D) Nothing is wrong

---

## SECTION C: SEQUENTIAL vs. CONCURRENT (Label S or C)

**1.** Consider the following Verilog code. Write 'S' if the statement is sequential and 'C' if it is concurrent:

```verilog
module example(input a, b, clk, output reg x, y);

    assign x = a & b;          // Statement 1: C

    always @(posedge clk) begin
        y = a | b;             // Statement 2: S
        x = y & a;             // Statement 3: S
    end

    assign y = a ^ b;          // Statement 4: C

endmodule
```

---

**2.** Label each statement S (sequential) or C (concurrent):

```verilog
module seq_conc(port_name1, port_name2, port_name3, port_name4);

    always @(*) begin
        for(counter_low; counter_up; counter_update)
        begin
            statement_1;    // S
            statement_2;    // S
        end
        statement_3;        // S
        statement_4;        // S
    end

    statement_5;            // C

endmodule
```

---

## SECTION D: ERROR IDENTIFICATION (Find the bug)

**1.** Find the error(s):
```verilog
module adder(a, b, sum) missing semicolin 
    input [3:0] a, b;
    output [3:0] sum;
    assign sum = a + b;
endmodule
```

---

**2.** Find the error(s):
```verilog
module mux(a, b, sel, out);
    input a, b, sel;
    output out; double instantiation of out 
    wire out;

    always @(a or b or sel) begin
        if (sel)
            out = a;
        else
            out = b;
    end
endmodule
```

---

**3.** Find the error(s):
```verilog
module test(A, B, Result);
    input A, B;
    output Result;

    always @(A or B) begin
        assign Result = A & B;
    end
endmodule
```

---

**4.** Find the error(s):
```verilog
module counter(clk, count);
    input clk;
    output [3:0] count;
    reg [3:0] count;

    always @(posedge clk)
        count <= count + 1;

    always @(negedge clk)
        count <= count - 1;
endmodule
```

---

## SECTION E: SIMPLE CODING — TESTBENCHES

**1.** Write a testbench for the following 2-to-1 MUX:
```verilog
module mux2to1(input a, b, sel, output out);
    assign out = sel ? a : b;
endmodule
```

---

**2.** Write a testbench for a D flip-flop that includes a clock generator:
```verilog
module dff(input clk, d, output reg q);
    always @(posedge clk)
        q <= d;
endmodule
```

---

**3.** Write a testbench for a 4-bit counter with synchronous reset:
```verilog
module counter4(input clk, rst, output reg [3:0] count);
    always @(posedge clk) begin
        if (rst)
            count <= 4'b0000;
        else
            count <= count + 1;
    end
endmodule
```
