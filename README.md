# Lab Week 05 Wed/Thurs
This will be the last lab of the quarter before the final project. You will get to work on it for both sections of week 6. You will be sending a 16x16 character sprite from the raspberry pi pico to your FPGA(over SPI) and display it through out the entire screen.

## Required Compoenents
1. 1x FPGA (either ICESugar or ICESugar-pro)
2. 1x RGB Screen
3. 1x Ribbon-cable to PMOD Adapte
4. Raspberry Pi Pico

## Exercise 1
In this exercise, you will not be using the pico, yet. You will alter your LCD module from the previous lab so that it reads a 16 bit value from a 16x16 sprite saved in memory and then output it to the screen. A simple memory module is given for this lab. For this exercise you should use `sprite_buf_Ex1.sv`. This is a read only memory module(named `dp_buffer`) that has the 16 bit RGB values for a 16x16 sprite saved(The current values in the file are for a 16x16 sprite of Super Mario. You can replace this with any 16x16 sprite you want). The sprite is saved as 1 dimensional array of size 256.

You will need to add the following 2 signals to your LCD module:
``` verilog
input [15:0] pixel,
output [7:0] pixel_address, 
```    
`pixel` is the 16 bit RGB value for a specific pixel and `pixel_address` is the address of the pixel you want to read. `pixel_address` will be output for the LCD module and will be connected to the read address of the memory module. `pixel` should then be connected to the read data from the memory module. By using these 2 signals, you will be able to display the sprite on the screen. Since the Sprite is 16x16 while the screen is 480x272, we want you to display the sprite multiple times so that it covers the entire screen. It should look something like the following:

![](pics/Screenshot%202026-05-03%20144435.png)

## Exercise 2
In this exercise, you will add the pico. You will move the 16 bit 16x16 sprite values to the pico. The pico will send each value over to the FPGA using SPI and then the FPGA will save those values in memory. For the memory module of this lab, you should use `sprite_buf_Ex2.sv`. You will notice that the sprite is no longer hard coded to the buffer and there are some new inputs. The `waddr` input is what address you want to save a value to and the `wdata` input is the data you want to save. There is also the `we` signal which only allows you to write data when this value is a 1. Feel free to alter this file if you want. 

You will need to implement an SPI module on the FPGA that recieves a 16 bit RGBA value and saves it to the memory module. Every 16 bit RGB value should be saved to a different address until all 256 address are filled. Once that happens, you should go back to the first address.

On the pico side, you will need to send the RGB values(using spi) in the correct order for the FPGA to store them. Since the sprite isn't changing, you only need to send each value once but if you implement the SPI module correctly on the FPGA, it should be able to handle it if you send the 16x16 sprite values continously.

## Exercise 3
For this exercise, you will make the sprite rotate 90 degrees every second. If you have implemented the SPI and LCD modules correctly up to this point, you should be able to acomplish this rotating effect by only altering your pico code. You can do this by sending the same 16 bit RGB values from the pico to the FPGA but in a different order. For example, if you previously sent the RGB values of the sprite over row by row, if you then send then column by column, you will get a different orientaiton of the sprite.

It should look something like [this](https://youtu.be/tnjq5w1s2bk).

### Tips/Warnings
Here are some things to keep in mind when working on exercise 3.
1. If you see something being displayed to your screen but every second it looks like it is being shifted over a little, this is usually due to not saving all 256 values despite them all being sent. This shift happens because the memory expects 256 RGB values but maybe only 250 are being recieved. When this happens, the next time the sprite is sent, the memory will start saving values at address 251 instead of 0.
2. If the sprite shape is correct on the screen but sometimes the color changes, this usually means that all 256 values are being recieved but the actuall RGB values might be off by a bit or 2. This also might happen if you move the spi wires while data is being sent.

## Using the LCD screen for the Final Project
In this lab we send 16x16 sprites over to the FPGA and then display them on the screen. For you project, you might want to use the pico to generate the entire frame you want displayed and then send it over. In doing that, you will send 480x272 16 bits of data. Depending on the FPGA you are using and what else you are doing on the FPGA, you might not have enough memory for that(480x272x16 bits). If that is the case for you, you will want to think of ways to save memory, such as by not sending entire 16 bit RGB values. You probably won't be using the entire range of colors so do you really need 16 bits of data for each pixel?