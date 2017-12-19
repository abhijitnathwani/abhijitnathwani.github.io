---
layout: post
title: Introduction to Image Processing using C 
---

So most of you would be aware of various Image Processing techniques, on different platforms such as MATLAB, SciLAB, etc. Here, in this series of blog posts, we are only going to focus on image processing using C language. At times, the platforms named above are not available to us, and there may be a need to process images in most basic language, such as C. 

##### Why image processing in C ?

Well, recently I was assigned a task to optimize an image processing code in C, and I had no clue how to go about and what are the image processing operations to be performed on C, *without the use of any external libraries*. After a lot of digging on the internet, reading books and articles, I finally managed to do the task. Also, I realized that the resources are all available on the internet, however, *they lie scattered*. Hence, these series of blog posts have been created so that one must get most of the required information at a go, and can start with  ***Image Processing using C*** .

##### Image formats

There are various widely available image formats such as JPEG, PNG, TIFF, BMP, etc. For ease, we would be discussing all the programs and operations around the [Bitmap file format](https://en.wikipedia.org/wiki/BMP_file_format). As the BMP is less complex to understand and decode than the compressed formats such as JPEG, we would be preferring this. As we do not intend to use any library as much as possible, we will have to deal with all the image headers and the smallest byte of information through our own code, BMP comes in handy to use.

##### The Bitmap Image 

This blog post only talks about the bare minimum information that you need in order to get started with Image Processing programs using C. So, the information provided would only as much as required to get to familiar with the environment. If you need more information, feel free to browse the internet or leave a comment below. We will discuss as required.

Basically, any image would be structured in the following way: 

 **image header -->  color table (if any) --> image data**

Similarly, the BMP image is structured in the same way. It has a **54 byte** *image header*, **1024 byte** *colorTable* if present, and the rest is the image data.

#### The Image Header

offset | size | description
------ |:----:|------------|
0 | 2 | signature, must be 4D42 hex
2 | 4 | size of BMP file in bytes (unreliable)
6 | 2 | reserved, must be zero
8 | 2 | reserved, must be zero
10 | 4 | offset to start of image data in bytes
14 | 4 | size of BITMAPINFOHEADER structure, must be 40
18 | 4 | image width in pixels
22 | 4 | image height in pixels
26 | 2 | number of planes in the image, must be 1
28 | 2 | number of bits per pixel (1, 4, ,8 or 24) (bitDepth)
30 | 4 | compression type (0=none, 1=RLE-8, 2=RLE-4)
34 | 4 | size of image data in bytes (including padding)
38 | 4 | horizontal resolution in pixels per meter (unreliable)
42 | 4 | vertical resolution in pixels per meter (unreliable)
46 | 4 | number of colors in image, or zero
50 | 4 | number of important colors, or zero

The Bitmap Image Header ([source](http://www.fastgraph.com/help/bmp_header_format.html))

From the above header, the information really important to us are *width, height and bitDepth*. 

Next, if the **bitDepth** is <= 8, the BMP image would contain the **colorTable**. ColorTable is, as per [Wikipedia:](https://en.wikipedia.org/wiki/BMP_file_format#Color_table)

> The color table is a block of bytes (a table) listing the colors used by the image. Each pixel in an indexed color image is described by a number of bits (1, 4, or 8) which is an index of a single color described by this table. The purpose of the color palette in indexed color bitmaps is to inform the application about the actual color that each of these index values corresponds to. The purpose of the color table in non-indexed (non-palettized) bitmaps is to list the colors used by the bitmap for the purposes of optimization on devices with limited color display capability and to facilitate future conversion to different pixel formats and paletization.

In layman terms, the colorTable helps us indentify the shade of the color based on the value. It serves as a lookup table for colors for that particular image.

The rest of the bytes, once we've read **54 bytes of image header** and **1024 bytes of colorTable** is all image data, i.e. the **pixel information.**

This is all you need to know for now, to start processing your BMP image in C. In the [next post]({{ site.baseurl }}/blog/2017/12/20/First-C-Program-for-Image-Processing), we will talk about writing your first C program that reads and makes a copy of the image. 
