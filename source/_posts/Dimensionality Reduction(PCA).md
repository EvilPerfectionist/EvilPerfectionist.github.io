---
title: Dimensionality Reduction PCA
date: 2017/12/16 10:21:00
categories:
- Machine Learning
tags:
- Machine Learning
thumbnail: "http://ox8ixvjau.bkt.clouddn.com/blog/171216/eg581EL6Ge.jpg"
---
## <font color=#0099ff><center> Preface </center></font> ##
Feature extraction is often used for image processing. However, some features are very similar to each other and it will slow down you calculation speed of your algorithm with so many features. Thus it is reasonable to implement Dimensionality Reduction for you features. Principle Components Analysis is a common method to deal with this.

## <font color=red><center> How to Implement PCA </center></font> ##
Andrew Ng gives a detailed description about PCA in his Machine Learning video. You can find this video both in coursera or [<font color=cyan>bilibili.</font>](https://www.bilibili.com/video/av9912938/?from=search&seid=8069802497216456143#page=84) So I will not explain this algorithm in detail here. The mathematical prove of this algorithm is not necessary for implementing it. So you can follow the instructions below and skip the mathematical prove.
### <font color= #ff3399 >1.Choose Samples</font> ###
The first thing you should do is choosing your samples and features. In image processing, the pixels can be selected as samples. The codes below can help you select your samples randomly.
```cpp
#define SAMPLE_NUMBER 100
IplImage* img = cvCreateImageHeader(size,IPL_DEPTH_8U,3);
img = cvLoadImage("1.jpg");
CvRNG rng = CvRNG( cvGetTickCount() );
int i, j, k, sample_count = SAMPLE_NUMBER;
k=0;
while (k < sample_count){												
	i = cvRandInt(&rng)%img->width;
	j = cvRandInt(&rng)%img->height;
}
```
CvRNG rng = CvRNG( cvGetTickCount() ); The function initializes a random number generator and returns the state.  The pointer to the state can be then passed to the cvRandInt, cvRandReal and cvRandArr functions. cvGetTickCount() is helper functions for RNG initialization and accurate time measurement: uses internal clock counter on x86.

unsigned cvRandInt	(	CvRNG * 	rng	) returns a 32-bit unsigned integer and updates RNG. It is similar to the rand() function from the C runtime library, except that OpenCV functions always generates a 32-bit random number, regardless of the platform.

So, with this block of codes, we get 100 random samples from the image.
### <font color= #ff3399 >2.Choose Features and Implement Mean Normalization</font> ###
Next, you should choose your features and implement mean normalization. In terms of image processing, it is common to choose colors and textures as features. Below is the codes:
```cpp
#define SAMPLE_NUMBER 100
int hold = 2;
int kernel = 3;
void ComputeLTP(IplImage *GrayImage, int i, int j,float s[8], int binp[8],int bino[8],int binn[8]){
	int u ;
	double q = 0;
	int   c[8];
	int p;
	for (int m = 0; m< kernel; m++){
		for (int n = 0; n< kernel; n++){
			u    = cvGetReal2D(GrayImage, j+3*(2*n-kernel+1)/2  , i+3*(2*m-kernel+1)/2  );
			c[0] = cvGetReal2D(GrayImage, j+3*(2*n-kernel+1)/2-1, i+3*(2*m-kernel+1)/2-1);
			c[1] = cvGetReal2D(GrayImage, j+3*(2*n-kernel+1)/2-1, i+3*(2*m-kernel+1)/2  );
			c[2] = cvGetReal2D(GrayImage, j+3*(2*n-kernel+1)/2-1, i+3*(2*m-kernel+1)/2+1);
			c[3] = cvGetReal2D(GrayImage, j+3*(2*n-kernel+1)/2  , i+3*(2*m-kernel+1)/2+1);
			c[4] = cvGetReal2D(GrayImage, j+3*(2*n-kernel+1)/2+1, i+3*(2*m-kernel+1)/2+1);
			c[5] = cvGetReal2D(GrayImage, j+3*(2*n-kernel+1)/2+1, i+3*(2*m-kernel+1)/2  );
			c[6] = cvGetReal2D(GrayImage, j+3*(2*n-kernel+1)/2+1, i+3*(2*m-kernel+1)/2-1);
			c[7] = cvGetReal2D(GrayImage, j+3*(2*n-kernel+1)/2  , i+3*(2*m-kernel+1)/2-1);
			for ( p = 0; p < 8; p++){
				if		( u <= (c[p] - hold ))
					binp[p]++;
				else if ( u >= (c[p] + hold ))
					binn[p]++;
				else
					bino[p]++;
			}
		}
	}
	for ( p = 0; p < 8; p++){
		if ( (binp[p] >= bino[p])&&(binp[p] >= binn[p]))
			s[p] = 0.6;
		else if ( (bino[p] > binp[p])&&(bino[p] > binn[p]))
			s[p] = 0.3;
		else
			s[p] = 0;
	}
}
IplImage* img = cvCreateImageHeader(size,IPL_DEPTH_8U,3);
img = cvLoadImage("1.jpg");
CvRNG rng = CvRNG( cvGetTickCount() );
int i, j, k, sample_count = SAMPLE_NUMBER;
CvMat* points   = cvCreateMat( sample_count,14, CV_32FC1);	//samples
CvMat* points2  = cvCreateMat( sample_count, 1, CV_32FC2);  //position information
k=0;
while (k < sample_count){												
	i = cvRandInt(&rng)%img->width;
	j = cvRandInt(&rng)%img->height;
  if (( i > 3*(kernel-1)/2) && ( j > 3*(kernel-1)/2) && ( i < 767-3*(kernel-1)/2) && ( j < 575-3*(kernel-1)/2)){
		((CvPoint2D32f*)points2->data.fl)[k].x = i;
		((CvPoint2D32f*)points2->data.fl)[k].y = j;
    CvScalar e[14];
    e[0].val[0]=(float)cvGet2D(hsv, j, i).val[0]/180;			//HSV
    e[1].val[0]=(float)cvGet2D(hsv, j, i).val[1]/256;
    e[2].val[0]=(float)cvGet2D(hsv, j, i).val[2]/256;
    e[3].val[0]=(float)cvGet2D(img, j, i).val[0]/256;			//RGB
    e[4].val[0]=(float)cvGet2D(img, j, i).val[1]/256;
    e[5].val[0]=(float)cvGet2D(img, j, i).val[2]/256;
    int binp[8] = {0}, bino[8] = {0}, binn[8] = {0};
    ComputeLTP(GrayImage, i, j, s, binp, bino, binn);
    for ( p = 0; p < 8; p++){
  	   e[p+6].val[0] = s[p];
    }
    for ( p = 0; p < 14; p++){
  	   cvSet2D( points, k, p, e[p]);
    }
    k++;
  }
}
```
There are two functions needed to be explained here. Besides, I would like to describe the meanings of some codes in order to help you understand better.
![mark](http://ox8ixvjau.bkt.clouddn.com/blog/171216/a26EiD46A2.png?imageslim)
CvMat* points = cvCreateMat( sample_count,14, CV_32FC1); In this case, the dimensions of the matrix will be 100x14 and the data type will be 32-bit float and one channel.

C: void cvSet2D(CvArr* arr, int idx0, int idx1, CvScalar value)  assigns the new value to a particular array element. In the case of a sparse array the functions create the node if it does not exist yet.

For each sample, there are 14 features, they can be divided into two parts ---- 6 for colors and 8 for textures. The 6 features for colors are easy to understand. I will explain the process of getting 8 texture features.
![mark](http://ox8ixvjau.bkt.clouddn.com/blog/171216/EmKjcE8igk.png?imageslim)
Because the kernel is set as 3, for an particular sample, we calculate 9 times within a 9x9 grid. For each 3x3 grid, we compare the value from c[0] to c[7] with the value of center and decide the corresponding value of which vector(binp(positive), binn(negative), bino(zero)) should be added one. Finally, we will allocate the values into s[] vector. For example, assuming that binp is [9, 2, 3, 6, 4, 6, 1, 7] which means the values of all the c[0] in each 3x3 grid are larger than the center values and there are two values of c[1] in all 3x3 grids are larger than the center values and so on. And binn can be [0, 3, 4, 1, 3, 2, 5, 0] and bino [0, 4, 2, 2, 2, 1, 3, 2]. Therefore, s can be [0.6, 0.3, 0, 0.6, 0.6, 0.6, 0, 0.6].

You are likely to be confused about this block of codes:
```cpp
if (( i > 3*(kernel-1)/2) && ( j > 3*(kernel-1)/2) && ( i < 767-3*(kernel-1)/2) && ( j < 575-3*(kernel-1)/2))
```
You can understand it with the image above. If the sample is too close to the edge, there are no pixels can be calculated.

The last thing I have to mention is that in order to implement mean normalization, 6 color values should be divided by a particular number. Thus, all the feature values are between [0-1].

### <font color= #ff3399 >3.Reducing Data From 14-dimensional to 10-dimensional</font> ###
Initially, you should use the samples with their features to calculate the covariance matrix and its eigenvectors with the formula and codes below.

![mark](http://ox8ixvjau.bkt.clouddn.com/blog/171216/01k788f3iA.png?imageslim)
![mark](http://ox8ixvjau.bkt.clouddn.com/blog/171216/9gEHgEkcfe.png?imageslim)

```cpp
CvMat* pMean = cvCreateMat(1, 14, CV_32FC1);
CvMat* pEigVals = cvCreateMat(1, 14, CV_32FC1);
CvMat* pEigVecs = cvCreateMat( 14, 14, CV_32FC1);
cvCalcPCA( points, pMean, pEigVals, pEigVecs, CV_PCA_DATA_AS_ROW );
```
U is the eigenvector which we want to obtain. In this case, pEigVecs is the matrix we need. And its dimensions are 14x14.

Then you can use cvProjectPCA transform the 14-dimensional one to 10-dimensional.
```cpp
CvMat* pResult = cvCreateMat( SAMPLE_NUMBER, 10, CV_32FC1 );
cvProjectPCA( points, pMean, pEigVecs, pResult );
```
Finally, you can get the samples with 10 features rather than 14 features.
## <font color=yellowish><center>The End</center> ##
