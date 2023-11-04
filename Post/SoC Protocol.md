## SRAM (Static Random-Access Memory)
AREA for storing bit
- F/F needs 24T for storing 1 bit, SRAM consumes 1/4 of F/F's
MUX & Demux
- for MUX logic, it's too burden to access over 64 depth

#### Strobe
- enable: en, cs ,we, re
- handshake: req & ack, valid & ready
#### Control (header)
- id, address, length, attribute
#### Data
- Data in (usually at same timing)
- Data out (same ~ +N cycle)

## SRAM interface
#### SRAM parameter & port
- parameter
  - width: the bit width for access at a time
  - depth: the number of element
- port
  - clk: cynchronus
  - rstn: optional, many SRAM don't have rstn
  - cs: with assertion, SRAM can operate write or read
  - we: with assertion, SRAM do write operation
  - addr: address for which cell should be accessed write and read will share same port
  - din: write data
  - dout: read data
  
#### SRAM interface behavior
- write operation
  - cs, we
  - addr, din
- read operation
  - cs, ~we
  - addr, dout

## SRAM Modeling
- Limitation
- Declare & Modeling
- Parameter
- Naming
- Define

#### DUT
```verilog
module sram_model #(
    parmeter    DEPTH=8,
    parmeter    WIDTH=32,
    parmeter    DEPTH_LOG=$clog2(DEPTH)
)(
    input                       clk,
    input                       cs, we,
    input       [DEPTH_LOG-1:0] ad,
    input       [WIDTH-1:0]     din,
    output reg  [WIDTH-1:0]     dout
);
    reg [WIDTH-1:0] mem[DEPTH-1:0];
    
    initial begin
        for (int i = 0; i<DEPTH; i++) mem[i] = 0;
    end
    
    always @(posedge clk)
        if (cs & we)    mem[ad] <= din;
        else if (cs)    dout <= mem[ad];
endmodule
```

#### Testbench
```verilog
module tb_sram;
    sram_model #(.DEPTH(S), .WIDTH(32)) u_sram_8x32 (clk, cs, we, ad, din, dout);
endmodule
```

#### SRAM Wrapper
```verilog
module sram_wrapper_64x8 (
    input                       clk,
    input                       cs, we,
    input       [DEPTH_LOG-1:0] ad,
    input       [WIDTH-1:0]     din,
    output reg  [WIDTH-1:0]     dout
);
`ifdef SIM
    sram_model #(64, 8) u_sram (clk, cs, we, ad, din, dout);
`elsif FPGA
    sram_fpga_ip_64x8   u_sram (clk, cs, we, ad, din, dout);
`else
    sram_asic_ip_64x8   u_sram (clk, cs, we, ad, din, dout);
`endif
endmodule
```

#### Various kinds of SRAM
- TPSRAM
- DPSRAM

## FIFO
Communication between different Time-Line
- bigger size FIFO makes handle timing easier
- Calculate optimized DEPTH for control
- Avoid overflow & underflow

#### FIFO Modeling
- Storage
- Control Logic (pointer)
- Control Logic (full, empty, af, ae)
- Parameter
- Naming
- Define

#### CDC & Async FIFO
- Clock Domain Crossing
  - Very careful when using multiple clock
  - Stabilize signals when crossing the clock domain
- Async FIFO
  - Use dual clock
  - Very useful IP for transferring data between different clock
- CDC's issue
  - can't deliver the data at real-time
  - too much latency burden to stabilize the signals

#### Asynchronous FIFO
- Pushing Data & Popping Data are easy. Each has separate domain
- Handling control signal is difficult, need gray counter.

## AMBA
#### Bus
- every block needs to communicate with others
- It can be too complicate if we connect each block independenrly
- BUS is likely literally bus on highway

- Components
  - Interconnect
  - Master
  - Slave
- Protocol
  - APB
  - AHB
  - AXI
#### APB interface
Write transfer
- Master
  - PENAABLE, PWRITE, PADDR, PWDATA
  - PSTRB
- DECODER
  - PSEL
  - PASSR
- SLAVE
  - PREADY
Read transfer
- Master
  - PENABLE, PWRITE, PADDR
- DECODER
  - PSEL
  - PADDR
- SLAVE
  - PREADY
  - PRDATA

#### AHB interface
Basic transfer
- Read
  - HRDATA follows next to HADDR
  - Separate phase
- Write
  - HWDATA follows next ot HADDR
- Mixed
  - Each transfer follows rule
- Transfer types
  - NONSEQ is mostly like PENABLE signal
  - SEQ will follow previous information ADDR will be sum of previous HADDR and HSIZE

#### APB/AHB Modeling
- APB Modeling
  - Design type
    - Slave >> Bridge >> Master >> Interconnect
  - Most Usage: Configure register (SFR)
  - Design
    - Very easy, can make strobe for write/read
    - case decoder
- AHB Mdoeling
  - Design type
    - Slave >= Master >> Bridge >> Interconnect
    - Usage: Data transfer
    - Design
      - Need to consider transaction type

#### AXI channels