## FPGA
- Final target is ASIC Chip
- Simulation is too ideal
  - No noise
  - No timing violation
  - No I/O delay
  - Free Stimulus
  - No need to make sub-components
- FPGA
  - Mandatory because it can verify functionality with above things
  - Much faster when observing long-term simulation
    - OS boosting
    - UHD video play

#### FPGA vs BOARD
FPGA (Field Programmable Gate Array)
- Set of Logic and Memory
- RTL code will be programmed on FPGA
BOARD
- We need input/output for FPAG
- External standard physical interface will be provided by Board
- Each part can be connected to FPGA with ball

#### Xilinx Line-up
- High-cost
  - Virtex
    - Most expensive, Fastest, Largest
  - Kintex
    - Cost-effectiveness
  - Artix
    - Cheapest
- Low-cost
  - Spartan
    - Entry-level type
- ZYNQ
  - Processing System + Programmable Logic
  - SoC -> MP SoC -> RF SoC
- Ultrascale+
  - 14nm/16nm node
- Ultrascale
  - 20nm node
- 7-series
  - 28nm node
- 6-serise
  - 45nm node

#### Xilinx Part number
- XC
- FAMILY/NODE
- LOGIC
- PACKAGE TYPE
- PIN
- SPEED GARDE
- TEMPERATURE RANGE

#### User guide
- Board User guide
- FPGA User guide

#### Project 생성
- Create project
- Verilog 추가 및 생성
- Create IP
- Synthesis에 맞는 코드 작성
- Constraint 작성
- Implementation에 맞는 코드 작성
- Btistream 생성
- FPGA에 업로드

## FPGA에서 waveform 보기
#### ILA (chipscope)의 필요성
ILA (INtegrated logic Analyzer)
- called as chipscope before
- Can't detect any signal insdie FPGA only we can check is LED/7-segment
- Even we arleady check at simulation.
- Debug feature is more important at bigger system
- We can select signal & waveform at selected point
- We can set the capture window