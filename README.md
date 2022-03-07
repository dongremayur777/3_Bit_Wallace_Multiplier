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
  * [Schematic_Model](#schematic_model)
  * [Netlist](#netlist)
  * [Waveforms](#waveforms)
  * [Using_Gaw](#using_gaw)
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

## Verilog_Code
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


Using NGVeri and the above Verilog Code We Create the 3_Bit_Wallace Model.

## Schematic_Model

<img width="1346" alt="Screenshot 2022-03-07 at 8 04 00 PM" src="https://user-images.githubusercontent.com/59500283/157094037-2ddcece6-f239-4c77-9935-6e3b7d5460d0.png">

After Drawing the Schematic we generate a netlist and then we use a KICAD to NGSPICE Converter

## Netlist

* /home/bt19ece016/esim-workspace/wallace/wallace.cir

* u2  net-_u2-pad1_ net-_u2-pad2_ net-_u2-pad3_ net-_u2-pad4_ net-_u2-pad5_ net-_u2-pad6_ net-_u2-pad7_ net-_u2-pad8_ net-_u2-pad9_ net-_u2-pad10_ net-_u2-pad11_ net-_u2-pad12_ mayur_wallace
* u8  a2 a1 a0 b2 b1 b0 net-_u2-pad1_ net-_u2-pad2_ net-_u2-pad3_ net-_u2-pad4_ net-_u2-pad5_ net-_u2-pad6_ adc_bridge_6
v1  a2 gnd pulse(0 5 0 0.1m 0.1m 5 10)
v2  a1 gnd pulse(0 5 0 0.1m 0.1m 10 20)
v3  a0 gnd pulse(0 5 0 0.1m 0.1m 20 40)
v4  b2 gnd pulse(0 5 0 0.1m 0.1m 5 10)
v5  b1 gnd pulse(0 5 0 0.1m 0.1m 10 20)
v6  b0 gnd pulse(0 5 0 0.1m 0.1m 20 40)
* u1  a2 plot_v1
* u3  a1 plot_v1
* u4  a0 plot_v1
* u5  b2 plot_v1
* u6  b1 plot_v1
* u7  b0 plot_v1
c2  prod1 gnd 1u
c4  prod3 gnd 1u
c6  prod5 gnd 1u
r2  net-_r2-pad1_ prod1 1k
r4  net-_r4-pad1_ prod3 1k
r6  net-_r6-pad1_ prod5 1k
* u15  prod5 plot_v1
* u13  prod3 plot_v1
* u11  prod1 plot_v1
* u9  net-_u2-pad7_ net-_u2-pad8_ net-_u2-pad9_ net-_u2-pad10_ net-_u2-pad11_ net-_u2-pad12_ net-_r6-pad1_ net-_r5-pad1_ net-_r4-pad1_ net-_r3-pad1_ net-_r2-pad1_ net-_r1-pad1_ dac_bridge_6
c1  prod0 gnd 1u
r1  net-_r1-pad1_ prod0 1k
* u10  prod0 plot_v1
r5  net-_r5-pad1_ prod4 1k
c5  prod4 gnd 1u
* u14  prod4 plot_v1
c3  prod2 gnd 1u
r3  net-_r3-pad1_ prod2 1k
* u12  prod2 plot_v1
a1 [net-_u2-pad1_ net-_u2-pad2_ net-_u2-pad3_ ] [net-_u2-pad4_ net-_u2-pad5_ net-_u2-pad6_ ] [net-_u2-pad7_ net-_u2-pad8_ net-_u2-pad9_ net-_u2-pad10_ net-_u2-pad11_ net-_u2-pad12_ ] u2
a2 [a2 a1 a0 b2 b1 b0 ] [net-_u2-pad1_ net-_u2-pad2_ net-_u2-pad3_ net-_u2-pad4_ net-_u2-pad5_ net-_u2-pad6_ ] u8
a3 [net-_u2-pad7_ net-_u2-pad8_ net-_u2-pad9_ net-_u2-pad10_ net-_u2-pad11_ net-_u2-pad12_ ] [net-_r6-pad1_ net-_r5-pad1_ net-_r4-pad1_ net-_r3-pad1_ net-_r2-pad1_ net-_r1-pad1_ ] u9
* Schematic Name:                             mayur_wallace, NgSpice Name: mayur_wallace
.model u2 mayur_wallace(rise_delay=1.0e-9 fall_delay=1.0e-9 input_load=1.0e-12 instance_id=1 ) 
* Schematic Name:                             adc_bridge_6, NgSpice Name: adc_bridge
.model u8 adc_bridge(in_low=1.0 in_high=2.0 rise_delay=1.0e-9 fall_delay=1.0e-9 ) 
* Schematic Name:                             dac_bridge_6, NgSpice Name: dac_bridge
.model u9 dac_bridge(out_low=0.0 out_high=5.0 out_undef=0.5 input_load=1.0e-12 t_rise=1.0e-9 t_fall=1.0e-9 ) 
.tran 0.1e-00 40e-00 0e-00

* Control Statements 
.control
run
print allv > plot_data_v.txt
print alli > plot_data_i.txt
plot v(a2) v(a1)+6 v(a0)+12 v(b2)+18 v(b1)+2 v(b0)+30 v(prod5)+36 v(prod3)+42 v(prod1)+48 v(prod0)+54 v(prod4)+60 v(prod2)+66
.endc
.end


## Waveforms
<img width="1340" alt="Screenshot 2022-03-07 at 8 06 01 PM" src="https://user-images.githubusercontent.com/59500283/157094948-38474f65-1df7-495b-a86c-1bbf85e27c6e.png">

Using GAW We Plot a0,a1,a2,b0,b1,b2,prod0,prod1,prod2,prod3,prod4,prod5 in Different Planes.
## Using_GAW
<img width="1436" alt="Screenshot 2022-03-07 at 8 11 04 PM" src="https://user-images.githubusercontent.com/59500283/157095200-ae6d6039-8e81-41bd-8fa2-8e8540c6e32c.png">
<img width="1439" alt="Screenshot 2022-03-07 at 8 27 15 PM" src="https://user-images.githubusercontent.com/59500283/157095226-568362ab-c78c-484a-a63f-73b3c89b98c1.png">


## Conclusion
Thus ,The Multiplication for a Two 3_Bit Numbers is achieved using Wallace Multiplier.

## Author
Mayur Dongre , Indian Institute of Information Technoology Nagpur.
## Acknowledgement
1. Kunal Ghosh, Co-founder, VSD Corp. Pvt. Ltd. - kunalpghosh@gmail.com
2. Sameer Durgoji, NIT Karnataka
3.https://esim.marathoniitb.in/
## References
  1)Wikipedia-https://en.wikipedia.org/wiki/Wallace_tree

  2)Youtube Video -https://www.youtube.com/watch?v=lcPIMvI57dM&t=114s

  3)PriyankaMishraandSeemaNayak.AStudyon Wallace Tree Multiplier. https://www.researchgate.net/publication/32720922 

  
