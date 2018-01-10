---
layout: post
title: RGB to sepia Image using C
---

Following the previous blog posts, I hope you now get a pretty clear idea about how to go ahead with image processing in C and also how to read color image. In this blog post, we will discuss about creating a sepia image of a RGB image.

For this blog post, we will again use our usual, *lena's* face.

![Lena Grayscale](/images/lena_color.bmp "Lena Grayscale")

The complete code for this post can be found [here.](https://github.com/abhijitnathwani/image-processing/blob/master/image_colortosepia.c)

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

Now Open the output file and write the image header and colortable to it.

```c
FILE *fo = fopen("/path/to/output/image.bmp", "wb");

fwrite(imageHeader, sizeof(unsigned char), 54, fo); // write the header back.
if(bitDepth <= 8)       // COLOR TABLE Present
        fwrite(colorTable, sizeof(unsigned char), 1024, fo); // write the color table back
```

Now, we will read the image data in buffer from input file using getc which is explained below. For that we will declare two-dimensional buffer to read data character by character using getc.

```c
int imgDataSize = width * height;		//calculate image size
unsigned char buffer[size][3];   		
    buffer[i][2] = getc(fp);                   //blue
    buffer[i][1] = getc(fp);                   //green
    buffer[i][0] = getc(fp);                   //red

```

Now, to convert the image into sepia, we need to apply conversion formula of RGB to sepia to all red, green and blue pixels. As the input image in our case is a rgb 24-bit (8-bit for each color), hence we know that the pixel value would be between `0-255` and not more than `MAX_VALUE`(255) for each color. We will check that the pixel value of each color does not exceed `MAX_VALUE`.We will get character by character values into buffer from all color planes.

The basic equation to get character values would be something like,

    r = (buffer[i][0]*0.393) + (buffer[i][1]*0.769) + (buffer[i][2]*0.189);
    g = (buffer[i][0]*0.349) + (buffer[i][1]*0.686) + (buffer[i][2]*0.168);
    b = (buffer[i][0]*0.272) + (buffer[i][1]*0.534) + (buffer[i][2]*0.131);


Now, it's time to write the C code to convert into sepia image for the same. We know that the image is arranged in terms of rows and columns, hence running `for loops` should solve our purpose.

```c
for(i=0;i<size;i++)
{
	r = 0;
	g = 0;
	b = 0;
	buffer[i][2] = getc(fp);			//blue
	buffer[i][1] = getc(fp);			//green
	buffer[i][0] = getc(fp);			//red

	//conversion formula of rgb to sepia
	r = (buffer[i][0]*0.393) + (buffer[i][1]*0.769)	+ (buffer[i][2]*0.189);
	g = (buffer[i][0]*0.349) + (buffer[i][1]*0.686)	+ (buffer[i][2]*0.168);
	b = (buffer[i][0]*0.272) + (buffer[i][1]*0.534)	+ (buffer[i][2]*0.131);

	if(r > MAX_VALUE){				//if value exceeds
		r = MAX_VALUE;
	}
	if(g > MAX_VALUE){
		g = MAX_VALUE;
	}
	if(b > MAX_VALUE){
		b = MAX_VALUE;
	}
	putc(b,fo);
	putc(g,fo);
	putc(r,fo);
		
}
```

As shown above, we read the data character by character so we will write it into file in that way only using putc.

Now that everything is complete, time to close the files.

```c
fclose(fo);
fclose(fp);
```

Compile the program, and run it. You should have a sepia image of lena formed in your specified directory.

![Lena Sepia](/images/lena_sepia.bmp)

If you are able to create the image as above, you have succesfully written your program to convert the image into sepia! :grinning:

Author:- **Priya Shah**
