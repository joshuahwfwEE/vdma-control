# vdma-control
this file is about controling Xilinx IP VDMA to access block ram or external DDR4 
the procedure is 
1. setup VDMA's parameter 
2. 
394,170,367

   1-1 Address Width: 32 bit
   
   1-2 Frame Buffers：3  
   a set of W/R in an action at the same time  
   FRAME0/FRAME2  
   FRAME1/FRAME0  
   FRAME2/FRAME1  
     
   1-3 S2MM:
       Memory Map Data Width: 512 bytes
       Write/Read Burst Size: 
       Stream Data Width：32
       Line Buffer Depth:
       
       
3. make sure stream is coming through AXIS MM2S
4. test memory can R/W 
5. configure VDMA IP 
6. enable acessing to AXI S2MM to wr to memory
7. wait for VDMA S2MM status is no longer halt
8. enable acessing to AXI MM2S to wr to memorY
9. wait for VDMA MM2S status is no longer halt
10. check video stream in AXIS SS2M  
 
datapath:
1. RX frame CRC => AXI4_Stream Subset Converter => S_AXIS S2MM:  
the data stream is active while tready and tvalid is asserted by VDMA
tuser is the first stream data transmitted  
tlast is the last stream data transmitted  
tdata contains the color scalar according the color format and BPC ( ex: RGB,BPC=10=>10 bytes*4*3= 120 bytes   
                                                                         RGB,BPC=8=>  8 bytes*4*3=  96 bytes  
                                                                         YCbCr422...)  
a video stream with 1920x1080 resolution which has h size:1920, v size:1080 per frame  
a periods of tuser to tlast's data stream is a frame  
a blank during tlast to tuser calls Vblank  
after the tlast is asserted, the S2MM start to active  
  
2. M_AXI_S2MM <=> DDR4_S_AXI  
   awvalid is asserted, it acquire:  
   awaddr= 817830000  
   awlength = 2f  
   awsize = 64 bytes  
   awcache = 3  
   awport = data secure unprivilegal  
   aw_cnt is asserted at the awvaild falling edge
   when aw_cnt is asserted, w_vaild is asserted to start  
   the data stream is in the period of both w_valid w_ready are asserted  
   w_last is the last stream data transmitted of a frame, which has 1 cycle clock period  
     
3. M_AXI_MM2S <=> DDR4_S_AXI_CRTL  
   arvalid is asserted, it acquire:  
   araddr= 810d00000  
   arlength = 07  
   arsize = 64 bytes  
   arcache = 3  
   arport = data secure unprivilegal  
   ar_cnt is asserted at the awvaild falling edge
   when ar_cnt is asserted, r_vaild is asserted to start  
   the data stream is in the period of both r_valid r_ready are asserted  
   the period of a cycle clock when r_last is asserted, which means the last stream data transmitted of a frame  
     
4. M_AXIS_MM2S => AXI4_Stream Subset Converter => TX frame CRC:  
   the data stream is active while tready and tvalid is asserted by VDMA
   notice that the frequence of data stream needs to be same to the data stream in S_AXIS S2MM
   
