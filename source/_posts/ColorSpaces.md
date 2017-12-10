---
title: ColorSpaces
date: 2017/10/2 10:21:00
categories:
- OpenCV
tags:
- OpenCV
thumbnail: "http://ox8ixvjau.bkt.clouddn.com/images/0.jpg"
---

## <font color=#0099ff><center> Preface </center></font> ##

<font size=4 face="微软雅黑">

This year our team will participate in a robot competition,Robomaster.Instead of taking charge of mechanisms,I am trying to learn coding and automatic control.Recently,I am learning C++ and OpenCV.And I will take notes and write some blogs to summarize the knowledge I have learned.

<strong><font color=DeepPink>When you analyze the codes, the most important thing is to separate them into different parts according to the functions. And then annotate the last part or sentence. Next, analyze the parameters in them. They will take you back to the other parts. Then,you will grasp the structure of the codes and gradually understand all of them . So, remember this method and use it. </font></strong>

</font>

## <font color=red><center> Content </center></font> ##

```cpp
#include <opencv2/opencv.hpp>
#include <opencv2/imgproc/imgproc.hpp>
#include <opencv2/highgui/highgui.hpp>
#include <iostream>

using namespace cv;
using namespace std;


#define T_ANGLE_THRE 10
#define T_SIZE_THRE 5


void BrightAdjust(IplImage* src, IplImage* dst,
	double dContrast, double dBright)
{
	int nVal;

	unsigned char* SrcData = (unsigned char*)src->imageData;
	unsigned char* DstData = (unsigned char*)dst->imageData;
	int step = src->widthStep / sizeof(unsigned char) / 3;

	for (int nI = 0; nI<src->height; nI++)
	{
		for (int nJ = 0; nJ <src->width; nJ++)
		{
			for (int nK = 0; nK < 3; nK++)
			{
				nVal = (int)(dContrast * SrcData[(nI*step + nJ) * 3 + nK] + dBright);
				if (nVal < 0)
					nVal = 0;
				if (nVal > 255)
					nVal = 255;
				DstData[(nI*step + nJ) * 3 + nK] = nVal;
			}
		}
	}
}

void GetDiffImage(IplImage* src1, IplImage* src2, IplImage* dst, int nThre)
{
	unsigned char* SrcData1 = (unsigned char*)src1->imageData;
	unsigned char* SrcData2 = (unsigned char*)src2->imageData;
	unsigned char* DstData = (unsigned char*)dst->imageData;
	int step = src1->widthStep / sizeof(unsigned char);

	for (int nI = 0; nI<src1->height; nI++)
	{
		for (int nJ = 0; nJ <src1->width; nJ++)
		{
			if (SrcData1[nI*step + nJ] - SrcData2[nI*step + nJ]> nThre)
			{
				DstData[nI*step + nJ] = 255;
			}
			else
			{
				DstData[nI*step + nJ] = 0;
			}
		}
	}
}

int main()
{
	IplImage* pFrame0 = cvLoadImage("6.jpg");

	CvSize pImgSize;
	CvBox2D s;
	vector<CvBox2D> vEllipse;
	vector<CvBox2D> vRlt;
	vector<CvBox2D> vArmor;
	CvScalar sl;
	bool bFlag = false;
	CvSeq *pContour = NULL;
	CvMemStorage *pStorage = cvCreateMemStorage(0);

	pImgSize = cvGetSize(pFrame0);

	IplImage *pRawImg = cvCreateImage(pImgSize, IPL_DEPTH_8U, 3);

	IplImage* pGrayImage = cvCreateImage(pImgSize, IPL_DEPTH_8U, 1);
	IplImage* pRImage = cvCreateImage(pImgSize, IPL_DEPTH_8U, 1);
	IplImage* pGImage = cvCreateImage(pImgSize, IPL_DEPTH_8U, 1);
	IplImage *pBImage = cvCreateImage(pImgSize, IPL_DEPTH_8U, 1);
	IplImage *pBinary = cvCreateImage(pImgSize, IPL_DEPTH_8U, 1);
	IplImage *pRlt = cvCreateImage(pImgSize, IPL_DEPTH_8U, 1);

	CvSeq* lines = NULL;
	CvMemStorage* storage = cvCreateMemStorage(0);
	while (1)
	{
		if (pFrame0)
		{
			BrightAdjust(pFrame0, pRawImg, 1, -120);
			cvSplit(pRawImg, pBImage, pGImage, pRImage, NULL);

			GetDiffImage(pRImage, pGImage, pBinary, 25);
			cvErode(pBinary, pGrayImage, NULL, 1);
			cvDilate(pGrayImage, pRlt, NULL, 3);

			cvFindContours(pRlt, pStorage, &pContour, sizeof(CvContour), CV_RETR_CCOMP, CV_CHAIN_APPROX_SIMPLE);
			for (; pContour != NULL; pContour = pContour->h_next)
			{
				if (pContour->total > 10)
				{
					bFlag = true;
					s = cvFitEllipse2(pContour);

					for (int nI = 0; nI < 5; nI++)
					{
						for (int nJ = 0; nJ < 5; nJ++)
						{
							if (s.center.y - 2 + nJ > 0 && s.center.y - 2 + nJ < 480 && s.center.x - 2 + nI > 0 && s.center.x - 2 + nI <  640)
							{
								sl = cvGet2D(pFrame0, (int)(s.center.y - 2 + nJ), (int)(s.center.x - 2 + nI));
								if (sl.val[0] < 200 || sl.val[1] < 200 || sl.val[2] < 200)
									bFlag = false;
							}
						}
					}
					if (bFlag)
					{
						vEllipse.push_back(s);
						cvEllipseBox(pFrame0, s, CV_RGB(255, 0, 0), 2, 8, 0);
					}
				}

			}
			cvShowImage("2", pFrame0);
			cvShowImage("3", pRawImg);
			cvShowImage("4", pBinary);
			cvShowImage("5", pGrayImage);
			cvShowImage("6", pRlt);
			cvWaitKey(0);
		}
	}
	return 0;
}
```

