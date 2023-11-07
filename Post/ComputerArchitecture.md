## Computer Architecture
### Computer Hierarchy
- CPU
  - Registers
- CACHE
  - SRAM, L1, L2, L3
- MEMORY
  - DRAM
- (Non-Volatile Memory)
  - NVDIMM, PCM
- STORAGE
  - NAND
- (External Storage)
  - Magnetic Disk, tape

## CPU (Central Processing Unit)
- Von Neumann Architecture
  - CPU + Memory + I/O Device
  - CPU (Control Unit + Registers + Logic) Communicates with Memory and I/O
- Harvard Architecture
  - Instruction Memory + Data Memory Avoid to bottle-neck
  - Used in now a days

#### CPU Block diagram
- Components
  - Registers
    - SPR
      - Program Counter
    - GPR
  - ALU
    - add, sub, mul, ...
  - Control Logic
  - Instruction Memory
  - Data Memory

#### Registers
- GPR (General Purpose Register) 
  - Store value in general
  - Sometimes, stores as pair: {R1, R0}, {ROH, ROL}
- SPR (Special Purpose Register)

#### CPU Components
- Instruction Fetch
  - PC is pointing next instruction
- Decode
  - Based on ISA (Instruction Set Architecture)
  - Control Unit will behave as decode
- Execution
  - Register will be selected by decoder
  - ALU operation will be selected by decoder
- Memory
  - for LOAD/STORE, Memory operation is necessary
- Write Back
  - for Register write, need to save result to destination register

#### CPU Data path
- I-type, Load instruction
  - IF-ID-EX-MEM-WB
  - touch all stages
- I-tpye, Store instruction
  - IF-ID-EX-MEM-(WB)
  - No need to write reg
- I-tpye, Branch
  - IF-ID-EX-(MEM)-(WB)
  - No need to store
- J-type, Jump
  - IF-ID-(EX)-(MEM)-(WB)
  - No need to exe, store

#### Pipelining
- Laundry machine
- 5-stage pipelining

#### Active at same clock
- Instruction fetch
  - IMEM read data
  - New PC ready
  - IF/ID Din
- Instruction Decode
  - IF/ID Qout
  - GPR read data
  - Immediate data
  - ID/EX Din
- Excution
  - ID/EX Qout
  - ALU operation
  - EX/MEM Din
- Memory
  - EX/MEM Qout
  - DMEM read data
  - MEM/WB Din
- Write back
  - MEM/WB Qout
  - GPR write

#### Adjusting Timing
- Write back
  - In case of Load instruction
  - IF/ID Qout: Write register number
  - ME/WB Qout: Write register data
  - 3 clocks delay

#### Hazard
- Data Hazard
  - Forwarding
  - Stall
- Control Hazard
  - Branch
  - Bubble
- Multi-core Hazard
  - Structural Hazard
  - Data Hazard (Coherency)

## CACHE
- CPU can't remember everything, it need to access Memory
- Communication with Main Memory (DRAM) tekes so long time
- Need to save frequently used data in nearby Memory called by CACHE
- CACHE is made of SRAM, it is much faster than DRAM

#### CACHE Basics
- Hit
  - Data is in CACHE
  - Hit Latency: Data access time when Hit
  - Hit Ratio: percentage of hit
- Miss
  - Data is not in CACHE
  - Miss Penalty (Latency): Data access time when Miss
  - Miss Rate: percentage of Miss
- Latency
  - (Hit Latency * Hir Ratio) + (Miss Penalty * Miss Ratio)
  - Really important to reduce Miss Ratio
- Operation
  - ADDRESS = {TAG, INDEX, OFFSET} offset depends on DATA WIDTH
  - CHCHE is missed at initialization No Valid bit
  - with Valid bit, Compare TAG value
  - TAG match means hit, mismatch means miss
  - Bigger CACHE size can reduce Miss Ratio
  - Need to use more area
  - Bigger BLOCK size can affect LOCALITY
  - SPATIAL: GOOD (perfetch nearby address)
  - TEMPORAL: BAD (less index)

