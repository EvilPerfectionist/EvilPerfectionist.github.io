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

Then we will split data into training (90%) and test set (10%):
```python
train_n=int(0.9*len(hog_descriptors))
digits_train, digits_test = np.split(digits_deskewed, [train_n])
hog_descriptors_train, hog_descriptors_test = np.split(hog_descriptors, [train_n])
labels_train, labels_test = np.split(labels, [train_n])
```

After that we will use the generated HOG vectors to train our SVM algorithm.
```python
def svmInit(C=12.5, gamma=0.50625):
  model = cv2.ml.SVM_create()
  model.setGamma(gamma)
  model.setC(C)
  model.setKernel(cv2.ml.SVM_RBF)
  model.setType(cv2.ml.SVM_C_SVC)
  return model
def svmTrain(model, samples, responses):
  model.train(samples, cv2.ml.ROW_SAMPLE, responses)
  return model
model = svmInit()
svmTrain(model, hog_descriptors_train, labels_train)
```
After training our algorithm successfully, we will evaluate our SVM algorithm.
```python
def svmPredict(model, samples):
  return model.predict(samples)[1].ravel()
def svmEvaluate(model, digits, samples, labels):
    predictions = svmPredict(model, samples)
    accuracy = (labels == predictions).mean()
    print('Percentage Accuracy: %.2f %%' % (accuracy*100))

    confusion = np.zeros((10, 10), np.int32)
    for i, j in zip(labels, predictions):
        confusion[int(i), int(j)] += 1
    print('confusion matrix:')
    print(confusion)

    vis = []
    for img, flag in zip(digits, predictions == labels):
        img = cv2.cvtColor(img, cv2.COLOR_GRAY2BGR)
        if not flag:
            img[...,:2] = 0

        vis.append(img)
    return mosaic(25, vis)
vis = svmEvaluate(model, digits_test, hog_descriptors_test, labels_test)
```
I use print(model.predict(samples)[1]) to know that the return values are the prediction of the test data labels. So then we can use (labels == predictions) to know whether the prediction result is right or wrong and then calculate its accuracy.

You may be curious that which digits the algorithm failed to recognize correctly. So we build a matrix called confusion. Then we add numbers according to the coordinates(labels, predictions). If all the digits are recognized correctly, then the numbers will only be added on diagonal line since the values of labels are the same to predictions. Besides, we can also know how the number was mistakenly recognized, such as 1->7, 8->5. Below is the example.
```python
[[52  0  0  0  0  0  0  0  0  0]
 [ 0 43  2  0  0  0  0  0  0  0]
 [ 0  0 61  0  0  0  0  1  0  0]
 [ 0  0  0 49  0  0  0  0  0  0]
 [ 0  0  0  0 44  0  0  0  0  0]
 [ 0  0  0  0  0 49  0  0  0  0]
 [ 0  0  0  0  0  0 46  0  0  0]
 [ 0  0  0  0  0  0  0 50  1  0]
 [ 0  0  0  0  0  1  0  0 50  0]
 [ 0  0  0  0  0  0  0  0  0 51]]
```
As we can see in the matrix, there are 5 numbers are recognized incorrectly. There are two 1 numbers are recognized as 2, one 2 number as 7, one 7 number as 8, one 8 number as 5.

Then we print the wrong numbers in red using this block of codes:
```python
vis = []
for img, flag in zip(digits, predictions == labels):
    img = cv2.cvtColor(img, cv2.COLOR_GRAY2BGR)
    if not flag:
        img[...,:2] = 0

    vis.append(img)
return mosaic(25, vis)
```
The result will be:

![mark](http://ox8ixvjau.bkt.clouddn.com/blog/171219/DKLLf0D9D6.jpg?imageslim)

If you change the mosaic(25, vis) to mosaic(20, vis), the result will be the image below since the function mosaic makes a grid from images and the first parameter stands for the number of grid columns.

![mark](http://ox8ixvjau.bkt.clouddn.com/blog/171219/mECBg02E2J.jpg?imageslim)
