---
title: LBP LTP LATP
date: 2017/12/13 10:21:00
categories:
- OpenCV
tags:
- OpenCV
thumbnail: "http://ox8ixvjau.bkt.clouddn.com/blog/171213/li5Gj4bgdk.jpg"
---

## <font color=#0099ff><center> Preface </center></font> ##

<font face="微软雅黑">

Local binary patterns (LBP) is a type of visual descriptor used for classification in computer vision. Local ternary patterns (LTP) and LATP are an extension of Local binary patterns (LBP). In this post, I would like to introduce these feature extraction methods and give an example for texture matching [<font color=cyan>following this link.</font>](http://hanzratech.in/2015/05/30/local-binary-patterns.html) For my task, I plan to use them along with color features for the next step.

</font>

## <font color=red><center> How to Calculate </center></font> ##


### <font color= #ff3399 >LBP:</font> ###
The method is very easy to understand. You can learn how to calculate the values of LBP in detail from the link I give you at the preface. If you are not interested in the principle. You can only see the image and code below.

![mark](http://ox8ixvjau.bkt.clouddn.com/blog/171212/iBLHIB5hm4.png?imageslim)

```cpp
void ComputeLBP(IplImage *img, IplImage *LBPImage){
	IplImage* GrayImage = cvCreateImage(cvGetSize(img), 8, 1);
	cvCvtColor(img, GrayImage, CV_BGR2GRAY);
	int center=0;
	int center_lbp=0;
	for (	 int row=1; row < GrayImage->height-1; row++){
		for (int col=1; col < GrayImage->width -1; col++){
			center	    = cvGetReal2D(GrayImage, row  , col   );
			center_lbp  = 0;
			if (center <= cvGetReal2D(GrayImage, row-1, col-1))
				center_lbp += 1 ;
			if (center <= cvGetReal2D(GrayImage, row-1, col  ))
				center_lbp += 2 ;
			if (center <= cvGetReal2D(GrayImage, row-1, col+1))
				center_lbp += 4 ;
			if (center <= cvGetReal2D(GrayImage, row  , col-1))
				center_lbp += 8 ;
			if (center <= cvGetReal2D(GrayImage, row  , col+1))
				center_lbp += 16;
			if (center <= cvGetReal2D(GrayImage, row+1, col-1))
				center_lbp += 32;
			if (center <= cvGetReal2D(GrayImage, row+1, col  ))
				center_lbp += 64;
			if (center <= cvGetReal2D(GrayImage, row+1, col+1))
				center_lbp += 128;
			cvSetReal2D( LBPImage, row, col, center_lbp );
		}
	}
}
```

### <font color= #ff3399 >LTP:</font> ###
The formula of LTP is a little more complex than the LBP. LTP method adds a new parameter k. And the value of pixel can be set as 1, 0, -1 instead of 1, 0.

![mark](http://ox8ixvjau.bkt.clouddn.com/blog/171212/54Ied0Fdkb.png?imageslim)

```cpp
void ComputeLTP(IplImage *img, IplImage *LTPImage_p, IplImage *LTPImage_n, int K){
	IplImage* GrayImage = cvCreateImage(cvGetSize(img), 8, 1);
	cvCvtColor(img, GrayImage, CV_BGR2GRAY);
	int center=0;
	int center_ltp_p=0;
	int center_ltp_n=0;
	for (	 int row=1; row < GrayImage->height-1; row++){
		for (int col=1; col < GrayImage->width -1; col++){
			center		= cvGetReal2D(GrayImage, row, col);
			center_ltp_n= 0;
			if (center >= cvGetReal2D(GrayImage, row-1, col-1)+K)
				center_ltp_n += 1 ;
			if (center >= cvGetReal2D(GrayImage, row-1, col  )+K)
				center_ltp_n += 2 ;
			if (center >= cvGetReal2D(GrayImage, row-1, col+1)+K)
				center_ltp_n += 4 ;
			if (center >= cvGetReal2D(GrayImage, row  , col-1)+K)
				center_ltp_n += 8 ;
			if (center >= cvGetReal2D(GrayImage, row  , col+1)+K)
				center_ltp_n += 16;
			if (center >= cvGetReal2D(GrayImage, row+1, col-1)+K)
				center_ltp_n += 32;
			if (center >= cvGetReal2D(GrayImage, row+1, col  )+K)
				center_ltp_n += 64;
			if (center >= cvGetReal2D(GrayImage, row+1, col+1)+K)
				center_ltp_n += 128;
			cvSetReal2D(LTPImage_n, row, col, center_ltp_n);//正值矩阵输入

			center_ltp_p= 0;
			if (center <= cvGetReal2D(GrayImage, row-1, col-1)-K)
				center_ltp_p += 1 ;
			if (center <= cvGetReal2D(GrayImage, row-1, col  )-K)
				center_ltp_p += 2 ;
			if (center <= cvGetReal2D(GrayImage, row-1, col+1)-K)
				center_ltp_p += 4 ;
			if (center <= cvGetReal2D(GrayImage, row  , col-1)-K)
				center_ltp_p += 8 ;
			if (center <= cvGetReal2D(GrayImage, row  , col+1)-K)
				center_ltp_p += 16;
			if (center <= cvGetReal2D(GrayImage, row+1, col-1)-K)
				center_ltp_p += 32;
			if (center <= cvGetReal2D(GrayImage, row+1, col  )-K)
				center_ltp_p += 64;
			if (center <= cvGetReal2D(GrayImage, row+1, col+1)-K)
				center_ltp_p += 128;
			cvSetReal2D(LTPImage_p, row, col, center_ltp_p);//负值矩阵输入
		}
	}
}
```

### <font color= #ff3399 >LATP:</font> ###
The formula of LATP is the most complicated. The advantages of LATP method is that the value depends on mean and variance of the eight neighbourhood pixels and is not associated with the center pixel.

![mark](http://ox8ixvjau.bkt.clouddn.com/blog/171212/DmgdIClH9C.png?imageslim)

```cpp
void ComputeLATP(IplImage *img, IplImage *LATPImage_p, IplImage *LATPImage_n, int K){
	 IplImage* GrayImage = cvCreateImage(cvGetSize(img), 8, 1);
	 cvCvtColor(img, GrayImage, CV_BGR2GRAY);
	 int center = 0;
	 int center_latp_p = 0;
	 int center_latp_n = 0;
	 int c1 = 0;
	 int c2 = 0;
	 int c3 = 0;
	 int c4 = 0;
	 int c5 = 0;
	 int c6 = 0;
	 int c7 = 0;
	 int c8 = 0;
	 double u = 0;
	 double q = 0;
	 for (int row=1; row < GrayImage->height-1; row++){
		 for (int col=1; col < GrayImage->width-1; col++)
		 {
			 center = cvGetReal2D(GrayImage, row, col);
			 c1 = cvGetReal2D(GrayImage, row-1, col-1);
			 c2 = cvGetReal2D(GrayImage, row-1, col);
			 c3 = cvGetReal2D(GrayImage, row-1, col+1);
			 c4 = cvGetReal2D(GrayImage, row, col-1);
			 c5 = cvGetReal2D(GrayImage, row, col+1);
			 c6 = cvGetReal2D(GrayImage, row+1, col-1);
			 c7 = cvGetReal2D(GrayImage, row+1, col);
			 c8 = cvGetReal2D(GrayImage, row+1, col+1);

			 u  = (c1+c2+c3+c4+c5+c6+c7+c8)/8;
			 q  = sqrt((c1-u)*(c1-u)+(c2-u)*(c2-u)+(c3-u)*(c3-u)+(c4-u)*(c4-u)+(c5-u)*(c5-u)+(c6-u)*(c6-u)+(c7-u)*(c7-u)+(c8-u)*(c8-u));
			 center=(int)u-K*(int)q;
			 center_latp_p = 0;
			 if (center >= cvGetReal2D(GrayImage, row-1, col-1))
				 center_latp_p += 1;
			 if (center >= cvGetReal2D(GrayImage, row-1, col  ))
				 center_latp_p += 2;
			 if (center >= cvGetReal2D(GrayImage, row-1, col+1))
				 center_latp_p += 4;
			 if (center >= cvGetReal2D(GrayImage, row  , col-1))
				 center_latp_p += 8;
			 if (center >= cvGetReal2D(GrayImage, row  , col+1))
				 center_latp_p += 16;
			 if (center >= cvGetReal2D(GrayImage, row+1, col-1))
				 center_latp_p += 32;
			 if (center >= cvGetReal2D(GrayImage, row+1, col  ))
				 center_latp_p += 64;
			 if (center >= cvGetReal2D(GrayImage, row+1, col+1))
				 center_latp_p += 128;
			 cvSetReal2D(LATPImage_p, row, col, center_latp_p);//正值矩阵输入

			 center=(int)u+K*(int)q;
			 center_latp_n = 0;
			 if (center <= cvGetReal2D(GrayImage, row-1, col-1))
				 center_latp_n += 1;
			 if (center <= cvGetReal2D(GrayImage, row-1, col  ))
				 center_latp_n += 2;
			 if (center <= cvGetReal2D(GrayImage, row-1, col+1))
				 center_latp_n += 4;
			 if (center <= cvGetReal2D(GrayImage, row  , col-1))
				 center_latp_n += 8;
			 if (center <= cvGetReal2D(GrayImage, row  , col+1))
				 center_latp_n += 16;
			 if (center <= cvGetReal2D(GrayImage, row+1, col-1))
				 center_latp_n += 32;
			 if (center <= cvGetReal2D(GrayImage, row+1, col  ))
				 center_latp_n += 64;
			 if (center <= cvGetReal2D(GrayImage, row+1, col+1))
				 center_latp_n += 128;
			 cvSetReal2D(LATPImage_n, row, col, center_latp_n);//负值矩阵输入
		 }
	 }
}
```

The function I wanna explain here is cvGetReal2D.
```cpp
C: double cvGetReal2D(const CvArr* arr, int idx0, int idx1)
```
GetReal?D returns a specific element of single-channel 1D, 2D, 3D or nD array. If the array has multiple channels, a runtime error is raised. Note that Get?D functions can be used safely for both single-channel and multiple-channel arrays though they are a bit slower. The parameters idx0 and idx1 stands for the position of the chosen element.
