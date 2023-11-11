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