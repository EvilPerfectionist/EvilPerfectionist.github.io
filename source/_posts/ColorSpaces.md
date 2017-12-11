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

#### <Strong><font color= #00FA9A >Let us go into the main function first.</font></strong> ####
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

<Strong><font color=tomato>Load the images and resize them</font></strong>

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
The function sprintf_s is similar to the function sprintf. You can learn the parameters of this function from [<font color=cyan>this website.</font>](http://www.cplusplus.com/reference/cstdio/sprintf/). I also have an example from another post. [<font color=cyan>You have better understand it from this example.</font>](https://evilperfectionist.github.io/Note%20of%20CPlusPlus%20I/)<font color=Violet>%02d is derived from %d which you must be familiar with. So the value of %02d is replaced by the value of the third parameter(image_number%nImages). You may be confused about the % in the third parameter, but actually the % is the Modulus Operator. It returns the the remainder of an integer division. For example, 1%10 = 1, 3%10 =3, 5%2 = 1. </font>

<Strong><font color=tomato>Show the image</font></strong>

```cpp
namedWindow("PRESS P for Previous, N for Next Image", WINDOW_AUTOSIZE);
imshow( "PRESS P for Previous, N for Next Image", img );
```
The format is stable. Just remember it.

<Strong><font color=tomato>Press P for the previous image, N for the next image and Esc to quit</font></strong>

```cpp
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
```
There is only one line needed to explain.
```cpp
char k = waitKey(30) & 0xFF
```
You can see the definition of waitKey [<font color=cyan>here.</font>](https://docs.opencv.org/2.4/modules/highgui/doc/user_interface.html?highlight=waitkey) However, you may be still confused about this line of code. You can see the pictures below as a reference.
![mark](http://ox8ixvjau.bkt.clouddn.com/blog/171211/H2BELh3Jbg.png?imageslim)

#### <Strong><font color= #00FA9A >Then is the setMouseCallback function</font></strong> ####

```cpp
void onMouse( int event, int x, int y, int flags, void* userdata )
{   
    if( event == EVENT_MOUSEMOVE )
	{

     	Vec3b bgrPixel(img.at<Vec3b>(y, x));

        Mat3b hsv,ycb,lab;
        // Create Mat object from vector since cvtColor accepts a Mat object
        Mat3b bgr (bgrPixel);

        //Convert the single pixel BGR Mat to other formats
        cvtColor(bgr, ycb, COLOR_BGR2YCrCb);
        cvtColor(bgr, hsv, COLOR_BGR2HSV);
        cvtColor(bgr, lab, COLOR_BGR2Lab);

        //Get back the vector from Mat
        Vec3b hsvPixel(hsv.at<Vec3b>(0,0));
        Vec3b ycbPixel(ycb.at<Vec3b>(0,0));
        Vec3b labPixel(lab.at<Vec3b>(0,0));

        // Create an empty placeholder for displaying the values
        placeholder = Mat::zeros(img.rows,400,CV_8UC3);

        //fill the placeholder with the values of color spaces
        putText(placeholder, format("BGR [%d, %d, %d]",bgrPixel[0],bgrPixel[1],bgrPixel[2]), Point(20, 70), FONT_HERSHEY_COMPLEX, .9, Scalar(255,255,255), 1);
        putText(placeholder, format("HSV [%d, %d, %d]",hsvPixel[0],hsvPixel[1],hsvPixel[2]), Point(20, 140), FONT_HERSHEY_COMPLEX, .9, Scalar(255,255,255), 1);
        putText(placeholder, format("YCrCb [%d, %d, %d]",ycbPixel[0],ycbPixel[1],ycbPixel[2]), Point(20, 210), FONT_HERSHEY_COMPLEX, .9, Scalar(255,255,255), 1);
        putText(placeholder, format("LAB [%d, %d, %d]",labPixel[0],labPixel[1],labPixel[2]), Point(20, 280), FONT_HERSHEY_COMPLEX, .9, Scalar(255,255,255), 1);


	    Size sz1 = img.size();
	    Size sz2 = placeholder.size();

        //Combine the two results to show side by side in a single image
        Mat combinedResult(sz1.height, sz1.width+sz2.width, CV_8UC3);
	    Mat left(combinedResult, Rect(0, 0, sz1.width, sz1.height));
	    img.copyTo(left);
	    Mat right(combinedResult, Rect(sz1.width, 0, sz2.width, sz2.height));
	    placeholder.copyTo(right);
	    imshow("PRESS P for Previous, N for Next Image", combinedResult);
    }
}
```
This block of codes mainly implement three effects.
1. Obtain the value of pixel and convert it into different color spaces.
2. Add and show the text.
3. Combine and show the image.

<Strong><font color=tomato>Obtain the value of pixel and convert it into different color spaces.</font></strong>

```cpp
        Vec3b bgrPixel(img.at<Vec3b>(y, x));

        Mat3b hsv,ycb,lab;
        // Create Mat object from vector since cvtColor accepts a Mat object
        Mat3b bgr (bgrPixel);

        //Convert the single pixel BGR Mat to other formats
        cvtColor(bgr, ycb, COLOR_BGR2YCrCb);
        cvtColor(bgr, hsv, COLOR_BGR2HSV);
        cvtColor(bgr, lab, COLOR_BGR2Lab);

        //Get back the vector from Mat
        Vec3b hsvPixel(hsv.at<Vec3b>(0,0));
        Vec3b ycbPixel(ycb.at<Vec3b>(0,0));
        Vec3b labPixel(lab.at<Vec3b>(0,0));
```
Vec3b intensity = img.at<Vec3b>(y, x); [<font color=cyan>can be used to acquire the intensity value of the pixel.</font>](https://docs.opencv.org/2.4.13.2/doc/user_guide/ug_mat.html) According to the RGB image,
1. uchar blue = intensity.val[0];
2. uchar green = intensity.val[1];
3. uchar red = intensity.val[2];

 If you want to go deep into the source with F12 and understand the syntax of this code and Mat3b, you need to know about some basic grammar of C++, such as Class and Templates.
```cpp
        cvtColor(bgr, ycb, COLOR_BGR2YCrCb);
        cvtColor(bgr, hsv, COLOR_BGR2HSV);
        cvtColor(bgr, lab, COLOR_BGR2Lab);
```
You can use function cvtColor to convert RGB image or pixel into HSV, YCrCb, LAB one.

<Strong><font color=tomato>Add and show the text.</font></strong>

```cpp
// Create an empty placeholder for displaying the values
placeholder = Mat::zeros(img.rows,400,CV_8UC3);

//fill the placeholder with the values of color spaces
putText(placeholder, format("BGR [%d, %d, %d]",bgrPixel[0],bgrPixel[1],bgrPixel[2]), Point(20, 70), FONT_HERSHEY_COMPLEX, .9, Scalar(255,255,255), 1);
putText(placeholder, format("HSV [%d, %d, %d]",hsvPixel[0],hsvPixel[1],hsvPixel[2]), Point(20, 140), FONT_HERSHEY_COMPLEX, .9, Scalar(255,255,255), 1);
putText(placeholder, format("YCrCb [%d, %d, %d]",ycbPixel[0],ycbPixel[1],ycbPixel[2]), Point(20, 210), FONT_HERSHEY_COMPLEX, .9, Scalar(255,255,255), 1);
putText(placeholder, format("LAB [%d, %d, %d]",labPixel[0],labPixel[1],labPixel[2]), Point(20, 280), FONT_HERSHEY_COMPLEX, .9, Scalar(255,255,255), 1);
```
What I want to mention in this part is CV_8UC3. The common syntax of this is {U|S|F}C(<number_of_channels>) where U is unsigned integer type, S is signed integer type, and F is float type. So CV_8UC3 is an 8-bit unsigned integer matrix/image with 3 channels. You can easily access the usage of putText function from some websites.

<Strong><font color=tomato>Combine and show the image.</font></strong>

```cpp
Size sz1 = img.size();
Size sz2 = placeholder.size();

	//Combine the two results to show side by side in a single image
Mat combinedResult(sz1.height, sz1.width+sz2.width, CV_8UC3);
Mat left(combinedResult, Rect(0, 0, sz1.width, sz1.height));
img.copyTo(left);
Mat right(combinedResult, Rect(sz1.width, 0, sz2.width, sz2.height));
placeholder.copyTo(right);
imshow("PRESS P for Previous, N for Next Image", combinedResult);
```
Just merge two images into one. If you successfully run the codes, you will get a interactive GUI like this.
![mark](http://ox8ixvjau.bkt.clouddn.com/blog/171211/H4IFlBGcgd.png?imageslim)

</font>
