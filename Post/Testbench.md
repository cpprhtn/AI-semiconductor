## Testbench

### TB Environment
#### DUT 
- Design Under Test 
- The grammar is more strict, because EDA tool can synthesize this part

#### TestBench
- Who provides an environment for verifying DUT
- It’s as important as DUT It’s as complex as DUT
- We need more test case for larger design
- Nowadays, System-Verilog is recommended for TB

### fork-join
- There are 3 ways to implement fork statement 
- all the parallel statements finish before join 
- any of the parallel statements finishes before join_any
- none of the parallel statements need to finish before join_none

### Enent, Wait
#### event
- This is another variable type for testbench 
- event happens after fixed time
- User can detect when event happens
#### wait
- Testbench can wait for a signal until when it gets certain value

### Force, Release
#### force
- We can force a certain value regardless of existing operation 
- The other signals which depends on forced signal are also affected
- We can use hierarchical reference
#### release
- User can release forced signal 
- The signal will be affected by existing operation after release
### File I/O
#### $fopen
binary type is supported
```verilog
int fin, fout;
fin = $fopen(“file_in.txt”, ”r”);
fout = $fopen(“file_out.txt”,”w”);
```
#### $fscanf
```verilog
reg [7:0] num, data;
$fscanf(fin, “%d %h”, num, data);
```
#### $fdisplay
```verilog
$fdisplay(fout, "%d %h", num, data);
```
#### $fclose
```verilog
$fclose(fin);
$fclose(fout);
```
### Etc
#### $random (seed)
Making random value with seed (optional)
#### $readmemh, $readmemb
- copy file’s txt or data to variables via hex or bin type 
- similar as $fscanf but much faster and simple for initializing memory
- we need to match vector size with txt
- start address, end address can also be defined