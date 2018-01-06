---
layout: post
title: Negative Image using C
---

Following the previous blog posts, I hope you now get a pretty clear idea about how to go ahead with image processing in C. In this blog post, we will discuss about creating a negative image of an image.

For this blog post, we will again use our usual, *lena's* face.

![Lena Grayscale](/images/lena512.bmp "Lena Grayscale")

The complete code for this post can be found [here.](https://github.com/abhijitnathwani/image-processing/blob/master/negative_image.c)

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

Now, we allocate the block of memory as per the imageSize, to read the image information.

```c
int imgDataSize = width * height; // calculate image size

imageData = (unsigned char*)malloc (imgDataSize * sizeof(unsigned char)); // allocate the block of memory as big as the image size
newimageData = (unsigned char*)malloc (imgDataSize * sizeof(unsigned char));
```

Now, to create the negative image, we need to have the complement of the color which it already has. The input image in our case is a gray-scale image, hence we know that the pixel value would be between `0-255`. To find the complement, we just subtract the read image value from 255.
The basic equation would be something like,

    new_pixel_data = 255 - old_pixel_data;

Now, it's time to write the C code for the same. We know that the image is arranged in terms of rows and columns, hence running two `for loops` should solve our purpose.

```c
for(i = 0; i < height; i++)
{
	for(j = 0; j < width; j++)
	{                   
		    newimageData[i*width + j] = 255 - imageData[i*width + j]; 
	}   
}
``` 

Now that we have the new image data in our `newimageData` array, it's time to construct the output image.

Open the output file and write the image header and colortable to it.

```c
FILE *fo = fopen("/path/to/output/image.bmp", "wb");

fwrite(imageHeader, sizeof(unsigned char), 54, fo); // write the header back.
if(bitDepth <= 8)	// COLOR TABLE Present
       	fwrite(colorTable, sizeof(unsigned char), 1024, fo); // write the color table back
```

Now write the `newimageData` information to the output image

```c
fwrite( newimageData, sizeof(unsigned char), imgDataSize, fo); // write the values of the negative image.
```

Now that everything is complete, time to close the files.

```c
fclose(fo);
fclose(fp);
```

Compile the program, and run it. You should a negative image of lena formed in your specified directory.

![Lena Negative](/images/lena_negative.bmp)

If are able to create the image as above, you have succesfully written your program for image negation! :beers:


