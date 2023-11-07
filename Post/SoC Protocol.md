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

## AMBA (advanced Microcontroller Bus Architeucture)
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
- Separate channels for Address & Data
- More powerful then separate phase
- Channel dependencies
  - VALID, READY for handshake

#### AXI interface
Basic transactions
- Concept of separate channel, needs ID/SIZE/LEN/BURST for specifying request
- Burst operation
- QoS
  - AWQOS/ARQOS, 4bit, optional
  - Higher value means higher priority, it affects arbitration
- REGION
  - AWREGION/ARREGION, 4bit, optional
  - Provide another address map, Different REGION means different address
- LOCK/PROT/CACHE
  - Similar with AHB
  - Locked transfer, Protection, Memory type
- USER
  - AWUSER/ARUSER
  - User defined signals

#### AXI interface Modeling
- Design type
  - Slave, Master > imterconnect > Bridge
- Usage: Data transfer
  - DRAM Controller
  - Graphic core
- Write transaction
  - each W channel should reflect each AW's info unitl wlast
  - It is necessary to have enough space to store
  - AW's info can be deleted when corresponding B is issued
- Read transaction
  - each R channel should relfect each AR's info until rlast
  - Same as Write
- Valid & Ready control
  - Ready should be asserted when it can accept
  - usually, Slave de-assert wready when it doesn't get aw info
- Various size support
  - Depends on size, slave should gather and split the data
  - It can make wready or rvalid not keeping enable state

#### AXI interconnect
- Converter
  - There are lots of Masters and Slaves
  - All of them don't have same bit width, frequency, version we need converter
- Arbiter/Decoder
  - There are lots of Masters, and Slaves
  - need to define which slave and master will be picked Input stage means converter which introduced previous page
  - Decoder is simple because we only adjust address and region Arbitration is complex because multiple master will access simultaneously
- Arbitration schemes
  - some rules for arbitration
  - RR, LRG with priority

## Serial
#### I2C interface
- I2C port
  - SCL / Controller / Control
  - SDA / Controller Device / Address & Data (bi-direction)
- Signal behavior
  - DATA, START, STOP
- I2C behavior
  - Between START and STOP, Data will be transmitted in 8bit unit + ACK
  - START -> ADDRESS, R/W -> DATA (repeat N times) -> STOP
- I2C connection
  - only Controller ASIC can drive SCL signal
  - Address, Write Data will be transmitted from Controller ASIC on SDA pin Read Data can be driven by other devices on SDA (bi-directional)

#### I2C Modeling
- FSM Controlled by
  - START condition
  - Address Detect
  - R/W detect
  - STOP condition
- SDA direction
  - When Read detected, direction should be converted
  - default: SDA = 'bZ;
- SERDES
  - 1bit interface should be converted to parallel
  - data_in <= {data_int[N-1:0], SDA}; data_out <= data_out >> 1; assign SDA = data_out[0];

#### SPI Modeling
- Configure
  - Many options are already set by registers rather than SPI i/f
- Select
  - Slave can be picked by SS_n
  - No need of detect address like I2C, which means no need of FSM
- Data
  - One set of 8bit is complete unit: MOSI/MISO
  - No dependency with another 8bit set, No need FSM
  - No start/stop condition by data pin, it can be controlled by SS_n
- SERDES
  - Same as I2C

#### UART Modeling
- Configure
  - Baud rate, Parity enable, # of Stop bit
  - Write Data/Write Enable/Write Complete
  - Read Data/Read Enable/Write Complete
  - Separate FSM is usually handled by APB control
- Select
  - No selcetion because UART is 1:1 communication
- Data
  - One set of 1+8+(1)+1,(2) bit is complete unit, No dependency with another set
  - some conditions are controlled by same pin
  - Data should be captured at sampling rate
- SERDES
  - Same as I2C, SPI