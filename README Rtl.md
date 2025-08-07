```verilog
module half_adder (input wire a ,b,
                   output wire s ,c);
  assign s = a ^ b;
  assign c = a & b;
endmodule

module full_adder (input wire a,b,cin,
                   output wire s,c);
  wire [2:0] w;
  
  half_adder ha0 (.a(a),.b(b),.s(w[0]),.c(w[1]));
  half_adder ha1 (.a(w[0]),.b(cin),.s(s),.c(w[2]));
  assign c = w[1] | w[2] ;
endmodule

module wallace_tree_multiplier (input wire [3:0] a , b,
                                output wire [7:0] z);
  wire p[0:3][0:3];
  wire [20:0]s, c;
  genvar i,j;
  generate 
    for (i=0; i<4; i=i+1) begin
      for (j=0; j<4; j=j+1) begin
        assign p[i][j] = a[i] & b[j];
      end
    end
  endgenerate
  
  assign z[0] = p[0][0];
  
  //Bit 1
  half_adder ha1(p[0][1], p[1][0] ,z[1], c[0]);
  
  //Bit 2
  full_adder fa1(p[2][0], p[1][1] ,p[0][2] ,s[0] ,c[1]);
  half_adder ha2(s[0] ,c[0] ,z[2] ,c[2]);
  
  //Bit 3
  full_adder fa2(p[3][0] ,p[2][1] ,p[1][2] ,s[1] ,c[3]);
  half_adder ha3(p[0][3] ,c[1] ,s[2] ,c[4]);
  full_adder fa3(s[1] ,s[2] ,c[2] ,z[3] ,c[5]);
  
  //Bit 4
  full_adder fa4(p[1][3] ,p[2][2] ,p[3][1] ,s[3] ,c[6]);
  full_adder fa5(s[3] ,c[3] ,c[4] ,s[4] ,c[7]);
  half_adder ha4(s[4] ,c[5] ,z[4] ,c[8]);
  
  //Bit 5
  half_adder ha5(p[2][3] ,p[3][2] ,s[5] ,c[9]);
  full_adder fa6(s[5] ,c[6] ,c[7] ,s[6] ,c[10]);
  half_adder ha6(s[6] ,c[8] ,z[5] ,c[11]);
  
  //Bit 6
  full_adder fa7(p[3][3] ,c[9] ,c[10] ,s[7] ,c[12]);
  half_adder ha7(s[7] ,c[11] ,z[6] ,c[13]);
  
  //Bit 7
  half_adder ha8(c[12] ,c[13] ,z[7] ,c[14]);
  
  endmodule
