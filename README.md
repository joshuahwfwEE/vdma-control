# vdma-control
this file is about controling Xilinx IP VDMA to access block ram or external DDR4 
the procedure is 
1. make sure stream is coming through AXIS MM2S
2. test memory can R/W 
3. configure VDMA IP 
4. enable acessing to AXI S2MM to wr to memory
5. wait for VDMA S2MM status is no longer halt
6. enable acessing to AXI MM2S to wr to memorY
7. wait for VDMA MM2S status is no longer halt
8. check video stream in AXIS SS2M
