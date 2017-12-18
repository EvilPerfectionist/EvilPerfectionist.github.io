---
title: Support Vector Machine Algorithm (SVM)
date: 2017/12/18 10:21:00
categories:
- Machine Learning
- OpenCV
tags:
- Machine Learning
- OpenCV
thumbnail: "http://ox8ixvjau.bkt.clouddn.com/blog/171218/BFgd2ijDJ2.jpg"
---

## <font color=#0099ff><center> Preface </center></font> ##
Support Vector Machine is a Machine Learning algorithm.

## <font color=red><center> Optical Character Recognition (OCR) with SVM and HOG </center></font> ##
I follow the instructions from [<font color=cyan>this post.</font>](https://www.learnopencv.com/handwritten-digits-classification-an-opencv-c-python-tutorial/) You can download the codes from his github account. In order to recognize handwritten digits, you should have large training samples, may be thousands. So the author makes a useful image. It contains 5000 images in all — 500 images of each digit. Each image is 20×20 grayscale with a black background.

![mark](http://ox8ixvjau.bkt.clouddn.com/blog/171218/fhj120DH07.png?imageslim)
So the first step we should do is to load the image and use 4500 of these digits for training and use the remaining 500 for testing the performance of the algorithm.
```python
SZ = 20
CLASS_N = 10
def split2d(img, cell_size, flatten=True):
    h, w = img.shape[:2]
    sx, sy = cell_size
    cells = [np.hsplit(row, w//sx) for row in np.vsplit(img, h//sy)]
    cells = np.array(cells)
    if flatten:
        cells = cells.reshape(-1, sy, sx)
    return cells

def load_digits(fn):
    digits_img = cv2.imread(fn, 0)
    digits = split2d(digits_img, (SZ, SZ))
    labels = np.repeat(np.arange(CLASS_N), len(digits)/CLASS_N)
    return digits, labels
digits, labels = load_digits('digits.png')
```
The result of print(img.shape) will be (1000, 2000).
```cpp
a = [1,2,3,4,5]
b = a[:4]
print(b)
```
The result is [1,2,3,4], so h=1000, w=2000.


Therefore, this block of codes separates the picture into 20x20 small pictures. Then set label 0 on pictures including 0, label 1 on pictures including 1, label 2 on pictures including 2 etc.

The next step is to shuffle or permute the data.
```python
rand = np.random.RandomState(10)
shuffle = rand.permutation(len(digits))
digits, labels = digits[shuffle], labels[shuffle]
```

An obvious variation in writing among people is the slant of their writing. Some writers have a right or forward slant where the digits are slanted forward, some have a backward or left slant, and some have no slant at all. We can help the algorithm quite a bit by fixing this vertical slant so it does not have to learn this variation of the digits. So, we use the codes below to get the deskewed (fixed) version of digits.
```python
ef deskew(img):
    m = cv2.moments(img)
    if abs(m['mu02']) < 1e-2:
        return img.copy()
    skew = m['mu11']/m['mu02']
    M = np.float32([[1, skew, -0.5*SZ*skew], [0, 1, 0]])
    img = cv2.warpAffine(img, M, (SZ, SZ), flags=cv2.WARP_INVERSE_MAP | cv2.INTER_LINEAR)
    return img
digits_deskewed = list(map(deskew, digits))
```
The process of getting the deskewed digits is a little complex. Besides, there are some mathematical knowledge you should know. Therefore, I do not want to tell the detail here. Instead, it is enough that you just know the function of this block of code.

There is one function I wanna mention here---map(), in this case, all members of digits will be implemented with funtion deskew.

The next step is to calculate the Histogram of Oriented Gradients (HOG) descriptor.
```python
def get_hog() :
    winSize = (20,20)
    blockSize = (8,8)
    blockStride = (4,4)
    cellSize = (8,8)
    nbins = 9
    derivAperture = 1
    winSigma = -1.
    histogramNormType = 0
    L2HysThreshold = 0.2
    gammaCorrection = 1
    nlevels = 64
    signedGradient = True

    hog = cv2.HOGDescriptor(winSize,blockSize,blockStride,cellSize,nbins,derivAperture,winSigma,histogramNormType,L2HysThreshold,gammaCorrection,nlevels, signedGradient)

    return hog
hog = get_hog();
hog_descriptors = []
for img in digits_deskewed:
    hog_descriptors.append(hog.compute(img))
hog_descriptors = np.squeeze(hog_descriptors)
```
