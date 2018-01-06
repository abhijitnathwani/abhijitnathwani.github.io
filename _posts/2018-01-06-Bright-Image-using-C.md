---
layout: post
title: Bright Image using C
---

Following the previous blog posts, I hope you now get a pretty clear idea about how to go ahead with image processing in C. In this blog post, we will discuss about creating a bright image of an image.

For this blog post, we will again use our usual, *lena's* face.

![Lena Grayscale](/images/lena512.bmp "Lena Grayscale")

The complete code for this post can be found [here.](https://github.com/abhijitnathwani/image-processing/blob/master/image_bright.c)

Starting with the usual procedure, image is nothing but a file for C. So go ahead and open the file.

```c
FILE* fp = fopen("/path/to/your/image.bmp", "rb");   //read the file//
```

Following the Bitmap basics, strip out the image header.

```c
fread(imageHeader, sizeof(unsigned char), 54, fp); // read the 54-byte from fp to imageHeader
```

Extract the required information from the image header,
+ Width 
+ Height
+ bitDepth

```c
// extract image height and width from imageHeader      
int width = *(int*)&imageHeader[18];
int height = *(int*)&imageHeader[22];
int bitDepth = *(int*)&imageHeader[28];
```
Next, read the colorTable if it exists.

```c
if(bitDepth <= 8)	// COLOR TABLE Present
	fread(colorTable, sizeof(unsigned char), 1024, fp); // read the 1024-byte from fp to colorTable
```

Now, we will read the image data in buffer from input file using fread.

```c
int imgDataSize = width * height; // calculate image size

fread(buffer,sizeof(unsigned char),imgDataSize,fp);			//read image data
```

Now, to create the bright image, we need to increase the pixel values. For that, we add `BRIGHTNESS_FACTOR`(in our case 25) to every pixel. We will add this value to each pixel. As the input image in our case is a gray-scale image, hence we know that the pixel value would be between `0-255` and not more than 255. We will check that the pixel value does not exceed `255`.

The basic equation would be something like,

    buffer[i] = buffer[i] + BRIGHTNESS_FACTOR ;


Now, it's time to write the C code for the same. We know that the image is arranged in terms of rows and columns, hence running `for loops` should solve our purpose.

```c
for(i = 0; i < size; i++)
{
	buffer[i] = buffer[i] + BRIGHTNESS_FACTOR;
	buffer[i] = (buffer[i] > MAX_COLOR) ? MAX_COLOR : buffer[i];  
}
``` 

Now that we have the new image data in our `buffer` array, it's time to construct the output image.

Open the output file and write the image header and colortable to it.

```c
FILE *fo = fopen("/path/to/output/image.bmp", "wb");

fwrite(imageHeader, sizeof(unsigned char), 54, fo); // write the header back.
if(bitDepth <= 8)	// COLOR TABLE Present
       	fwrite(colorTable, sizeof(unsigned char), 1024, fo); // write the color table back
```

Now write the `buffer` information to the output image

```c
fwrite( buffer, sizeof(unsigned char), imgDataSize, fo); // write the values of the bright image.
```

Now that everything is complete, time to close the files.

```c
fclose(fo);
fclose(fp);
```

Compile the program, and run it. You should have a bright image of lena formed in your specified directory.

![Lena Bright](/images/lena_bright.bmp)

If are able to create the image as above, you have succesfully written your program to make the image brighter ! :sunglasses:

Author:- **Priya Shah**
