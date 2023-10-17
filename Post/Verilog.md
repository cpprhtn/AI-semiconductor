## Semiconductor Ecosystem
Semiconductor Ecosystem에는 크게 4가지의 공정으로 나뉜다.
1. DESIGN
2. FOUNDARY
3. PACKAGING
4. PRODUCT

그중에 DESIGN 부분이 Verilog이 가장 많이 사용되는 구간이다.

이부분을 공부하면서 주워 들은 이야기인 `Wafer`, `Fab`에 대해서도 공부를 해보게 되었다.

* Wafer: 반도체를 만들기 위한 아주 flat한 원형 판을 의미한다.
* Fab: `Fabrication`의 약자로 Wafer를 생산하는 설비 전체를 Fab이라 부른다.

그렇다면 여기에서 더 나아가서 `Fabless`, `FOUNDARY`와 관련된 내용에 대해서도 정리해보았다.

- IDM(Integrated Device Manufacturer): 모든 반도체 생산 공정을 종합적으로 갖춘 기업을 의미한다.
- IP(intellectual property): 셀 라이브러리라고 하는 특정 설계 블록을 팹리스나 IDM, 파운드리 등에 제공하고 IP 사용에 따른 라이선스료, 로열티를 받는다. 즉, 설계 라이선스를 판매할 뿐 자신의 브랜드로 제품을 생산하지 않는다.
- Fabless: 팹리스는 반도체 설계를 전문적으로 하는 기업이다. 설계를 제외한 웨이퍼 생산, 패키징, 테스트 등은 모두 외주로 진행되며, 외주를 통해 생산이 완료된 칩의 소유권이나 영업권은 팹리스에 있어 자사 브랜드로 판매한다.
- Design House: 팹리스(설계)와 파운드리(생산)의 연결다리 역할을 하는 기업이다. 팹리스 기업이 설계한 제품을 각 파운드리 생산공정에 적합하도록 최적화된 디자인 서비스를 제공하는 역할을 한다. 
- FOUNDARY: 생산 공정을 전담하는 기업으로 자체 제품이 아닌 수탁생산을 주로 하고 있다. 즉 반도체 생산설비를 갖추고 있지만 직접 설계하여 제품을 만드는 것이 아니라 고객으로부터 위탁받은 제품을 대신 생산해 이익을 얻는 것이다.
- OSAT(Outsourced Semiconductor Assembly And Test):  반도체 패키징 및 테스트 수탁기업으로 어셈블리 기업, 패키징 기업이라고도 불린다. 폭넓은 의미에서 반도체 후공정 업계를 뜻한다.

위처럼 다양한 Semiconductor Ecosystem중에서 `FOUNDARY`, `OSAT`외에는 모두 DESIGN이 필요하게 되며, 해당 부분의 주력인 Verilog는 중요한 역할을 하고 있다.

## Verilog and Other Language

C/C++은 함수 기반 형태의 구조이지만 Verilog는 모듈 기반의 구조이다.
또한 C/C++은 SW를 위한 언어이며 Verilog는 HW를 위한 언어이다.

또한 아래 예시에 대한 해석에도 차이가 존재한다.

- 각 Line에 대한 해석
  - C/C++: 위에서부터 순차적으로 진행한다.
  - Verilog: Line에 관계없이 같은 틱(time)에 동작한다.
- 동작에 대한 해석 (c = a + b)
  - C/C++: 덧셈 Command 실행. 결과는 수행된 동작.
  - Verilog: 덧셈 로직 구현. 결과는 동작이 아닌 회로.


## Verilog 기본 문법 및 개념

#### How to print?
- $display have a role as similar as printf in C
- $monitor is active only when variables change

```verilog
`timescale 1ns/1ns
module hello_world;
    int test = 0;
    
    inital begin
        #10 $display("time %t : hello world %d ", $time, test++);
        #10
        $display("time %t : hello world %d ", $time, test++);
        $display("time %t : hello world %d ", $time, test++);
        
        $finish;
    end
    
    initial begin
        $monitor("monitor test %d", test);
    end
endmodule
```

#### How to debug
- $dumpfile define output files which can save waveform (*.vcd)
- $dumpvars define the range of waveform dump

```verilog
module dump_example;
    reg a, b;
    initial begin
            a=0; b=0;
        #10 a=1; b=0;
        #10 a=0; b=1;
        #10 a=1; b=1;
        $finish;
    end
    
    initial begin
        $dumpfile("dump_example.vcd");
        $dumpvars(1, dump_example);
        $display("test waveform dump");
    end
endmodule    
```

#### Data type
- Value Set
  - 0 : logic 0, false
  - 1 : logic 1, true
  - x : unknown
  - z : high-impedance, open connection
- Nets
  - physical connection between entities 
  - Do not store values This data type is always affected by driving circuit 
  - Normally used in combinational logic 
  - wire, tri, wand, wor, supply0, supply1, ……
  - assign wire_a= … ; module_a inst_a(wire_a)
  - module_binst_b(wire_b); module_c inst_c(wire_b);
- Variables
  - used in procedural blocks 
  - Store values 
  - Normally used in not only sequential logic but also combinational logic
  - reg, int, real, time
  - initial begin reg_a = …; end 
  - always @(*) begin reg_b= …; end
  - reg_a, reg_bkeeps its value before next line
  - always @(posedge clk) begin reg_c<= …; end
- Vectors
  - nets, variables are usually 1 bit type
  - We can define bit width “N” like
  - wire [N-1:0] wire_a; reg [N-1:0] reg_a;
- Array
  - Data type can have its dimensions
  - [N-1:0] after data type keyword means bit width=N (vector)
  - while [M-1:0]after variable name means its depth=M (scalar)
  - wire [N-1:0] array_a[M-1:0];
    reg [N-1:0] array_b[M-1:0]
  - 
#### Instantiation 
- Call sub-modules below module 
- Hierarchy between modules

#### Module 
- Design Unit in general
- Usually, one module in a file with same name

#### Port
- input & output & (inout) , 
- interface between modules 
- We can express bit width as data type

#### Parameter
- Bit width for vector or array can be various depending on situation
- for same function with different bit width, it’s too inefficient to make lots of modules for each bit width
- We can use parameters for variant number. We don’t need to fix bit width which is variant
- parameterized design is very recommended in field because it is very good to reuse

- parameter override
  - Several bit width should be used at multiple instance in hierarchy
  - We can control parameter’s value in upper hierarchy
  - defparam, parameter argument are the way
- localparam
  - This acts like parameter in a module but can’t be overridden by upper hierarchy

#### DEFINE
- Usage 
  - This role is similar with language C 
  - Sometimes, several lines should be compiled or not depending on situation define is frequently used with ifdef/else for covering this case
  - `define`, `ifdef`, `ifndef`, `else` in Verilog
  - Do not use too much because it can make worse readability

#### Operator
- Bit-part
  - wire [3:0] a;
  - wire[1:0] b, c; <br><br>
  - b=a[3:2];
  - c=a[1:0];

- Concatenation
  - a = {b, c};
  - {b, c} = a; <br><br>
  - Replication a = {2{b}, c};
  - Bit width of each variables are very important
- Conditional
  - a = b ? c : d;
- Arithmetic
  - a+b; a-b; a*b; a/b; a%b; a**b;
- Relational
  - a<b; a>b; a<=b; a>=b;
- Equality
  - a===b; a!==b; a==b; a!=b;
- Logical
  - a && b; a || b; !a;
- Bitwise 
  - ~a; a & b; a | b; a ^ b;
- Unary
  - &a; |a; ^a;
- Shift 
  - a >> 3; a << 3; a >>> 3; a <<< 3;