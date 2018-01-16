---
layout: post
title: RGB Image Basics 
---

Now that we are well-versed in grayscale bitmaps, let us take some time and understand the basics of RGB Bitmaps, and how to deal with it.

The RGB Image differentiates from the grayscale, by the color component. The grayscale does not hold any color information. The major differentiator in this image is the `Depth` field in the image info header, _bit no. 28_. This helps us identify the color components are present in the image or not. 

For the grayscale images, this value would be less than or equal to 8, and for RGB, it will be greater than 8. There is one exception for 8 bit RGB as well, we will talk about it later.

In most cases, the RGB `depth` would be 24-bit. This means that each pixel is of 24-bit and sub-dividing it, each color component is of 8-bit. So we have 8-bit of Red, 8-bit of Green and 8-bit of Blue data. In each component, 0 means no contribution of that color, and 255 means full contribution of that color. Since each component has 256 different states there are a total of 16777216 possible colours.


![RGB Pixel Info](/images/colourdepthfig2.gif)

And to quote more about RGB Color spaces,
> This idea of RGB colour space is a fundamental concept in computer graphics. In RGB space any colour is represented as a point inside a colour cube with orthogonal axes r,g,b.

The above statement is more clear with following picture in mind.

![RGB Color Space](/images/colourdepthfig3.gif)

Note that grey values form a straight line from black to white along the diagonal of the cube, R = G = B.

##### 8-bit indexed color

There may even be 8-bit indexed RGB color image. It is more economical to store color bitmaps without using 3 bytes per pixel. As with 8-bit gray bitmaps, each pixel has one byte associated it with, however, this byte does not hold the color information but an index into a table of colors, called a palette or color table.

![Color Table](/images/colourdepthfig4.gif)

##### 4-bit indexed color

The concept for this is same as the 8-bit indexed color, except for the fact that it stores only 4-bits for the index. It because of this that only 16 colors are possible to create from the index.

##### 32-bit RGB

This is normally the same as 24 bit colour but with an extra 8 bit bitmap known as an alpha channel. This channel can be used to create masked areas or represent transparency.

![32-Bit RGB](/images/colourdepthfig6.gif)

##### 16-bit RGB

This is generally a direct system with 5 bits per colour component and a 1 bit alpha channel.

![16-Bit RGB](/images/colourdepthfig7.gif)


Also, another thing to keep in mind is the storage of pixels in BMP file format.When the pixel information starts, the data is arranged something like this,

`
B0 G0 R0 B1 G1 R1 B2 G2 R2 .... Bn Gn Rn
`

The color component is stored in __BGR format__ and not in RGB. Hence the pixel 0's Blue component comes first, followed by Green component and the Red component. Hence take special care while reading the image. 

Another interesting point to note is the pixel arrangement in BMP format. BMP stores pixel data ___upside down!___ What this means is the first pixel which you encounter while reading the file, represents the last pixel (bottom right pixel) of the image that you visually see. Hence, if you need certain operations for region based implementation, doubly check the pixels you are addressing!

For more information on Bitmap File Format, check the Wikipedia page [here](https://en.wikipedia.org/wiki/BMP_file_format).

Author: __Abhijit Nathwani__ 




