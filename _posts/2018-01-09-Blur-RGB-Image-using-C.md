---
layout: post
title: Blur RGB Image using C
---

Following the previous blog posts, I hope you now get a pretty clear idea about how to go ahead with image processing in C. In this blog post, we will discuss about creating a blur image of a RGB image.

For this blog post, we will again use our usual, *lena's* face.

![Lena Color](/images/lena_color.bmp "Lena Color")

The complete code for this post can be found [here.](https://github.com/abhijitnathwani/image-processing/blob/master/image_blur_color.c)

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

First we will initialize the kernel to store image data,

```c
unsigned char buffer[size][3];						//store the input image data
unsigned char out[size][3];						//store the output image data
```

To initialize kernel,

```c
{% raw %}
float v=1.0 / 9.0;											
float kernel[3][3]={{v,v,v},				  //initialize the blurrring kernel
		   {v,v,v},
		   {v,v,v}};

{% endraw %}
```

The basic equation for each of the color plane would be something like,

    sum0=sum0+(float)kernel[i+1][j+1]*buffer[(x+i)*width+(y+j)][0];
    sum1=sum1+(float)kernel[i+1][j+1]*buffer[(x+i)*width+(y+j)][1];
    sum2=sum2+(float)kernel[i+1][j+1]*buffer[(x+i)*width+(y+j)][2];

Now, it's time to write the C code for the same. We know that the image is arranged in terms of rows and columns, hence running `for loops` should solve our purpose.

```c
for(x=1;x<height-1;x++)
	{					
		for(y=1;y<width-1;y++)
		{
			float sum0= 0.0;
			float sum1= 0.0;
			float sum2= 0.0;
			for(i=-1;i<=1;++i)
			{
				for(j=-1;j<=1;++j)			//matrix multiplication with kernel with every color plane
				{				
					sum0=sum0+(float)kernel[i+1][j+1]*buffer[(x+i)*width+(y+j)][0];
					sum1=sum1+(float)kernel[i+1][j+1]*buffer[(x+i)*width+(y+j)][1];
					sum2=sum2+(float)kernel[i+1][j+1]*buffer[(x+i)*width+(y+j)][2];
				}
			}
			out[(x)*width+(y)][0]=sum0;
			out[(x)*width+(y)][1]=sum1;
			out[(x)*width+(y)][2]=sum2;
		}
	}
``` 

Now that we have the new image data in our `out` array, it's time to construct the output image.

Open the output file and write the image header and colortable to it.

```c
FILE *fo = fopen("/path/to/output/image.bmp", "wb");

fwrite(imageHeader, sizeof(unsigned char), 54, fo); // write the header back.
if(bitDepth <= 8)	// COLOR TABLE Present
       	fwrite(colorTable, sizeof(unsigned char), 1024, fo); // write the color table back
```

Now write the `out_buffer` information to the output image

```c
for(i=0;i<size;i++)					//write image data back to the file
	{
		putc(out[i][2],fOut);
		putc(out[i][1],fOut);
		putc(out[i][0],fOut);
	}
```

Now that everything is complete, time to close the files.

```c
fclose(fo);
fclose(fp);
```

Compile the program, and run it. You should have a blur RGB image of lena formed in your specified directory.

![Lena Rotate](/images/lena_color_blur.bmp)

If you are able to create the image as above, you have succesfully written your program to blur the RGB image ! :smiley:

Author:- **Priya Shah**
