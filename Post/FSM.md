## FSM 설계
#### Finite State Machine
- for Sequential Logic, output depends on not only input but also current stored information
- Current stored information is called states, When system has finite number of states, we can call it Finite State Machine 
- Useful way to control and check the progressing status of Design
- System behaves differently depending on state
- Each state can go to another state with certain condition Each state can have its own output
- State Machine is used for deciding other signal’s behavior

#### Moore vs Mealy
- Moore machine 
  - output depends on only current state 
  - output will change at only clock edge
  - safer than mealy machine because there is no asynchronous issue
- Mealy machine
  - output depends on both current state and input
  - react faster than moore machine because it doesn’t have to wait for 1 clock
  - can have less state than moore machine

#### FSM Coding
- next_state is implemented by case statements, combinational logic
- curr_state is F/F output of next_state
- Above 2 things can be combined into one procedure block
- localparam (parameter) is used for state encoding