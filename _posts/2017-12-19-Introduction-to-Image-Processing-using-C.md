---
layout: post
title: Introduction to Image Processing using C 
---

So most of you would be aware of various Image Processing techniques, on different platforms such as MATLAB, SciLAB, etc. Here, in this series of blog posts, we are only going to focus on image processing using C language. At times, the platforms named above are not available to us, and there may be a need to process images in most basic language, such as C. 

##### Why image processing in C ?

Well, recently I was assigned a task to optimize an image processing code in C, and I had no clue how to go about and what are the image processing operations to be performed on C, *without the use of any external libraries*. After a lot of digging on the internet, reading books and articles, I finally managed to do the task. Also, I realized that the resources are all available on the internet, however, *they lie scattered*. Hence, these series of blog posts have been created so that one must get most of the required information at a go, and can start with  ***Image Processing using C*** .

##### Image formats

There are various widely available image formats such as JPEG, PNG, TIFF, BMP, etc. For ease, we would be discussing all the programs and operations around the Bitmap file format. As the BMP is less complex to understand and decode than the compressed formats such as JPEG, we would be preferring this. As we do not intend to use any library as much as possible, we will have to deal with all the image headers and the smallest byte of information through our own code, BMP comes in handy to use.