#### Associative CACHE
- Direct-mapped Cache
  - Each block can be assigned to only one position
  - 64:1 mapping: ex) Cache Line 0: 0/64/128/192/...
- Fully-Associative Cache
  - Each block can be assigned to any position
  - 4096:64 mapping: ex) Cache Line 0~63: 0/1/2/3/...
- Set-Associative Cache
  - Each block can be assigned to multiple postiton
  - 2-way, 128:2 mapping: ex) Cache Line 0~1: 0/32/64/96/...

#### CACHE Miss
- Compulsory miss
  - cold start miss, can't avoid
  - at initial state, there is no data in CACHE
- Capacity miss
  - CACHE size is smaller than access data
  - Solution: Large size
- Conflict miss
  - Lack of way (set-associative cache)
  - Solution: increase way, better eviction algorithm
  
#### CACHE Replacement
- Replacement (Eviction)
  - Direct-mapped Cache
    - No algorithm is necessary
  - Associative Cache
    - Select which way in eviction
    - Can't predict at 100% for future, but based on history need to define
- Replacement Policy (Eviction algorithm)
  - FIFO
    - Not efficient
  - RLU
    - Most famous one
  - LFU

#### CACHE Write
Write miss operation is not clear while Read is clear

- Write-policy
  - Write-through
    - Write data to DRAM immediately whenever write to $
    - Good for data persistency, Bad for effciency
  - Write-back
    - Don't write data to DRAM when writes to $
    - Write data to DRAM when the cache line is evicted
    - Good for efficiency (when lots of write at same address) Bad for data persistency

## MEMORY
#### DRAM structure
- Array
  - 2D array rather than 1D array
  - More dimension with BANK/BANK GROU/RANK
  - Lots of bits under red dots
- Structure
  - DRAM structure with BANK Address
  - Address consists of (RAMK, BG), BANK, ROW, COL
- Command
  - CKE/CS/RAS/CAS/WE + ADDRESS Bits
- One address points 8 Cells at 8 Chips Which means 64bit Data width at a time

#### DRAM interface
- READ
  - DQ after READ
  - DQS for strobe
  - Burst for nearby Col
- WRITE
  - DQ after READ
  - DQS for strobe
  - Brust for nearby Col
- MPR
  - Read DQ training by pre-defined pattern
  - After Read training by MPR, can start write-training

#### DDR
- Data rate
  - Bus Clock imporvement rather than core clock
  - until DDR3, by phase get 8bit prefetch
  - DDR4 is by Bank group, Data rate can be faster than cell access time we can overlap operations

## STORAGE (NAND Flash)
#### NAND Cell
- Floating gate Cell
- WRITE
  - use tunneling
  - PROGRAM (0)
    - Control Gate -> Increase Vth
  - Erase(1)
    - Substrate -> Decrease Vth
- READ
  - Detect Vth
  - 0: high Vth -> Switch off
  - 1: low Vth -> Switch on

#### NAND unit
- NAND string
  - a set of serial connection of NAND
  - consists of 32, 64 floating-gate cell
  - unit of making rather than control
- NAND page
  - minimum unit of R/W
  - 512B, 2KB, 4KB
- NAND block
  - minimum unit of erase
  - Small Block: 32 pages
  - Large Block: 64 pages

#### NAND vs NOR
- NAND Flash
  - serial connection
  - less contact -> less area
- NOR Flash
  - parallel connection
  - more contact -> more area

#### MAND problem & solution
- Cell endurance
  - Erase can damage CELL's life-time
  - Read doesn't affect
- Wear Leveling
  - Remap address for distribution
  - Change translation table periodically
- BER
  - NAND has higher BER
  - Can't guarantee every data from NAND Flash
- ECC
  - Using parity, Error bit can be corrected
  - Hamming is most simple one
  - More complex algorithm is used for NAND