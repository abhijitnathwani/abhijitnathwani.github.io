---
layout: post
title: Rotate Image using C
---

Following the previous blog posts, I hope you now get a pretty clear idea about how to go ahead with image processing in C. In this blog post, we will discuss about creating a bright image of an image.

For this blog post, we will again use our usual, *lena's* face.

![Lena Grayscale](/images/lena512.bmp "Lena Grayscale")

The complete code for this post can be found [here.](https://github.com/abhijitnathwani/image-processing/blob/master/image_rotate.c)

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

Now, to rotate the image 180 degree, we need to swap the pixel values. For that, we replace all the columns to rows and vice versa. So we will take out buffer and rotated values will be stored in out buffer. And thus it will rotate our image.

The basic equation would be something like,

    out_buffer[width-1][j] = buffer[i][j] ;


Now, it's time to write the C code for the same. We know that the image is arranged in terms of rows and columns, hence running `for loops` should solve our purpose.

```c
for(i = 0; i < width; i++)
{
	for(j = 0; j < height; j++){
		out_buffer[width-1][j] = buffer[i][j];
	}
}
``` 

Now that we have the new image data in our `out_buffer` array, it's time to construct the output image.

Open the output file and write the image header and colortable to it.

```c
FILE *fo = fopen("/path/to/output/image.bmp", "wb");

fwrite(imageHeader, sizeof(unsigned char), 54, fo); // write the header back.
if(bitDepth <= 8)	// COLOR TABLE Present
       	fwrite(colorTable, sizeof(unsigned char), 1024, fo); // write the color table back
```

Now write the `out_buffer` information to the output image

```c
fwrite( out_buffer, sizeof(unsigned char), imgDataSize, fo); // write the values of the bright image.
```

Now that everything is complete, time to close the files.

```c
fclose(fo);
fclose(fp);
```

Compile the program, and run it. You should have a rotated image of lena formed in your specified directory.

![Lena Rotate](/images/lena_rotate.bmp)

If you are able to create the image as above, you have succesfully written your program to rotate the image ! :smiley:

Author:- **Priya Shah**
