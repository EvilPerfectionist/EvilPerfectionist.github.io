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

I analyze that the first step of my work is to split the images. [<font color=cyan>I follow this tutorial to learn color spaces.</font>](https://www.learnopencv.com/color-spaces-in-opencv-cpp-python/).The example and method is wonderful and allow me to fully understand how to choose the best color spaces. You can get the source code from my Github Account.

</font>

## <font color=red><center>  Four Color Spaces </center></font> ##

<font size=4 face="微软雅黑">

Four color spaces are introduced. They are BGR, HSV, LAB and YCrCb respectively. There are certain formulas used to transform one color space into another. [<font color=cyan>You can access them from this website.</font>](https://docs.opencv.org/2.4/modules/imgproc/doc/miscellaneous_transformations.html) We choose them depending on their properties. So let us focus on their properties first.

<Strong><font color=tomato>The RGB Color Space:</font></strong>

![mark](http://ox8ixvjau.bkt.clouddn.com/blog/171210/EmHIDA3cea.png?imageslim)
- The three channels(B, G, R) are correlated by the amount of light hitting the surface. In RGB color space the color information is separated into three channels but the same three channels also encode brightness information.
- significant perceptual non-uniformity(Even with our eyes we can see the clear difference between indoor and outdoor B, G, R channels)
- mixing of chrominance ( Color related information ) and luminance ( Intensity related information ) data

<Strong><font color=tomato>The LAB Color Space:</font></strong>

![mark](http://ox8ixvjau.bkt.clouddn.com/blog/171210/L1eFd94dmh.png?imageslim)
- Different from RGB Color Space, LAB only have two channels encoding color. Let me introduce the meaning of different channels first:
	   - L-Lightness(Intensity)
	   - A-color component ranging from Green to Magenta
	   - B-color component ranging from Blue to Yellow

So, the L channel is independent of color information and encodes brightness only. The other two channels encode color.
- Perceptually uniform color space which approximates how we perceive color.(We can not use our eyes to see clear difference between indoor and outdoor A, B channels which encodes color.)
- It is pretty clear from the figure that the change in illumination has mostly affected the L component.

<Strong><font color=tomato>The YCrCb Color Space:</font></strong>

![mark](http://ox8ixvjau.bkt.clouddn.com/blog/171210/hH2fJf2hh3.png?imageslim)
- The YCrCb color space is derived from the RGB color space and has the following three components
	   - Y-Luminance or Luma component obtained from RGB after gamma correction.
	   - Cr = R – Y ( how far is the red component from Luma ).
	   - Cb = B – Y ( how far is the blue component from Luma ).
- Similar observations as LAB can be made for Intensity and color components with regard to Illumination changes
- White has undergone change in all 3 components

<Strong><font color=tomato>The HSV Color Space:</font></strong>

![mark](http://ox8ixvjau.bkt.clouddn.com/blog/171211/J0alJ27GbE.png?imageslim)
- The HSV color space has the following three components
	   - H-Hue ( Dominant Wavelength )
	   -  Saturation ( Purity / shades of the color )
	   - Value ( Intensity )
- The H Component is very similar in both the images which indicates the color information is intact even under illumination changes.
- The S component is also very similar in both images.
- The V Component captures the amount of light falling on it thus it changes due to illumination changes.

</font>

## <font color=Violet><center> How to Choose Color Spaces </center></font> ##

<font size=4 face="微软雅黑">

I run the code written by Satya Mallick. He built a interactive GUI to detect the color of the pixels in the images. You can obtain the values of different colors through this simple GUI. The next step is to extract certain colors from the cube. Still, he wrote a simple software which has Trackbars on the windows. Since each color space can be split into three particular channels, the author set up thresholds(the maximum and minimum values of the channels) for each channel and used the functions inRange and bitwise_and to extract the specific color. You can see the description of the inRange function [<font color=cyan>here</font>](https://docs.opencv.org/2.4/modules/core/doc/operations_on_arrays.html?highlight=inrange#inrange) and bitwise_and function [<font color=cyan>here.</font>](https://docs.opencv.org/2.4/modules/core/doc/operations_on_arrays.html#bitwise-and) <font color=Violet>I got stuck here for a while since I ignore the fact that the value is 8 bit rather than 1 bit which means that it ranges from 00000000 to 11111111. While doing & and ^ operation, every 8 bit joins in the operation.</font>

This method seems very great. But it is still hard to find the suitable thresholds manually. Still, there is a problem that even if you have found the best thresholds with all the pictures you have, it still be possible to fail while dealing with another image. So the author used another approach which is better. Let us analyze the code first.

###<font color= #ff3399 >The code block of interactive GUI:</font> ###

#### <font color= #00FA9A >Let us go into the main function first.</font> ####
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
- Load the images and resize them.
- Show the image.
- Press P for the previous image, N for the next image and Esc to quit.

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
- Obtain the value of pixel and convert it into different color spaces.
- Add and show the text.
- Combine and show the image.

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
- uchar blue = intensity.val[0];
- uchar green = intensity.val[1];
- uchar red = intensity.val[2];

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

###<font color= #ff3399 >The code block of setting thresholds:</font> ###
In this block of code, there is nothing I should explain in detail since the functions inRange and bitwise_and are mentioned already.

###<font color= #ff3399 >The code block of DataAnalysis:</font> ###

This block of code are written in python. I will choose some specific functions to introduce.

<Strong><font color=tomato>Load and convert the images</font></strong>

```python
color = 'pieces/yellow'
files = glob.glob(color + '*.jpg')
files.sort()
for fi in files[:]:
    # BGR
    im = cv2.imread(fi)
    b = im[:,:,0]
    b = b.reshape(b.shape[0]*b.shape[1])
    g = im[:,:,1]
    g = g.reshape(g.shape[0]*g.shape[1])
    r = im[:,:,2]
    r = r.reshape(r.shape[0]*r.shape[1])
    B = np.append(B,b)
    G = np.append(G,g)
    R = np.append(R,r)
    # HSV
    hsv = cv2.cvtColor(im,cv2.COLOR_BGR2HSV)
    h = hsv[:,:,0]
    h = h.reshape(h.shape[0]*h.shape[1])
    s = hsv[:,:,1]
    s = s.reshape(s.shape[0]*s.shape[1])
    v = hsv[:,:,2]
    v = v.reshape(v.shape[0]*v.shape[1])
    H = np.append(H,h)
    S = np.append(S,s)
    V = np.append(V,v)
    # YCrCb
    ycb = cv2.cvtColor(im,cv2.COLOR_BGR2YCrCb)
    y = ycb[:,:,0]
    y = y.reshape(y.shape[0]*y.shape[1])
    cr = ycb[:,:,1]
    cr = cr.reshape(cr.shape[0]*cr.shape[1])
    cb = ycb[:,:,2]
    cb = cb.reshape(cb.shape[0]*cb.shape[1])
    Y = np.append(Y,y)
    Cr = np.append(Cr,cr)
    Cb = np.append(Cb,cb)
    # Lab
    lab = cv2.cvtColor(im,cv2.COLOR_BGR2LAB)
    ll = lab[:,:,0]
    ll = ll.reshape(ll.shape[0]*ll.shape[1])
    la = lab[:,:,1]
    la = la.reshape(la.shape[0]*la.shape[1])
    lb = lab[:,:,2]
    lb = lb.reshape(lb.shape[0]*lb.shape[1])
    LL = np.append(LL,ll)
    LA = np.append(LA,la)
    LB = np.append(LB,lb)
```
glob.glob returns a possibly-empty list of path names that match pathname, which must be a string containing a path specification.

sort() method can return a sorted list. [<font color=cyan>You can better understand it from this post.</font>](https://evilperfectionist.github.io/Note%20of%20CPlusPlus%20I/l)

If you want to split an image in a certain color space into three channels. You can use codes like this:
```python
b = im[:,:,0]
g = im[:,:,1]
r = im[:,:,2]
```
np.append appends values to the end of an array. [<font color=cyan>You can better understand it from this website.</font>](https://docs.scipy.org/doc/numpy-1.13.0/reference/generated/numpy.append.html) So in this case, since the code is in a for loop, the blue channels from different images are extracted and combined together into one matrix B. So are the other channels.

<Strong><font color=tomato>Make a density plot</font></strong>
```python
nbins = 11
plt.figure(figsize=[20,10])
plt.subplot(2,3,1)
plt.hist2d(B, G, bins=nbins, norm=LogNorm())
plt.xlabel('B')
plt.ylabel('G')
plt.title('RGB')
if not zoom:
    plt.xlim([0,255])
    plt.ylim([0,255])
plt.colorbar()
plt.subplot(2,3,2)
plt.hist2d(B, R, bins=nbins, norm=LogNorm())
plt.colorbar()
plt.xlabel('B')
plt.ylabel('R')
plt.title('RGB')
if not zoom:
    plt.xlim([0,255])
    plt.ylim([0,255])
plt.subplot(2,3,3)
plt.hist2d(R, G, bins=nbins, norm=LogNorm())
plt.colorbar()
plt.xlabel('R')
plt.ylabel('G')
plt.title('RGB')
if not zoom:
    plt.xlim([0,255])
    plt.ylim([0,255])

plt.subplot(2,3,4)
plt.hist2d(H, S, bins=nbins, norm=LogNorm())
plt.colorbar()
plt.xlabel('H')
plt.ylabel('S')
plt.title('HSV')
if not zoom:
    plt.xlim([0,180])
    plt.ylim([0,255])

plt.subplot(2,3,5)
plt.hist2d(Cr, Cb, bins=nbins, norm=LogNorm())
plt.colorbar()
plt.xlabel('Cr')
plt.ylabel('Cb')
plt.title('YCrCb')
if not zoom:
    plt.xlim([0,255])
    plt.ylim([0,255])

plt.subplot(2,3,6)
plt.hist2d(LA, LB, bins=nbins, norm=LogNorm())
plt.colorbar()
plt.xlabel('A')
plt.ylabel('B')
plt.title('LAB')
if not zoom:
    plt.xlim([0,255])
    plt.ylim([0,255])
    plt.savefig(color + '.png',bbox_inches='tight')
else:
    plt.savefig(color + '-zoom.png',bbox_inches='tight')

plt.show()
```
plt.figure(figsize=[20,10]) make a background whose size is 20*10

plt.subplot(2,3,1) separate the whole figure into a 2*3 region and the third parameter indicates the location of the picture. According to a 2*3 region, six pictures can be showed.

plt.hist2d is a little difficult to understand. [<font color=cyan>You can grasp it from this post.</font>](https://evilperfectionist.github.io/Note%20of%20CPlusPlus%20I/l) It is easy for us to observe the degree of the density of the channels through the image plotted by plt.hist2d.
![mark](http://ox8ixvjau.bkt.clouddn.com/blog/171211/I8a0hI9Ljf.png?imageslim)

Also you can learn about the usage of "if not" syntax [<font color=cyan>here.</font>](https://evilperfectionist.github.io/Note%20of%20CPlusPlus%20I/l)

</font>

## <font color=#00ff00><center> Conclusion </center></font> ##

<font size=4 face="微软雅黑">

As the Illumination changes by a large amount, we can see that:

- Ideally, we want to work with a color space with the most compact / concentrated density plot for color channels.(because when the color value is concentrated in a small region, we can use a small range of thresholds to extract it. And it is robust to other conditions as well.)
- The density plots for RGB blow up drastically. This means that the variation in the values of the channels is very high and fixing a threshold is a big problem.
- In HSV, since only the H component contains information about the absolute color. Thus, it becomes my first choice of color space since I can tweak just one knob ( H ) to specify a color as compared to 2 knobs in YCrCb ( Cr and Cb ) and LAB ( A and B ).(And as you can see, the value of H channel is concentrated in a small range, it is also an important feature of HSV)
- Comparing the plots of YCrCb and LAB shows a higher level of compactness in case of LAB. So, next best choice for me becomes the LAB color space.

</font>

## <font color=yellowish><center>The End</center> ##
