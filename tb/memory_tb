`include "memory.v"
module tb;

	parameter DEPTH=16;
	parameter WIDTH=8;
	parameter ADDR_WIDTH=$clog2(DEPTH);

	reg clk,res,wr_rd,valid;
	reg [ADDR_WIDTH-1:0] addr;
	reg [WIDTH-1:0] wdata;
	wire ready;
	wire [WIDTH-1:0] rdata;
	reg [8*30-1:0] test_name;
	integer i;

	memory #(.DEPTH(DEPTH),.WIDTH(WIDTH),.ADDR_WIDTH(ADDR_WIDTH)) dut(.clk(clk),.res(res),.addr(addr),.wr_rd(wr_rd),.wdata(wdata),.rdata(rdata),.valid(valid),.ready(ready));
	
	//reset operation
	task res_mem();
		begin
			res=1;
			wr_rd=0;
			wdata=0;
			addr=0;
			valid=0;
			repeat(2) @(posedge clk);
			res=0;
		end
	endtask

	//write operation
	task mem_write(input integer start_point, input integer end_point);
		begin
			for(i=start_point;i<end_point;i=i+1) begin
				@(posedge clk);
				valid=1;
				wr_rd=1;
				wdata=$urandom_range(0,255);
				addr=i;
				$display("WRITE :: ADDR=%0d DATA=%0d TIME=%0t",addr,wdata,$time);
			end
			@(posedge clk);
			valid=0;
			wr_rd=0;
			wdata=0;
			addr=0;
			$display("------------------------------------");
		end
	endtask

	//read operation
	task read_mem(input integer start_point,input integer end_point);
		begin
			for(i=start_point;i<end_point;i=i+1) begin
				@(posedge clk);
				valid=1;
				wr_rd=0;
				addr=i;
				@(posedge clk);
				#1;
				$display("READ  :: ADDR=%0d DATA=%0d TIME=%0t",addr,rdata,$time);
			end
			@(posedge clk);
			valid=0;
			wr_rd=0;
			addr=0;
		end
	endtask

	//clock generation
	initial begin
		clk=0;
		forever #5 clk=~clk;
	end

	initial begin
		res_mem();
		if(!$value$plusargs("test_case=%0s",test_name));
		case(test_name)
			"test_1wr_1rd": begin
				mem_write(0,1);
				read_mem(0,1);
			end
			"test_5wr_5rd":begin
				mem_write(0,5);
				read_mem(0,5);
			end
			"test_full":begin
				mem_write(0,DEPTH);
				read_mem(0,DEPTH);
			end
			"test_half":begin
				mem_write(0,DEPTH/2);
				read_mem(0,DEPTH/2);
			end
			default: $display("INVALID TEST CASE");
		endcase
		#100;
		$finish;
	end
endmodule
