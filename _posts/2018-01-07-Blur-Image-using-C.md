---
layout: post
title: Blur Image using C
---

Following the previous blog posts, I hope you now get a pretty clear idea about how to go ahead with image processing in C. In this blog post, we will discuss about creating a blur image of an image.

For this blog post, we will again use our usual, *lena's* face.

![Lena Grayscale](/images/lena512.bmp "Lena Grayscale")

The complete code for this post can be found [here.](https://github.com/abhijitnathwani/image-processing/blob/master/image_blur_gray.c)

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

fread(buffer,sizeof(unsigned char),imgDataSize,fp);        //read image data
```

Now, to blur the image, we need to initialize a blurring kernel(matrix). And we will do matrix multiplication. These values will be stored in out buffer. And thus it will blur our image.

To initialize kernel,

```c
{% raw %}
float v=1.0 / 9.0;											
float kernel[3][3]={{v,v,v},				  //initialize the blurrring kernel
		   {v,v,v},
		   {v,v,v}};

{% endraw %}
```

The basic equation would be something like,

    sum=sum+(float)kernel[i+1][j+1]*buffer[(x+i)*width+(y+j)];


Now, it's time to write the C code for the same. We know that the image is arranged in terms of rows and columns, hence running `for loops` should solve our purpose.

```c
for(i=0;i<size;i++)
	{
		out_buffer[i] = buffer[i];										//copy image data to out bufer
	}

	for(x=1;x<height-1;x++)
	{
		for(y=1;y<width-1;y++)
		{
			float sum= 0.0;
			for(i=-1;i<=1;++i)									
			{
				for(j=-1;j<=1;++j)
				{
					sum=sum+(float)kernel[i+1][j+1]*buffer[(x+i)*width+(y+j)];	//matrix multiplication with kernel
				}
			}
			out_buffer[(x)*width+(y)]=sum;
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

Compile the program, and run it. You should have a blur image of lena formed in your specified directory.

![Lena Rotate](/images/lena_blur.bmp)

If you are able to create the image as above, you have succesfully written your program to blur the image ! :smiley:

Author:- **Priya Shah**
