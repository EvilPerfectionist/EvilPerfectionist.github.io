---
title: Kmeans
date: 2017/12/16 10:21:00
categories:
- Machine Learning
tags:
- Machine Learning
thumbnail: "http://ox8ixvjau.bkt.clouddn.com/blog/171216/5cKiLk40Ie.jpg"
---
## <font color=#0099ff><center> Preface </center></font> ##
Kmeans is an unsupervised learning algorithm. This algorithm can classify the same data into different groups. At the beginning, you should know how many groups you are willing to separate your data into. In this way, the number of clusters(centroids) can be determined. Then, we use the iteration method to find the reasonable positions of the centroids.

## <font color=red><center> The Principle of Kmeans </center></font> ##
Actually, the principle of Kmeans is very simple. You can either learn them by the video from Andrew Ng or search some tutorials and explanations like [<font color=cyan>this one</font>](https://mubaris.com/2017/10/01/kmeans-clustering-in-python/) and [<font color=cyan>this one.</font>](http://opencv-python-tutroals.readthedocs.io/en/latest/py_tutorials/py_ml/py_kmeans/py_kmeans_understanding/py_kmeans_understanding.html)
## <font color=#ff3399><center> Descriptions of Some Examples in Detail </center></font> ##
### <font color= tomato>Color Quantization</font> ###
The codes is all from [<font color=cyan>this website:</font>](http://opencv-python-tutroals.readthedocs.io/en/latest/py_tutorials/py_ml/py_kmeans/py_kmeans_understanding/py_kmeans_understanding.html)
```python
import numpy as np
import cv2

img = cv2.imread('4.jpg')
Z = img.reshape((-1,3))

# convert to np.float32
Z = np.float32(Z)

# define criteria, number of clusters(K) and apply kmeans()
criteria = (cv2.TERM_CRITERIA_EPS + cv2.TERM_CRITERIA_MAX_ITER, 10, 1.0)
K = 2
ret,label,center=cv2.kmeans(Z,K,None,criteria,10,cv2.KMEANS_RANDOM_CENTERS)
print(center)
print(label)
# Now convert back into uint8, and make original image
center = np.uint8(center)
res = center[label.flatten()]
print(res)
res2 = res.reshape((img.shape))

cv2.imshow('res2',res2)
cv2.waitKey(0)
cv2.destroyAllWindows()
```
![mark](http://ox8ixvjau.bkt.clouddn.com/blog/171216/Che9HKh9jj.png?imageslim)
Let me do some explanations about the codes: There are 3 features, say, R,G,B. So we need to reshape the image to an array of Mx3 size (M is number of pixels in image). And after the clustering, we apply centroid values (it is also R,G,B) to all pixels, such that resulting image will have specified number of colors. And again we need to reshape it back to the shape of original image.

It is obvious that the function cv2.kmeans is very important, you can learn this function from the image below:

![mark](http://ox8ixvjau.bkt.clouddn.com/blog/171216/fDma385G2i.png?imageslim)

I use print function to let you better know what the output parameters include (Assuming that k=2):
```python
# The result of print(center)
[[  77.909729     77.34547424   83.28701019]
 [ 202.06881714  199.72377014  205.21600342]]
# The result of print(label)
[[0]
 [0]
 [0]
 ...,
 [1]
 [1]
 [1]]
# The result of print(res)
[[ 77  77  83]
 [ 77  77  83]
 [ 77  77  83]
 ...,
 [202 199 205]
 [202 199 205]
 [202 199 205]]
```
So the function returns the RGB values of the two centroids to parameter center.

flatten() returns a copy of the array collapsed into one dimension.
```cpp
a = np.array([[1,2], [3,4]])
```
The result will be array([1, 2, 3, 4]).

So, you should know why the result of print will be like this.
### <font color= tomato>Data with Only One Feature</font> ###
```python
import numpy as np
import cv2
from matplotlib import pyplot as plt

x = np.random.randint(25,100,25)
y = np.random.randint(175,255,25)
z = np.hstack((x,y))
z = z.reshape((50,1))
z = np.float32(z)
# Define criteria = ( type, max_iter = 10 , epsilon = 1.0 )
criteria = (cv2.TERM_CRITERIA_EPS + cv2.TERM_CRITERIA_MAX_ITER, 10, 1.0)

# Set flags (Just to avoid line break in the code)
flags = cv2.KMEANS_RANDOM_CENTERS

# Apply KMeans
compactness,labels,centers = cv2.kmeans(z,2,None,criteria,10,flags)

A = z[labels==0]
B = z[labels==1]
print(centers)
# Now plot 'A' in red, 'B' in blue, 'centers' in yellow
plt.hist(A,256,[0,256],color = 'r')
plt.hist(B,256,[0,256],color = 'b')
plt.hist(centers,32,[0,256],color = 'y')
plt.show()
```
The result will be:

![mark](http://ox8ixvjau.bkt.clouddn.com/blog/171217/9DejjKdg1m.png?imageslim)
```python
[[  64.76000214]
 [ 212.08000183]]
```

The detailed explanations about this block of code can be found [<font color=cyan>here.</font>](http://opencv-python-tutroals.readthedocs.io/en/latest/py_tutorials/py_ml/py_kmeans/py_kmeans_opencv/py_kmeans_opencv.html) There is one function I want to explain here---plt.hist().
```python
numpy.histogram(a, bins=10, range=None, normed=False, weights=None, density=None)
```
A, B, centers are the parameter a, which stand for the data we want to plot.

256, 256, 32 are the parameter bins, which means the x axis is divided in 256, 256, 32 boxes.

The range is from 0 to 256.
### <font color= tomato>Data with Multiple Features</font> ###
```python
import numpy as np
import cv2
from matplotlib import pyplot as plt

X = np.random.randint(25,50,(25,2))
Y = np.random.randint(60,85,(25,2))
Z = np.vstack((X,Y))

# convert to np.float32
Z = np.float32(Z)

# define criteria and apply kmeans()
criteria = (cv2.TERM_CRITERIA_EPS + cv2.TERM_CRITERIA_MAX_ITER, 10, 1.0)
ret,label,center=cv2.kmeans(Z,2,None,criteria,10,cv2.KMEANS_RANDOM_CENTERS)
print(label.ravel())
# Now separate the data, Note the flatten()
A = Z[label.ravel()==0]
B = Z[label.ravel()==1]
print(A)
print(B)
# Plot the data
plt.scatter(A[:,0],A[:,1])
plt.scatter(B[:,0],B[:,1],c = 'r')
plt.scatter(center[:,0],center[:,1],s = 80,c = 'y', marker = 's')
plt.xlabel('Height'),plt.ylabel('Weight')
plt.show()
```
Below is the result:
![mark](http://ox8ixvjau.bkt.clouddn.com/blog/171217/mjdg47LBJj.png?imageslim)
There is one function I want to explain here---plt.scatter().

A[:,0], B[:,0], center[:,0] are values of x.
A[:,1], B[:,1], center[:,1] are values of y.
s=80 mean the size of the point is 80.

### <font color= tomato>A Example Using Kmeans with C</font> ###
The codes are from a book called Learning *OpenCV_ Computer Vision with the OpenCV Library*.
```cpp
#include "cv.h"
#include "highgui.h"
#include <iostream>
#include <cmath>
#include "cxcore.h"

void main(int argc, char** argv)
{
#define MAX_CLUSTERS 5
	CvScalar color_tab[MAX_CLUSTERS];
	IplImage* img = cvCreateImage(cvSize(500, 500), 8, 3);
	CvRNG rng = cvRNG(0xffffffff);
	color_tab[0] = CV_RGB(255, 0, 0);
	color_tab[1] = CV_RGB(0, 255, 0);
	color_tab[2] = CV_RGB(100, 100, 255);
	color_tab[3] = CV_RGB(255, 0, 255);
	color_tab[4] = CV_RGB(255, 255, 0);
	cvNamedWindow("clusters", 1);
	for (;;)
	{
		int k, cluster_count = cvRandInt(&rng) % MAX_CLUSTERS + 1;
		int i, sample_count = cvRandInt(&rng) % 1000 + 1;
		CvMat* points = cvCreateMat(sample_count, 1, CV_32FC2);
		CvMat* clusters = cvCreateMat(sample_count, 1, CV_32SC1);
		/* generate random sample from multivariate
		Gaussian distribution */
		for (k = 0; k < cluster_count; k++)
		{
			CvPoint center;
			CvMat point_chunk;
			center.x = cvRandInt(&rng) % img->width;
			center.y = cvRandInt(&rng) % img->height;
			cvGetRows(points, &point_chunk,
				k*sample_count / cluster_count,
				k == cluster_count - 1 ? sample_count :
				(k + 1)*sample_count / cluster_count);
			cvRandArr(&rng, &point_chunk, CV_RAND_NORMAL,
				cvScalar(center.x, center.y, 0, 0),
				cvScalar(img->width / 6, img->height / 6, 0, 0));
		}
		/* shuffle samples */
		for (i = 0; i < sample_count / 2; i++)
		{
			CvPoint2D32f* pt1 = (CvPoint2D32f*)points->data.fl +
				cvRandInt(&rng) % sample_count;
			CvPoint2D32f* pt2 = (CvPoint2D32f*)points->data.fl +
				cvRandInt(&rng) % sample_count;
			CvPoint2D32f temp;
			CV_SWAP(*pt1, *pt2, temp);
		}
		cvKMeans2(points, cluster_count, clusters,
			cvTermCriteria(CV_TERMCRIT_EPS + CV_TERMCRIT_ITER,
				10, 1.0));
		cvZero(img);
		for (i = 0; i < sample_count; i++)
		{
			CvPoint2D32f pt = ((CvPoint2D32f*)points->data.fl)[i];
			int cluster_idx = clusters->data.i[i];
			cvCircle(img, cvPointFrom32f(pt), 2,
				color_tab[cluster_idx], CV_FILLED);
		}
		cvReleaseMat(&points);
		cvReleaseMat(&clusters);
		cvShowImage("clusters", img);
		int key = cvWaitKey(0);
		if (key == 27) // ‘ESC’
			break;
	}
}
```
Let me do some explanations about the codes:

CvRNG rng = cvRNG(0xffffffff); RNG implements a random number generator, rng is a RNG element initialized with the value 0xFFFFFFFF

There are several lines of code using funtion CvScalar ().
```cpp
CvScalar color_tab[MAX_CLUSTERS];
cvScalar(center.x, center.y, 0, 0),
cvScalar(img->width / 6, img->height / 6, 0, 0));
```
In fact, CvScalar() is only a struct including a four length double Array.
```cpp
typedef struct CvScalar
{
    double val[4];

```
So, what is the purpose of using this funtion? Someone answers this question on Stackoverflow.

![mark](http://ox8ixvjau.bkt.clouddn.com/blog/171217/BBgciCFKfg.png?imageslim)

Fuction GetRow(s) returns array row or row span.
```cpp
cvGetRows(points, &point_chunk,
				k*sample_count / cluster_count,
				k == cluster_count - 1 ? sample_count :
				(k + 1)*sample_count / cluster_count);
```
The first parameter is an input array. We input our samples here.

The second parameter is a pointer to the resulting sub-array header. That means we will select a special part of points and input it into point_chunk.

The third parameter is the zero-based index of the starting row (inclusive) of the span and the fourth is the zero-based index of the ending row (exclusive) of the span. This means that in fact we separate the points into cluster_count(number) parts and get the fist address of each part and then give them to &point_chunk since k loops cluster_count(number) times.

Function cvRandArr fills an array with random numbers and updates the RNG state.
```cpp
cvRandArr(&rng, &point_chunk, CV_RAND_NORMAL,
				cvScalar(center.x, center.y, 0, 0),
				cvScalar(img->width / 6, img->height / 6, 0, 0));
```
The first parameter is a CvRNG state initialized by RNG().

The second parameter is the pointer of the destination array.

The third parameter stands for the distribution type. Here we choose CV_RAND_NORMAL which means normal or Gaussian distribution.

The fourth parameter, in the case of a normal distribution, it is the mean value of the random numbers.

The fifth parameter, in the case of a normal distribution, it is the standard deviation of the random numbers.

So, with the function cvGetRows, we gain cluster_count groups of data and with the function cvRandArr, we feed these data with random values, these values obey normal distribution. And they range in a rectangle whose four corner coordinates are (center.x-img->width / 6, center.y-img->height / 6), (center.x-img->width / 6, center.y+img->height / 6), (center.x+img->width / 6, center.y-img->height / 6), (center.x+img->width / 6, center.y+img->height / 6).

The codes below are used to shuffle samples:
```cpp
for (i = 0; i < sample_count / 2; i++)
		{
			CvPoint2D32f* pt1 = (CvPoint2D32f*)points->data.fl +
				cvRandInt(&rng) % sample_count;
			CvPoint2D32f* pt2 = (CvPoint2D32f*)points->data.fl +
				cvRandInt(&rng) % sample_count;
			CvPoint2D32f temp;
			CV_SWAP(*pt1, *pt2, temp);
		}
```
Let us look at the definition of the CV_SWAP first:
```cpp
#define CV_SWAP(a,b,t) ((t) = (a), (a) = (b), (b) = (t))
```
So, the temp is only used for transfer. We exchange the values of two random data using this block of code.

Then we use the codes below to draw some points on the image.
```cpp
for (i = 0; i < sample_count; i++)
		{
			CvPoint2D32f pt = ((CvPoint2D32f*)points->data.fl)[i];
			int cluster_idx = clusters->data.i[i];
			cvCircle(img, cvPointFrom32f(pt), 2,
				color_tab[cluster_idx], CV_FILLED);
		}
```
In this case, pt will be given a member of samples. In fact, we initiate points using CvMat* points = cvCreateMat(sample_count, 1, CV_32FC2); Here we can see that "points" is actually a pointer. It points to a sample. So we can give sample to pt using ((CvPoint2D32f*)points->data.fl)[i] since you can consider the content of a pointer as an array.

Then we use clusters->data.i[i] to obtain to index and draw the small colorful circle with cvCircle.

The parameter cvPointFrom32f(pt) means the enter of the circle and 2 is the radius of the circle.

## <font color=yellowish><center>The End</center> ##
