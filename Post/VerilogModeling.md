## Verilog Modeling

#### Gate Level Modeling
- Verilog can provide gate primitive cell which can be instantiated
- Describe each gate with each instance including connection

```verilog
module mux_gatelevel(
    input   a, b,
    input   A, B, C, D,
    output  Q
);

    wire    a_not, b_not;
    wire    A_pick, B_pick, C_pick, D_pick;
    
    not(a_not, a);
    not(b_not, b);
    
    and(A_pick, a_not, b_not, A);
    and(B_pick, a    , b_not, B);
    and(C_pick, a_not, b    , C);
    and(D_pick, a    , b    , D);
    
    or (Q, A_pick, B_pick, C_pick, D_pick);
    
endmodule
```

#### Data Flow Modeling
- assign statement is used with operators which is more effective to describe than gates
- Between assign statements, there is no sequence but dependency
- We can know how data flows in this modeling (which variables follow which operations)

```verilog
module mux_dataflow(
    input   a, b,
    input   A, B, C, D,
    output  Q
);

    wire    sel_0, sel_1, sel_2, sel_3;
    
    assign  sel_0 = ~a & ~b;
    assign  sel_1 =  a & ~b;
    assign  sel_2 = ~a &  b;
    assign  sel_3 =  a &  b;
    
    assign  Q = sel_0 ? A :
                sel_1 ? B :
                sel_2 ? C :
                sel_3 ? D : 0;
                
endmodule 
```

#### Behavioral Modeling
- Describe behavior in procedure blocks
- This is usually in “always” block
- This is normal way to implement sequential logic We can not save the value in variables with dataflow modeling (except tri-reg)
- Let’s deep-dive into behavioral modeling after practice

```verilog
module mux_behavior(
    input   a, b,
    input   A, B, C, D,
    output  Q
);
    
    reg Q;
    
    always @* begin
        case({b,a})
            'b00    : Q = A;
            'b01    : Q = B;
            'b10    : Q = C;
            'b11    : Q = D;
            default : Q = 0;
        endcase
    end
endmodule
```

- Procedure block
  - initial: execute only once at time zero
  - always : execute over and over, with condition defined at @
  - begin-end: sequential executes
  - fork-join : parallel executes

```verilog
initial begin
    a = 0;
    a = ~a;
end

always @(posedge clk, negedge rstn)
if (!rstn)  a <= 0;
else        a <= ~a;
```

```verilog
initial begin
    a = 0; b = 0; c = 0;
    
    #10 a = 1;    //time 10: a=1
    #20 b = 1;    //time 20: b=1
    #30 c = 1;    //time 30: c=1
end

initial begin
    a = 0; b = 0; c = 0;
    fork
        #10 a = 1;    //time 10: a=1
        #20 b = 1;    //time 20: b=1
        #30 c = 1;    //time 30: c=1
    join
end
```

- blocking assignment
  - executes in order, sequential
  - previous line blocks next line. it is why they are called blocking
  - a=b;
- Non-Blocking assignment
  - executes in parallel
  - previous line doesn’t block next line. it is why they are called non-blocking
  - a<=b;
  - Do not mix blocking & non-blocking in a procedure
- always @(sensitivity list)
  - always block executes when sensitivity list events
  - always @(a, b)means this procedure executes when a or b changes
  - always @(*): * means any signal event who affects output of this procedure
  - always @(posedge clk) means this procedure excutes when clk is rising
  - always @(negedge rstn) means this procedure excutes when rstn is falling
  - always @* , always @(posedge clk, negedgerstn) are most common usage
