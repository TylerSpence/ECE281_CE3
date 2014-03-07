ECE281_CE3
==========

CE3-Elevator Control


#Truth table
This truth table was derived from the state diagram and was the basis for the logic for the elevator.
![alt tag] (https://raw.github.com/TylerSpence/ECE281_CE3/master/truthtable.png)
#Screenshot of Moore device
![alt tag] (https://raw.github.com/TylerSpence/ECE281_CE3/master/moorescreenshot.png)
This data matches up correctly with the expected data as noted by the self checking testbench. 
#Screenshot of Mealy device
![alt tag] (https://raw.github.com/TylerSpence/ECE281_CE3/master/mealyscreenshot.png)
This data matches up correctly with the expected data as noted by the self checking testbench. 
#Moore shell code
The first change necesary for the moore code is to remove the combinational logic for the rising edge and put a more concise command in.
```vhdl
if rising_edge(clk) then
```
The rest of this shell is very strait forward.
#Moore testbench code
The code below is the initialization for the test bench and the first step of cycling through the different floors.
```vhdl
reset <= '1';
		wait for clk_period*2;
		reset <= '0';
		Assert floor = "0001" Report "fail" Severity ERROR;
		wait for clk_period*2;

		up_down <= '1' ;

		stop <= '0';
		wait for clk_period;
		stop <= '1';
		Assert floor = "0010" Report "fail" Severity ERROR;
		wait for clk_period*2;
```		
#Mealy shell code
The first difference in the mealy machine is that it is asyncronous.
```vhdl
if stop= '0' and rising_edge(clk) then
```
The second difference is in the output logic, where it is dependent on more than the state.
```vhdl
floor <= "0001" when (floor_state = floor1) else
        "0010" when (floor_state = floor2) else
        "0011" when (floor_state = floor3) else
        "0100" when (floor_state = floor4) else
        "0001";
nextfloor <= 	
        "0001" when (floor_state = floor1) and (stop = '1') else
        "0010" when (floor_state = floor2) and (stop = '1') else
        "0011" when (floor_state = floor3) and (stop = '1') else
        "0100" when (floor_state = floor4) and (stop = '1') else
        

		  "0010" when (floor_state = floor1) and (up_down ='1') and (stop = '0') else
        "0011" when (floor_state = floor2) and (up_down ='1') and (stop = '0') else
        "0100" when (floor_state = floor3) and (up_down ='1') and (stop = '0') else
        "0100" when (floor_state = floor4) and (up_down ='1') and (stop = '0') else
       

		  "0001" when (floor_state = floor1) and (up_down ='0') and (stop = '0') else
        "0001" when (floor_state = floor2) and (up_down ='0') and (stop = '0') else
        "0010" when (floor_state = floor3) and (up_down ='0') and (stop = '0') else
        "0011" when (floor_state = floor4) and (up_down ='0') and (stop = '0') else
		  "0001";
       
```       
#Mealy testbench code
The code for the mealy testbench code is the same as the moore.
#Answers to questions

Question:Is reset synchronous or asynchronous.

Answer: Synchronous.

Question:If up_down is set to "go up" and stop is set to "don't stop" which floor do we want to go to?

Answer:The floor above it, or if we are already on floor 4, we will stay there, unless the clock process continues to run for multiple cycles, then it will continue going up until we reach the top.

Question:If up_down is set to "go down" and stop is set to "don't stop" which floor do we want to go to?

Answer:The floor bellow it, or if we are already on floor 1, we will stay there, unless the clock process continues to run for multiple cycles, then it will continue going down until we reach the bottom.

Question:Will the Mealy machine be different from the Moore machine?

Answer: Yes, for reasons stated above related to clock and output.

Question:What value would we set to simulate a 50MHz clock?

Answer:20ns

#Debugging
It took a significant amount of time to figure out the proper way to do the outputs for the mealy machine.
Captain Silva affirmed in class that I needed to alter his code to make it better.
