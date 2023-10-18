Party sourced from

 https://syssec.ethz.ch/content/dam/ethz/special-interest/infk/inst-infsec/system-security-group-dam/education/Digitaltechnik_14/13_AdvancedAdders.pdf

 and 

 https://nandland.com/full-adder/

# Summary of Adders

## Ripple Carry Adder
- It is the smallest and simplest adder, for small bit widths it is not too slow either

## Brent-King Adder
- Tradional Carry Lookahead Adder is BK with 4-bit groups 

## Sklansky Adder
- Multi-level parallel prefix adder with reduced delay.

## Huan Carlsson Adder
- A low-power adder designed for energy-efficient computation.

## Kogge Stone
- High-speed parallel prefix adder with improved performance.

## Carry Increment Adder
- Adder design focused on optimizing carry chain propagation for speed.

## Performance Comparison of Adders 
| Adder     | Area / Speed / Fan Out |
| ----------- | ----------- |
| RIpple Carry Adder    |small / slow / min|
| Sklansky Adder        |large / fast / high|
| Brent King Adder      |med / med / med|
| Kogge Stone Adder     |huge / fast / min|
| Haun Carlsson Adder   |large / fast / min|
| Carry Increment Adder |med / med / med|


# How are Adders Implemented in Verilog?
From Nandland (The Long Way)

https://nandland.com/full-adder/

```verilog
module full_adder 
  (
   i_bit1,
   i_bit2,
   i_carry,
   o_sum,
   o_carry
   );
 
  input  i_bit1;
  input  i_bit2;
  input  i_carry;
  output o_sum;
  output o_carry;
 
  wire   w_WIRE_1;
  wire   w_WIRE_2;
  wire   w_WIRE_3;
       
  assign w_WIRE_1 = i_bit1 ^ i_bit2;
  assign w_WIRE_2 = w_WIRE_1 & i_carry;
  assign w_WIRE_3 = i_bit1 & i_bit2;
 
  assign o_sum   = w_WIRE_1 ^ i_carry;
  assign o_carry = w_WIRE_2 | w_WIRE_3;
 
 
  // FYI: Code above using wires will produce the same results as:
  // assign o_sum   = i_bit1 ^ i_bit2 ^ i_carry;
  // assign o_carry = (i_bit1 ^ i_bit2) & i_carry) | (i_bit1 & i_bit2);
 
  // Wires are just used to be explicit. 
   
endmodule // full_adder
```

 Or you can simply write 
 
 ```verilog
 assign a = b + c;
 ``` 
 
 * The synthesizer will figure out what to do 
    - Most synthesizers have a large library of adder architectures
    - Depending on the design constraints, an architecture is chosen 
 
* FPGA design is slightly special 
    - Most modern FPGAs have embedded fast adders, these are faster than adders mapped to logic. 

So for the most part, don't worry about it too much?

## Ripple Bit Adder

The tool does this automatically but for learning purposes from Nandland 

```verilog
module ripple_carry_adder 
  #(parameter WIDTH)
  (
   input [WIDTH-1:0] i_add_term1,
   input [WIDTH-1:0] i_add_term2,
   output [WIDTH:0]  o_result
   );
     
  wire [WIDTH:0]     w_CARRY;
  wire [WIDTH-1:0]   w_SUM;
   
  // No carry input on first full adder  
  assign w_CARRY[0] = 1'b0;        
   
  genvar             ii;
  generate 
    for (ii=0; ii<WIDTH; ii=ii+1) 
      begin
        full_adder full_adder_inst
            ( 
              .i_bit1(i_add_term1[ii]),
              .i_bit2(i_add_term2[ii]),
              .i_carry(w_CARRY[ii]),
              .o_sum(w_SUM[ii]),
              .o_carry(w_CARRY[ii+1])
              );
      end
  endgenerate
   
  assign o_result = {w_CARRY[WIDTH], w_SUM};   // Verilog Concatenation
 
endmodule // ripple_carry_adder
```

Testbench

```verilog
module ripple_carry_adder_tb ();
 
  parameter WIDTH = 2;
 
  reg [WIDTH-1:0] r_ADD_1 = 0;
  reg [WIDTH-1:0] r_ADD_2 = 0;
  wire [WIDTH:0]  w_RESULT;
   
  ripple_carry_adder #(.WIDTH(WIDTH)) ripple_carry_inst
    (
     .i_add_term1(r_ADD_1),
     .i_add_term2(r_ADD_2),
     .o_result(w_RESULT)
     );
 
  initial
    begin
      #10;
      r_ADD_1 = 2'b00;
      r_ADD_2 = 2'b01;
      #10;
      r_ADD_1 = 2'b10;
      r_ADD_2 = 2'b01;
      #10;
      r_ADD_1 = 2'b01;
      r_ADD_2 = 2'b11;
      #10;
      r_ADD_1 = 2'b11;
      r_ADD_2 = 2'b11;
      #10;
    end
 
endmodule // ripple_carry_adder_tb
```