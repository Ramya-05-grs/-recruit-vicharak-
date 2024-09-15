# -recruit-vicharak-
module CPU (
input wire[18:0]r2,
input wire[18:0]r3,
input wire[4:0]op,
input wire[18:0]addr,
input rst,
input clk,
output reg [18:0]pc);

reg [18:0]r1;
reg [18:0]stack [0:15];
reg [3:0]sp;
reg [18:0]memory [0:255];
reg [18:0]result;
reg [18:0]fft_start_addr;
function [18:0]encrypt(input[18:0]data);
        encrypt=data^19'h1FF;
endfunction
function [18:0]decrypt(input[18:0]data);
        decrypt=data^19'h1FF;
endfunction
always@(posedge clk or posedge rst)
begin
if(rst)
begin
    r1<=0;
    pc<=0;
    sp=4'b1111;
    result=0;
    end
else
begin
case(op)
5'b00000: r1=0;
5'b00001: r1=r2+r3;
5'b00010: r1=r2-r3;
5'b00011: r1=r2*r3;
5'b00100: r1=r2/r3;
5'b00101: r1=r1+1;
5'b00110: r1=r1-1;
5'b00111: r1=r2&r3;
5'b01000: r1=r2|r3;
5'b01001: r1=r2^r3;
5'b01010: r1=~r2;
5'b01011: pc=addr;
5'b01100: begin
            if(r1==r2)
            begin
            pc=addr;
            end
            else if(r1!=r2)
            begin
            pc=addr;
            end
          end  
5'b01101: begin
            stack[sp]=pc+1;
            sp=sp-1;
            pc=addr;
            end
5'b01110: begin
            sp=sp+1;
            pc=stack[sp];
            end
5'b01111: r1=memory[addr];
5'b10000: memory[addr]=r1;
5'b10001: begin
            fft_start_addr=r2;
            result=memory[r2]*2;
            memory[r1]=result;
            end
5'b10010: begin
            
            memory[r1]=encrypt(memory[r2]);
            end
5'b10011: begin
            memory[r1]=decrypt(memory[r2]);
            end
default: r1=0;
endcase
end
end
endmodule
