---
title: ColorSpaces
date: 2017/12/11 10:21:00
categories:
- OpenCV
tags:
- OpenCV
thumbnail: "http://ox8ixvjau.bkt.clouddn.com/blog/171210/9jAdD15e9i.jpg"
---

## <font color=#0099ff><center> Preface </center></font> ##

<font size=4 face="微软雅黑">

I analyze that the first step of my work is to split the images. [<font color=cyan>I follow this tutorial to learn color spaces.</font>](https://www.learnopencv.com/color-spaces-in-opencv-cpp-python/).The example and method is wonderful and allow me to fully understand how to choose the best color spaces.

</font>

## <font color=red><center>  Four Color Spaces </center></font> ##

<font size=4 face="微软雅黑">

Four color spaces are introduced. They are BGR, HSV, LAB and YCrCb respectively. There are certain formulas used to transform one color space into another. [<font color=cyan>You can access them from this website.</font>](https://docs.opencv.org/2.4/modules/imgproc/doc/miscellaneous_transformations.html) We choose them depending on their properties. So let us focus on their properties first.

<Strong><font color=tomato>The RGB Color Space:</font></strong>

![mark](http://ox8ixvjau.bkt.clouddn.com/blog/171210/EmHIDA3cea.png?imageslim)
1. The three channels(B, G, R) are correlated by the amount of light hitting the surface. In RGB color space the color information is separated into three channels but the same three channels also encode brightness information.
2. significant perceptual non-uniformity(Even with our eyes we can see the clear difference between indoor and outdoor B, G, R channels)
3. mixing of chrominance ( Color related information ) and luminance ( Intensity related information ) data

<Strong><font color=tomato>The LAB Color Space:</font></strong>

![mark](http://ox8ixvjau.bkt.clouddn.com/blog/171210/L1eFd94dmh.png?imageslim)
1. Different from RGB Color Space, LAB only have two channels encoding color. Let me introduce the meaning of different channels first:
	1. L-Lightness(Intensity)
	2. A-color component ranging from Green to Magenta
	3. B-color component ranging from Blue to Yellow

So, the L channel is independent of color information and encodes brightness only. The other two channels encode color.
2. Perceptually uniform color space which approximates how we perceive color.(We can not use our eyes to see clear difference between indoor and outdoor A, B channels which encodes color.)
3. It is pretty clear from the figure that the change in illumination has mostly affected the L component.

<Strong><font color=tomato>The YCrCb Color Space:</font></strong>

![mark](http://ox8ixvjau.bkt.clouddn.com/blog/171210/hH2fJf2hh3.png?imageslim)
1. The YCrCb color space is derived from the RGB color space and has the following three components
	1. Y-Luminance or Luma component obtained from RGB after gamma correction.
	2. Cr = R – Y ( how far is the red component from Luma ).
	3. Cb = B – Y ( how far is the blue component from Luma ).
2. Similar observations as LAB can be made for Intensity and color components with regard to Illumination changes
3. White has undergone change in all 3 components

<Strong><font color=tomato>The HSV Color Space:</font></strong>

![mark](http://ox8ixvjau.bkt.clouddn.com/blog/171211/J0alJ27GbE.png?imageslim)
1. The HSV color space has the following three components
	1. H-Hue ( Dominant Wavelength )
	2. Saturation ( Purity / shades of the color )
	3. Value ( Intensity )
2. The H Component is very similar in both the images which indicates the color information is intact even under illumination changes.
3. The S component is also very similar in both images.
4. The V Component captures the amount of light falling on it thus it changes due to illumination changes.

</font>

## <font color=Violet><center> How to Choose Color Spaces </center></font> ##

<font size=4 face="微软雅黑">

I run the code written by Satya Mallick. He built a interactive GUI to detect the color of the pixels in the images. You can obtain the values of different colors through this simple GUI. The next step is to extract certain colors from the cube. Still, he wrote a simple software which has Trackbars on the windows. Since each color space can be split into three particular channels, the author set up thresholds(the maximum and minimum values of the channels) for each channel and used the functions inRange and bitwise_and to extract the specific color. You can see the description of the inRange function [<font color=cyan>here</font>](https://docs.opencv.org/2.4/modules/core/doc/operations_on_arrays.html?highlight=inrange#inrange) and bitwise_and function [<font color=cyan>here.</font>](https://docs.opencv.org/2.4/modules/core/doc/operations_on_arrays.html#bitwise-and) <font color=Violet>I got stuck here for a while since I ignore the fact that the value is 8 bit rather than 1 bit which means that it ranges from 00000000 to 11111111. While doing & and ^ operation, every 8 bit joins in the operation.</font>

This method seems very great. But it is still hard to find the suitable thresholds manually. Still, there is a problem that even if you have found the best thresholds with all the pictures you have, it still be possible to fail while dealing with another image. So the author used another approach which is better. Let us analyze the code first.

The code block of interactive GUI:

Let us go into the main function first.
```cpp
int main( int argc, const char** argv )
{
    // filename
    // Read the input image
    int image_number = 0;
    int nImages = 10;

    if(argc > 1)
        nImages = atoi(argv[1]);

    char filename[20];
    sprintf_s(filename,"images/rub%02d.jpg",image_number%nImages);
    img = imread(filename);
    // Resize the image to 400x400
    Size rsize(400,400);
    resize(img,img,rsize);

    if(img.empty())
    {
        return -1;
    }

    // Create an empty window
    namedWindow("PRESS P for Previous, N for Next Image", WINDOW_AUTOSIZE);   
    // Create a callback function for any event on the mouse
    setMouseCallback( "PRESS P for Previous, N for Next Image", onMouse );

    imshow( "PRESS P for Previous, N for Next Image", img );
    while(1)
    {
		imshow("PRESS P for Previous, N for Next Image", img);
        char k = waitKey(30) & 0xFF;
        if (k == 27)
            break;
        //Check next image in the folder
        if (k =='n')
        {
            image_number++;
            sprintf_s(filename,"images/rub%02d.jpg",image_number%nImages);
            img = imread(filename);
            resize(img,img,rsize);
        }
        //Check previous image in he folder
        else if (k =='p')
        {
            image_number--;
            sprintf_s(filename,"images/rub%02d.jpg",image_number%nImages);
            img = imread(filename);
            resize(img,img,rsize);
        }
    }
    return 0;
}
```
This block of codes mainly implement three effects.
1. Load the images and resize them.
2. Show the image.
3. Press P for the previous image, N for the next image and Esc to quit.

Let separate the codes into three parts according to their effects.

```cpp
int image_number = 0;
int nImages = 10;
char filename[20];
sprintf_s(filename,"images/rub%02d.jpg",image_number%nImages);
img = imread(filename);
// Resize the image to 400x400
Size rsize(400,400);
resize(img,img,rsize);
```
There is only one line needed to explain.
```cpp
sprintf_s(filename,"images/rub%02d.jpg",image_number%nImages);
```
The function sprintf_s is similar to the function sprintf. You can learn the parameters of this function from [<font color=cyan>this website.</font>](http://www.cplusplus.com/reference/cstdio/sprintf/). I also have an example from another post.

</font>
