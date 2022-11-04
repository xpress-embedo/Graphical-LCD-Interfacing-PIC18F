# Graphical LCD Interfacing with PIC18F4550 Microcontroller
The use of a graphical LCD (GLCD) drastically changes the look of your project. It provides more freedom for presenting data than the HD44870 based character LCDs. Today we will see how to interface a KS0108 (name of the display controller chip) based GLCD to a PIC microcontroller. In this tutorial we will focus on exploring the built-in GLCD Library of mikroC Pro for PIC compiler to display more complex texts and objects. Since GLCDs are real resource hungry devices (in terms of required I/O pins and memory), a bigger size PIC microcontroller (PIC18F4550) is selected for this experiment.  

## Blog Posts
[Graphical LCD Interfacing with PIC18F Part-1](http://embeddedlaboratory.blogspot.com/2016/06/graphical-lcd-128x64-part-1.html)  
[Graphical LCD Interfacing with PIC18F Part-2](http://embeddedlaboratory.blogspot.com/2016/07/graphical-lcd-128x64-part-2.html)  

## Theory
The graphical LCD used in this experiment is based on KS0108B controller, which is a 128×64 pixel monochromatic display. The KS0108B is a dot matrix LCD segment driver with 64 channel output. On the other hand, the KS0107B is a 64-channel common driver which generates the timing signal to control the two KS0108B segment drivers. The KS0108B and KS0107B are a very popular controllers and have made their way into many graphical LCDs. The internal block diagram of the GLCD module is shown below.

![GLCD Block Diagram](https://1.bp.blogspot.com/-XxUATuppNac/V3P45Y-kDDI/AAAAAAAAAT4/4-6vrzUj9o0BQOZEBJWlBZyIzZNmxMw3wCLcB/s1600/GLCD_InternalBlock.jpg)

The GLCD has total of 8 pages, starting from Page-0 to Page 7, as shown below:

![](https://2.bp.blogspot.com/-lPGwDX69RCA/V3P5IsQsPhI/AAAAAAAAAUA/L6petPmx_SwKpu8pOeAPM2ITYFPJeDFLgCLcB/s1600/GLCD_Pages.jpg)

Please refer your GLCD Datasheet for the connection Diagram, or follow our circuit diagram.

The KS0107/KS0108 does not have a character generator so this must be implemented in the micro-controller firmware. The LCD controller supports a handful of instructions which are summarized in the table shown below. Note that the RS (D/I) pin is high only during data read and data write operations, and stays low when a transmitted byte is an instruction.

![GLCD Commands](https://3.bp.blogspot.com/-aiC9MywmteA/V3P5eSZS8QI/AAAAAAAAAUI/YdD7sKDg7VElIWbYRD_quId0A9aZxIjYQCLcB/s1600/DisplayControlInstructions.jpg)

The schematic diagram which this tutorial uses is as follow:  
![Schematic Diagram](https://2.bp.blogspot.com/-0xQ77AIEWjM/V4Ek3dKVt0I/AAAAAAAAAV8/qby8wmpghU0XHwX1N5ObCjeoLUi55gElwCLcB/s1600/schematic%2Bdiagram.png)

### Simulation Output
![First Screen](https://3.bp.blogspot.com/-U13GMsScAFo/V3P6IbLlfCI/AAAAAAAAAUY/7EHhfEdzLQElxnlvG0Lh87fHt4qPtZXdwCLcB/s1600/GraphicalLcdBank.png)

![Second Screen](https://2.bp.blogspot.com/-QyINKjQ1lyo/V3P6IRRWy2I/AAAAAAAAAUc/vDHe_sx2-vsJUjuI5qlt4s9WxIKUlrXqACLcB/s1600/GraphicalLcdBankInverted.png)

![Alt text](https://2.bp.blogspot.com/-EZ9A9ilk_c0/V4EmaVAPO9I/AAAAAAAAAWM/GumEpTDObzcHlZBcGHUDmK4o93amxBThQCLcB/s1600/Image1.PNG)

![Alt text](https://3.bp.blogspot.com/-3LFYbt7mJdk/V4EmafC2eMI/AAAAAAAAAWI/oM2_yyWdz0gVDbMt4PHw32-lqOmkyg7mgCLcB/s1600/Image2.PNG)

![Alt text](https://1.bp.blogspot.com/-XFiIMIlRT5U/V4Ema6Lf-JI/AAAAAAAAAWU/QnZVQh3ij-I0Fnd9PPYDNTcdsbuOvXcnACLcB/s1600/Image3.PNG)

![Alt text](https://4.bp.blogspot.com/-uHGkm-t6JhA/V4EmaxKB00I/AAAAAAAAAWY/ZMiAXB7am-EB_bzs8wSNPKKpdXbKmF16wCLcB/s1600/Image4.PNG)

![Alt text](https://2.bp.blogspot.com/-XosPf5ewDPM/V4Ema2oYEMI/AAAAAAAAAWc/isYfbd_eJwsfPI9HskbTi7kEfiKk1C9fQCLcB/s1600/Image5.PNG)

![Alt text](https://1.bp.blogspot.com/-WQ_bDo204nk/V4EmbX2sU5I/AAAAAAAAAWk/BBsaKKcK9u858b_Ntv3_WfpQOHvNuRoTwCLcB/s1600/Image7.PNG)

![Alt text](https://3.bp.blogspot.com/-2teabc0c9z8/V4EmbTt1k5I/AAAAAAAAAWg/jPNnJWAiN30E024VFeay8wAXMokmw4UDACLcB/s1600/Image6.PNG)

![Alt text](https://4.bp.blogspot.com/-LZxHbA7y-7w/V4EmbhZDW8I/AAAAAAAAAWo/hod3Yh9lP7A50QtE9kGkuuo8iVf9DO0sQCLcB/s1600/Image8.PNG)

![Alt text](https://3.bp.blogspot.com/-H4ptImkUQy8/V4EmaUD76_I/AAAAAAAAAWQ/tRIwxx-0eBYWYceo3sZMuqYg2hmhg5z9QCLcB/s1600/Image10.PNG)

The libraries functions are inspired from mikroC functions and i tried to keep most of the functions similar to the one available in mikroC compiler.  
In this version most of the basic functions are written, which are as follow:  
```C
// Basic Function  
 void Glcd_Init( void );  
 void Glcd_Set_Side( u8_t x_pos);  
 void Glcd_Set_X( u8_t x_pos );  
 void Glcd_Set_Page( u8_t page );  
 void Glcd_Write_Data( u8_t data );  
 u8_t Glcd_Read_Data( void );  
 // Advanced Function Prototypes  
 void Glcd_Fill( u8_t pattern );  
 void Glcd_Dot( u8_t x_pos, u8_t y_pos, u8_t color );  
 void Glcd_V_Line ( u8_t y_start, u8_t y_end, u8_t x_pos, u8_t color);  
 void Glcd_H_Line( u8_t x_start, u8_t x_end, u8_t y_pos, u8_t color);  
 void Glcd_Image( const u8_t *image);  
 void Glcd_Circle( s8_t x_center, s8_t y_center, u8_t radius, u8_t color);  
 void Glcd_Line( s16_t x_start, s16_t y_start,  
         s16_t x_end, s16_t y_end, u8_t color);  
 void Glcd_Rectangle( u8_t x_upper_left, u8_t y_upper_left,   
     u8_t x_bottom_right, u8_t y_bottom_right,   
     u8_t color);  
 void Glcd_Write_Char(u8_t chr, u8_t x_pos, u8_t page_num, u8_t color);  
 void Glcd_Write_Text(char *text, u8_t x_pos, u8_t page_num, u8_t color);  
 ```
 