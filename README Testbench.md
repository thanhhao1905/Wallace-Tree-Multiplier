```verilog
`timescale 1ps/1ps
module tb_wallace_tree_multiplier;
  
  reg [3:0] a,b;
  wire [7:0] z;
  reg [7:0] exp_z;
  integer err=0 ,k,l;
  
  wallace_tree_multiplier DUT (a,b,z);
  
  initial begin
    $monitor ("time=%0t, a=%b ,b=%b z=%b ,exp_z=%b",$time,a,b,z,exp_z);
    
    for(k=0; k<16 ; k=k+1) begin
      for(l=0; l<16 ;l=l+1) begin
        a = k;
        b = l;
        exp_z = a*b;
        
        #1
      	check(z, exp_z);
      end
    end
    
    if(err==0)begin
      $display("--------");
      $display("Test Pass");
      $display("--------");
    end else begin
      $display("--------");
      $display("Test Fail with %d error",err);
      $display("--------");
    end
    
    $finish;
  end
  
  task check(input [7:0]z ,input [7:0]exp_z);
    begin
      if(z!==exp_z) begin
        $display("[CHECK] Error");
        err = err+1;
      end else begin
        $display("[CHECK] Matching");
      end
    end
  endtask
  
  initial begin
    $dumpfile("dump.vcd");
    $dumpvars;
  end
endmodule
