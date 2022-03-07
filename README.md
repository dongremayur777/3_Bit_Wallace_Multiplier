# 3_Bit_Wallace_Multiplier
Mixed_Signal_Circuit
  * [Abstract](#abstract)
  * [Reference Circuit Details](#reference-circuit-details)
  * [Reference Circuit Diagram](#reference-circuit-diagram)
  * [Reference Circuit Waveform](#reference-circuit-waveform)
  * [Tools Used](#tools-used)
- [Simulation in ESIM](#simulation-in-esim)
  * [Verilog_Code](#verilog_code)
  * [Makerchip_Block](#makerchip_block)
  * [Carry_Block](#carry_block)
  * [Sum_Block](#sum_block)
  * [Parameters set for Voltage Source for Input A](#parameters-set-for-voltage-source-for-input-a)
  * [Parameters set for Voltage Source for Input B](#parameters-set-for-voltage-source-for-input-b)
  * [Parameters set for Voltage Source for Input C](#parameters-set-for-voltage-source-for-input-c)
  * [Transient Settings](#transient-settings)
  * [Schematic of Full_Adder using the above Blocks](#schematic-of-full_adder-using-the-above-blocks)
  * [Output Waveform](#output-waveform)
  * [Netlist](#netlist)
  * [Conclusion](#conclusion)
  * [Author](#author)
  * [Acknowledgement](#acknowlegement)
  * [References](#references)


## Abstract
Multiplication process is often used in digital signal processing systems, microprocessors designs, communication systems, and other application specific integrated circuits. Multipliers are complex units and play an important role in deciding the overall area, speed and power consumption of digital designs.A Wallace multiplier is a hardware implementation of a binary multiplier, a digital circuit that multiplies two integers. It uses a selection of full and half adders (the Wallace tree or Wallace reduction) to sum partial products in stages until two numbers are left.Compared to naively adding partial products with regular adders, the benefit of the Wallace tree is its faster speed. It has reduction layers, but each layer has only propagation delay. A naive addition of partial products would require time. As making the partial products is and the final addition is , the total multiplication is , not much slower than addition.
## Reference Circuit Details

The Wallace tree has three steps:
1)Multiply each bit of one of the arguments, by each bit of the other.
2)Reduce the number of partial products to two by layers of full and half adders.
3)Group the wires in two numbers, and add them with a conventional adder.
The input is 3-bit numbers which will be multiplied using the Wallace tree algorithm and it will produce a 6-bit product. This structure is implemented using half adders, full adders and AND gates. Initially, every bit is multiplied with every bit of the other number, then these partial products which have weight equal to the product of its factors are further reduced to obtain the respective weights by using half adders or full adders based on the size. The final result is calculated by the sum of all these partial products.

## Reference Circuit Diagram
<![Schematic](https://user-images.githubusercontent.com/59500283/157089103-de39dddf-1199-4e3d-8672-0ee1c45a7ad5.jpeg)>

## Reference Circuit Waveform
<![Waveforms](https://user-images.githubusercontent.com/59500283/157089156-d8fbe222-5b1b-44fa-b643-72e0748a3d3e.jpeg)>

## Tools Used:
• eSim (formerly known as Oscad/FreeEDA) is an open source EDA tool for circuit design, simulation, analysis and PCB design, developed by the FOSSEE team at IIT Bombay. Using open source software, such as KiCad, Ngspice, Python and Scilab, we have built an integrated open source EDA tool, eSim.
 
 <img width="980" alt="Screenshot 2022-03-07 at 8 41 18 PM" src="https://user-images.githubusercontent.com/59500283/157089564-f1b58be4-cafd-465c-8e46-189c9c6c1b43.png">

# Simulation in ESIM
The First Task we do here is to create a model of 3_bit_Wallace Multiplier using Makerchip and NGVeri where we write a verilog code for it.
##Verilog_Code
module mayur_half_adder(
    Data_in_A,
    Data_in_B,
    Data_out_Sum,
    Data_out_Carry
    );

    //what are the input ports.
    input Data_in_A;
    input Data_in_B;
    //What are the output ports.
    output Data_out_Sum;
     output Data_out_Carry;
     
     //Implement the Sum and Carry equations using Verilog Bit operators.
     assign Data_out_Sum = Data_in_A ^ Data_in_B;  //XOR operation
     assign Data_out_Carry = Data_in_A & Data_in_B; //AND operation
    
endmodule

module mayur_full_adder(
    Data_in_A,  //input A
    Data_in_B,  //input B
    Data_in_C,  //input C
    Data_out_Sum,
    Data_out_Carry
    );

    //what are the input ports.
    input Data_in_A;
    input Data_in_B;
     input Data_in_C;
    //What are the output ports.
    output Data_out_Sum;
     output Data_out_Carry;
     //Internal variables
     wire ha1_sum;
     wire ha2_sum;
     wire ha1_carry;
     wire ha2_carry;
     wire Data_out_Sum;
     wire Data_out_Carry;

     //Instantiate the half adder 1
    mayur_half_adder  ha1(
        .Data_in_A(Data_in_A),
        .Data_in_B(Data_in_B),
        .Data_out_Sum(ha1_sum),
        .Data_out_Carry(ha1_carry)
    );
    
    //Instantiate the half adder 2
    mayur_half_adder  ha2(
        .Data_in_A(Data_in_C),
        .Data_in_B(ha1_sum),
        .Data_out_Sum(ha2_sum),
        .Data_out_Carry(ha2_carry)
    );

    //sum output from 2nd half adder is connected to full adder output
    assign Data_out_Sum = ha2_sum;  
    //The carry's from both the half adders are OR'ed to get the final carry./
    assign Data_out_Carry = ha1_carry | ha2_carry;
    
endmodule

module mayur_wallace(A,B,prod);
    
    //inputs and outputs
    input [2:0] A,B;
    output [5:0] prod;
    //internal variables.
    wire s11,s12,s13,s22,s23,s32,s35,s34;
    wire c11,c12,c13,c22,c23,c32,c35;
    wire [2:0] p0,p1,p2,p3;

//initialize the p's.
    assign  p0 = A & {3{B[0]}};
    assign  p1 = A & {3{B[1]}};
    assign  p2 = A & {3{B[2]}};

//final product assignments    
    assign prod[0] = p0[0];
    assign prod[1] = s11;
    assign prod[2] = s22;
    assign prod[3] = s32;
    assign prod[4] = s34;
    assign prod[5] = s35;

//first stage
    mayur_half_adder ha11 (p0[1],p1[0],s11,c11);
    mayur_full_adder fa12(p0[2],p1[1],c11,s12,c12);
    mayur_half_adder ha15(p1[2],c12,s13,c13);

//second stage
    mayur_half_adder ha22 (p2[0],s12,s22,c22);
    mayur_full_adder fa23 (p2[1],c22,s13,s32,c32);
    mayur_full_adder fa24 (p2[2],c13,c32,s34,s35);

endmodule

## Makerchip_Block
<img width="1440" alt="Screenshot 2022-03-07 at 8 43 52 PM" src="https://user-images.githubusercontent.com/59500283/157091147-2f2f0d22-ba91-4351-9cd7-7bc3939535d7.png">

## Carry_Block

<img width="1422" alt="Carry_Block" src="https://user-images.githubusercontent.com/59500283/155388804-95e2aeb2-7f21-456e-bb09-ebc4cecf9253.png">
<img width="590" alt="Carry_Symbol" src="https://user-images.githubusercontent.com/59500283/155388883-20e31e93-54c2-497b-9062-3c1d7e855d4c.png">

## Sum_Block
<img width="1440" alt="Sum_Block" src="https://user-images.githubusercontent.com/59500283/155390426-86a5188d-2bf9-46fc-9396-1412fb35c22c.png">
<img width="1432" alt="Sum_Symbol" src="https://user-images.githubusercontent.com/59500283/155390464-23c4c947-44cc-4dde-8aed-828c80fe98f7.png">


## Parameters set for Voltage Source for Input A
<img width="357" alt="A_Pulse" src="https://user-images.githubusercontent.com/59500283/155388964-19e9a68d-e11c-4b39-8a08-1bdd65005658.png">

## Parameters set for Voltage Source for Input B
<img width="357" alt="B_Pulse" src="https://user-images.githubusercontent.com/59500283/155388995-879f0e25-8a64-4e78-bd85-e79c15a113f4.png">

## Parameters set for Voltage Source for Input C
<img width="354" alt="C_Pulse" src="https://user-images.githubusercontent.com/59500283/155389151-0461b3bf-d8d1-4464-a471-0056d355ffc4.png">


## Transient Settings
<img width="675" alt="Transient_Analysis" src="https://user-images.githubusercontent.com/59500283/155389323-55075cf7-a4e3-4ee8-8d6a-52facd7a74bf.png">

## Schematic of Full_Adder using the above Blocks
<img width="1431" alt="Final_Design" src="https://user-images.githubusercontent.com/59500283/155389447-252a7eb4-16f6-4aee-96d4-76d396d15b0e.png">

## Output Waveform
<img width="1435" alt="Output" src="https://user-images.githubusercontent.com/59500283/155389768-d8bf8e3f-72a5-426d-b366-3b6548f98d23.png">


## Netlist
```


*  Generated for: PrimeSim
*  Design library name: full_adder
*  Design cell name: Carry_tb
*  Design view name: schematic
.lib 'saed32nm.lib' TT

*Custom Compiler Version S-2021.09
*Wed Feb 23 18:37:06 2022

.global gnd!
********************************************************************************
* Library          : full_adder
* Cell             : Carry_Block
* View             : schematic
* View Search List : hspice hspiceD schematic spice veriloga
* View Stop List   : hspice hspiceD
********************************************************************************
.subckt carry_block a b c gnd_1 vdd carry
xm11 carry carry_bar gnd_1 gnd_1 n105 w=0.1u l=0.03u nf=1 m=1
xm8 net30 a gnd_1 gnd_1 n105 w=0.1u l=0.03u nf=1 m=1
xm7 carry_bar b net30 gnd_1 n105 w=0.1u l=0.03u nf=1 m=1
xm2 net9 b gnd_1 gnd_1 n105 w=0.1u l=0.03u nf=1 m=1
xm1 net9 a gnd_1 gnd_1 n105 w=0.1u l=0.03u nf=1 m=1
xm31 carry_bar c net9 gnd_1 n105 w=0.1u l=0.03u nf=1 m=1
xm12 carry carry_bar vdd vdd p105 w=0.1u l=0.03u nf=1 m=1
xm10 net34 a vdd vdd p105 w=0.1u l=0.03u nf=1 m=1
xm9 carry_bar b net34 vdd p105 w=0.1u l=0.03u nf=1 m=1
xm5 net23 b vdd vdd p105 w=0.1u l=0.03u nf=1 m=1
xm4 net23 a vdd vdd p105 w=0.1u l=0.03u nf=1 m=1
xm3 carry_bar c net23 vdd p105 w=0.1u l=0.03u nf=1 m=1
.ends carry_block

********************************************************************************
* Library          : full_adder
* Cell             : Inverter
* View             : schematic
* View Search List : hspice hspiceD schematic spice veriloga
* View Stop List   : hspice hspiceD
********************************************************************************
.subckt inverter gnd_1 input not vdd
xm0 not input vdd vdd p105 w=0.1u l=0.03u nf=1 m=1
xm1 not input gnd_1 gnd_1 n105 w=0.1u l=0.03u nf=1 m=1
.ends inverter

********************************************************************************
* Library          : full_adder
* Cell             : Sum_Block
* View             : schematic
* View Search List : hspice hspiceD schematic spice veriloga
* View Stop List   : hspice hspiceD
********************************************************************************
.subckt sum_block a b c carry_bar gnd_1 sum_bar vdd
xm29 sum_bar c net174 vdd p105 w=0.1u l=0.03u nf=1 m=1
xm30 net174 b net178 vdd p105 w=0.1u l=0.03u nf=1 m=1
xm31 net178 a net111 vdd p105 w=0.1u l=0.03u nf=1 m=1
xm18 net111 c vdd vdd p105 w=0.1u l=0.03u nf=1 m=1
xm19 sum_bar carry_bar net111 vdd p105 w=0.1u l=0.03u nf=1 m=1
xm20 net111 a vdd vdd p105 w=0.1u l=0.03u nf=1 m=1
xm21 net111 b vdd vdd p105 w=0.1u l=0.03u nf=1 m=1
xm26 sum_bar c net160 gnd_1 n105 w=0.1u l=0.03u nf=1 m=1
xm28 net164 b gnd_1 gnd_1 n105 w=0.1u l=0.03u nf=1 m=1
xm27 net160 a net164 gnd_1 n105 w=0.1u l=0.03u nf=1 m=1
xm23 net150 a gnd_1 gnd_1 n105 w=0.1u l=0.03u nf=1 m=1
xm22 sum_bar carry_bar net150 gnd_1 n105 w=0.1u l=0.03u nf=1 m=1
xm24 net150 b gnd_1 gnd_1 n105 w=0.1u l=0.03u nf=1 m=1
xm25 net150 c gnd_1 gnd_1 n105 w=0.1u l=0.03u nf=1 m=1
.ends sum_block

********************************************************************************
* Library          : full_adder
* Cell             : Carry_tb
* View             : schematic
* View Search List : hspice hspiceD schematic spice veriloga
* View Stop List   : hspice hspiceD
********************************************************************************
xi24 a b c gnd! net55 carry carry_block
v1 net55 gnd! dc='1.8V'
v22 c gnd! dc=0 pulse ( 0 1.8 0 0.1u 0.1u 20u 40u )
v21 b gnd! dc=0 pulse ( 0 1.8 0 0.1u 0.1u 10u 20u )
v20 a gnd! dc=0 pulse ( 0 1.8 0 0.1u 0.1u 5u 10u )
c2 carry gnd! c=1p
c19 sum gnd! c=1p
xi23 gnd! net50 sum net55 inverter
xi9 gnd! carry net33 net55 inverter
xi15 a b c net33 gnd! net50 net55 sum_block

.tran '1u' '40u' name=tran

.option primesim_remove_probe_prefix = 0
.probe v(*) i(*) level=1
.probe tran v(a) v(b) v(c) v(carry) v(sum)

.temp 25



.option primesim_output=wdf


.option parhier = LOCAL



.end

```

## Conclusion
Thus, the Multiplication for a Two 3_Bit Numbers is achieved using Wallace Multiplier.

## Author
Mayur Dongre , Indian Institute of Information Technoology Nagpur.
## Acknowledgement
1. Kunal Ghosh, Co-founder, VSD Corp. Pvt. Ltd. - kunalpghosh@gmail.com
2. Sameer Durgoji, NIT Karnataka
3.https://esim.marathoniitb.in/
## References
1) https://en.wikipedia.org/wiki/Wallace_tree

2)Youtube Video -https://www.youtube.com/watch?v=lcPIMvI57dM&t=114s

3)PriyankaMishraandSeemaNayak.AStudyon Wallace Tree Multiplier. https://www.researchgate.net/publication/32720922 

  
