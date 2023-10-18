# Summary of Adders

## Ripple Carry Adder
- It is the smallest and simplest adder, for small bit widths it is not too slow either

# How are Adders Implemented in Verilog?

 You simply write assign  
 
 ```
 assign a = b + c;
 ``` 
 
 * The synthesizer will figure out what to do 
    - Most synthesizers have a large library of adder architectures
    - Depending on the design constraints, an architecture is chosen 
 
* FPGA design is slightly special 
    - Most modern FPGAs have embedded fast adders, these are faster than adders mapped to logic. 