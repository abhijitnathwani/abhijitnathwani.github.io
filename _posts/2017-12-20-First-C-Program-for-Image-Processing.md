---
layout: post
title: First C Program for Image Processing 
---
 
So, now that you know the basics of Bitmap image, we can start writing our first program. This will also serve as your boiler plate program for the following tutorials. Once you're clear with this, things should start to make sense.

If you're are reading this post directly, I insist you to read the introductory [post]({{ site.baseurl }}/blog/2017/12/19/Introduction-to-Image-Processing-using-C) that contains the heads up required for this blog post.

The complete program that will be discussed here, has been updated in the [repository](https://github.com/abhijitnathwani/image-processing/) complementing this blog posts. The code for this post is [here](https://github.com/abhijitnathwani/image-processing/blob/master/image_copy.c).


So, let's get started! :sunglasses:

As the traditional image processing practice, we'd be performing operations on the most common images in the IP domain. For this post, we'd be using _lena512.bmp_ which is a gray scale image.

![Lena Grayscale](/images/lena512.bmp "Lena Grayscale")

The first thing to do, read the input image. While we know the different file formats and the their meanings, C understands the image as a *file* only. Everything is *file handling operations* for C. It is the responsibility of the programmer to handle the bytes of information and structure it as an image file.

```c
FILE *streamIn;
streamIn = fopen("/path/to/your/image.bmp","r"); // open the file
```
Now we need to read the imageHeader and colorTable. Hence we declare the variables for the same.

```c
unsigned char header[54]; // to store the image header
unsigned char colorTable[1024]; // to store the colorTable, if it exists.
```

Note that once we open the file using `fopen()`, the file pointer points to the start of the file. We know need to read **54 bytes of image header** from this position.

```c
for(i=0;i<54;i++) 
	header[i] = getc(streamIn);  // strip out BMP header, byte-wise
```

We now have the image header information stored in the `header` array. As discussed, we need three important things from this header.
+ width of the image (18th byte)
+ height of the image (22nd byte)
+ bitDepth of the image (28th byte)

Read these and store into different variables.

```c
int width = *(int*)&header[18]; // read the width from the image header
int height = *(int*)&header[22]; // read the height from the image header
int bitDepth = *(int*)&header[28]; // read the bitDepth from the image header
``` 

Next, if the **bitDepth** is <= 8, we need to read the colorTable.

```c
if(bitDepth <= 8)
	fread(colorTable, sizeof(unsigned char), 1024, streamIn);
```

`fread()` reads 1024 bytes of data in the colorTable array.

Now, we take another `buf[]` to store the image pixel information. This should be of the size `height * width` as the image pixels are stored in row and column format.

```c
unsigned char buf[height * width]; // to store the image data

fread(buf, sizeof(unsigned char), (height * width), streamIn);
```

We now have our pixel information in `buf[]`. Now it's time to copy all this to a new image.

```c
FILE *fo = fopen("/path/to/output/file.bmp","wb"); // Output File name
```

Write the image header first, which we have stored in the `header[]` array.

```c
fwrite(header, sizeof(unsigned char), 54, fo); // write the image header to output file
```

Write the color table to the output file.

```c
if(bitDepth <= 8)
	fwrite(colorTable, sizeof(unsigned char), 1024, fo);
```

Now, write the image information `buf[]`

```c
fwrite(buf, sizeof(unsigned char), (height * width), fo);
```

Now that everything has been read and written, it's time to close our files.

```c
fclose(fo);
fclose(streamIn);
```

After compiliing, and successfully executing this program, there should be an exact same copy of the lena image at the path which you specified in the `fo` pointer.

If that exists, and looks like this:

![Lena Grayscale](/images/lena512.bmp "Lena Copy")

Voila! Your first C program to copy an image is ready, **without the use of any external library**. :beers:

**TODO:** Now that you have successfully copied a grayscale image, try and copy a bitmap RGB image. (*Hint: You may want to check the bitDepth.*) Let me know of any problems you face in the discussions below.