<center>
{% qnimg 1.jpg %}
</center>

### <center> This is the picture I want to detect. </center> ###

<center>
{% qnimg 2.jpg %}
</center>

### <center> This is the dectected picture. </center> ###


```cpp
cvEllipseBox(pFrame0, s, CV_RGB(255, 0, 0), 2, 8, 0);
```

This sentence turn the former to the latter.So,I begin my analysis from here.This function is used to draw elliptic arc or fill an ellipse sector.[<font color=cyan>You can click here to see it</font>](http://docs.opencv.org/2.4/modules/core/doc/drawing_functions.html).I have changed the parameters to observe its change.

The fourth parameter which is given as '2' represents the thickness of the ellipse arc outline, <font color=red>if positive</font>.Otherwise, this indicates that a filled ellipse sector is to be drawn.In other words, if the parameter is setted>0(positive),a outline will be shown.While the parameter is setted<0,a filled ellipse outline will be shown.

The third parameter is color,There are two functions,CV_RGB(<font color=red>R,G,B</font>) and cvScalar(<font color=red>B,G,R</font>).In this case,namely CV_RGB(255, 0, 0), it means complete red.<font color=LawnGreen >And two functions are connected with each other by #define</font>.

The second parameter is box. I can not clearly understand it right now.But this parameter indicates that the function draws <font color=GreenYellow > an ellipse inscribed in the rotated rectangle </font>.It can be understood like this:
<center>
{% qnimg inscribed.jpg %}
</center>

So,we can notice that 's' is an important parameter.I decide to begin with it.After define it with this code:
```cpp
CvBox2D s;
```
An important sentence should be paid attention to:
```cpp
s = cvFitEllipse2(pContour);
```
The function calculates the ellipse that fits a set of 2D points best of all. It returns the rotated rectangle in which the ellipse is inscribed.And then we look downward:
```cpp
for (int nI = 0; nI < 5; nI++)
{
	for (int nJ = 0; nJ < 5; nJ++)
	{
		if (s.center.y - 2 + nJ > 0 && s.center.y - 2 + nJ < 480 && s.center.x - 2 + nI > 0 && s.center.x - 2 + nI <  640)
		{
			sl = cvGet2D(pFrame0, (int)(s.center.y - 2 + nJ), (int)(s.center.x - 2 + nI));
			if (sl.val[0] < 200 || sl.val[1] < 200 || sl.val[2] < 200)
			bFlag = false;
		}
	}
}
```
We can see 's.center.y' and 's.center.x' in these codes.So 'CvBox2D' must be a struct.And one of its methods is 'center'.Of course, the function of 'center' must be a struct as well,it has two methods,'x' and 'y'.<font color=LawnGreen >So,'s.center.y' represents the y coordinates. </font>

These codes detect the ellipses which beyond the picture and delete them.Besides,they also detect the color surrounding the center of every ellipses.If they are not suitable,the bFlag will be equivalent to 'false'.

'sl' is defined above:
```cpp
CvScalar sl;
```
We should notice that the type 'CvScalar' is different from the type 'cvScalar' which is mentioned above.It does not relate to color at all.And it has one method 'val'.

<font color=red>Besides,I want to say that 'int n[6];' is allowed,which means it defines an array which has 6 numbers.</font>

Now,we completely deal with the variable 's'.And next one is 'pContour' and 'pFrame0'.
```cpp
CvSize pImgSize;
pImgSize = cvGetSize(pFrame0);
```
'CvSize' means the size of a rectangle or an image.

'cvGetSize' returns number of rows and number of columns.

```cpp
	IplImage *pRawImg = cvCreateImage(pImgSize, IPL_DEPTH_8U, 3);
	IplImage* pGrayImage = cvCreateImage(pImgSize, IPL_DEPTH_8U, 1);
	IplImage* pRImage = cvCreateImage(pImgSize, IPL_DEPTH_8U, 1);
	IplImage* pGImage = cvCreateImage(pImgSize, IPL_DEPTH_8U, 1);
	IplImage *pBImage = cvCreateImage(pImgSize, IPL_DEPTH_8U, 1);
	IplImage *pBinary = cvCreateImage(pImgSize, IPL_DEPTH_8U, 1);
	IplImage *pRlt = cvCreateImage(pImgSize, IPL_DEPTH_8U, 1);
```
IplImage *xxx = cvCreateImage(xxx, IPL_DEPTH_8U, xxx);can be considered as a particular usage.All these codes are used to create a new image memory block and store the future results.

<font color=red>So,in this step we create a lot of empty arrays.They can be used for storage of the images which have been transformed.</font>

```cpp
BrightAdjust(pFrame0, pRawImg, 1, -120);
```
This function copys one image array(pFrame0) to another one(pRawImg) which also has three channels, but change how bright the picture is.Therefore,you can change the value of '-120' to adjust the brightness.For example:
<center>
{% qnimg 3.jpg %}
</center>

```cpp
cvSplit(pRawImg, pBImage, pGImage, pRImage, NULL);
```
Then we use 'cvSplit' to spilt the image into four channels,the first parameter represents the image,and the next three parameter represent the B,G,R channel.I do not know which channel the fourth parameter represents.The default setting of it is 'NULL'.

```cpp
GetDiffImage(pRImage, pGImage, pBinary, 25);
cvErode(pBinary, pGrayImage, NULL, 1);
cvDilate(pGrayImage, pRlt, NULL, 3);
```

'GetDiffImage' compare the R channel with the G channel.The place where the difference is larger than 25 will be setted as 'white' and the rest will be setted as 'black':

<center>
{% qnimg 4.jpg %}
</center>


'cvErode' and 'cvDilate' are functions to erode and dilate the pictures:

<center>
{% qnimg 5.jpg %}
</center>

```cpp
cvFindContours(pRlt, pStorage, &pContour, sizeof(CvContour), CV_RETR_CCOMP, CV_CHAIN_APPROX_SIMPLE);
```
This function is to find contours in a binary image.[<font color=cyan>More information can be seen here</font>](http://docs.opencv.org/2.4/modules/imgproc/doc/structural_analysis_and_shape_descriptors.html).

```cpp
for (; pContour != NULL; pContour = pContour->h_next)
```
The difference of <font color=GreenYellow >h_next</font> and <font color=GreenYellow >v_next</font> [<font color=cyan>can be seen here</font>](http://jmpelletier.com/a-simple-opencv-tutorial/).

<center>
{% qnimg Contours.gif %}
</center>

```cpp
CvSeq *pContour = NULL;
if (pContour->total > 10)
```

Total is one of the methods of 'CvSeq'.It represents the total number of elements.


## <font color=yellowish><center>The End</center> ##
