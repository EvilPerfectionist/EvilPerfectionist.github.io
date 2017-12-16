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
