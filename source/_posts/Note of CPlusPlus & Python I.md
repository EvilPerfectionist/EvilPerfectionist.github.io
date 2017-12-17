---
title: Note of CPlusPlus & Python I
date: 2017/12/11 10:21:00
categories:
- OpenCV
- C++
tags:
- OpenCV
- C++
thumbnail: "http://ox8ixvjau.bkt.clouddn.com/blog/171211/ecHEm8cK5l.jpg"
---

## <font color=#0099ff><center> Preface </center></font> ##


This post includes some simple examples of the functions I meet while learning Color Spaces. You can see the post [<font color=cyan>here.</font>](https://evilperfectionist.github.io/ColorSpaces/)


## <font color=red><center> Content </center></font> ##



#### <Strong><font color=tomato>Function Sprintf </font></strong>

```cpp
#include <stdio.h>
int main ()
{
  char buffer [50];
  int n, a=5, b=3;
  n=sprintf (buffer, "%d plus %d is %d", a, b, a+b);
  printf ("[%s] is a string %d chars long\n",buffer,n);
  return 0;
}
```
The output is: [5 plus 3 is 8] is a string 13 chars long

So what the function does is to write the content of the second parameter into the string(first parameter).

#### <Strong><font color=tomato>Method sort() </font></strong>

```python
a = [5, 2, 3, 1, 4]
a.sort()
print (a)
```
The output is [1, 2, 3, 4, 5].

#### <Strong><font color=tomato>Method plt.hist2d </font></strong>

```python
from matplotlib.colors import LogNorm
import matplotlib.pyplot as plt
x = [1, 2, 3, 2]
y = [1, 2, 3, 2]
plt.hist2d(x, y, bins=40, norm=LogNorm())
plt.colorbar()
plt.show()
```
The result:

<center>
![mark](http://ox8ixvjau.bkt.clouddn.com/blog/171211/dL2j5cmlKJ.png?imageslim)
</center>

```python
from matplotlib.colors import LogNorm
import matplotlib.pyplot as plt
import numpy as np
x = np.random.randn(100000)
y = np.random.randn(100000) + 10
plt.hist2d(x, y, bins=40, norm=LogNorm())
plt.colorbar()
plt.show()
```
The result:

<center>
![mark](http://ox8ixvjau.bkt.clouddn.com/blog/171211/i8D2lhjh3e.png?imageslim)
</center>

In this case, the parameter bins stands for the number of bins for the two dimensions. The parameter norm=LogNorm() means that the number in the colorbar in the right will be calculated in logarithm. In other words, the data was normalized via log10.

#### <Strong><font color=tomato>Syntax if not </font></strong>

```python
a = 0
x = [4, 5, 6]
if not a:
    x = [1, 2, 3]
print(x)
```
The result will be [1, 2, 3]

```python
a = 1
x = [4, 5, 6]
if not a:
    x = [1, 2, 3]
print(x)
```
The result will be [4, 5, 6]

#### <Strong><font color=tomato>Method plt.plot </font></strong>

```python
import matplotlib.pyplot as plt
plt.plot([1,2,3,4])
plt.ylabel('some numbers')
plt.show()
```
![mark](http://ox8ixvjau.bkt.clouddn.com/blog/171212/cFDBBGfB82.png?imageslim)

You may be wondering why the x-axis ranges from 0-3 and the y-axis from 1-4. If you provide a single list or array to the plot() command, matplotlib assumes it is a sequence of y values, and automatically generates the x values for you. Since python ranges start with 0, the default x vector has the same length as y but starts with 0. Hence the x data are [0,1,2,3].
```python
import matplotlib.pyplot as plt
plt.plot([1, 2, 3, 4], [1, 4, 9, 16])
plt.ylabel('some numbers')
plt.show()
```
![mark](http://ox8ixvjau.bkt.clouddn.com/blog/171212/JEIJBGIJja.png?imageslim)

If you provide two arrays which are corresponding with each other. It will plot a broken line according to the points.

#### <Strong><font color=tomato>Method reshape() </font></strong>

```python
a = np.arange(6).reshape((3, 2))
```
The result will be:

![mark](http://ox8ixvjau.bkt.clouddn.com/blog/171216/3H659kd7H0.png?imageslim)

What I want to mention is that one of the parameters can be set as negative such as -1. Let us see an example.
```python
import numpy as np

a = np.array([[1,2,3], [4,5,6]]) # the unspecified value is inferred to be 2
b = np.reshape(a, (3,-1))
print(b)
```
The result will be:

![mark](http://ox8ixvjau.bkt.clouddn.com/blog/171216/Jd8hG38ll6.png?imageslim)

-1 here means following. We know that a has 6 numbers, and we set the reshape row as 3, so the unspecified value is inferred to be 2. If a has 9 numbers, and we also set the reshape row as 3 and reshape column as -1 , the unspecified value is inferred to be 3.

#### <Strong><font color=tomato> numpy.random.randint </font></strong>

This function returns random integers from low (inclusive) to high (exclusive).
```python
import numpy as np

x = np.random.randint(25,100,25)
y = np.random.randint(175,255,10)

X = np.random.randint(25,50,(5,2))
Y = np.random.randint(60,85,(3,2))

print(x)
print(y)
print(X)
print(Y)
```
The result will be:

```python
[31 69 70 86 88 74 45 56 26 56 81 30 39 78 86 25 89 30 95 44 53 90 56 76 83]
[214 195 196 219 176 251 183 251 201 189]
[[45 29]
 [39 36]
 [27 49]
 [49 32]
 [34 33]]
[[64 78]
 [78 75]
 [63 83]]
```
#### <Strong><font color=tomato> numpy.hstack </font></strong>
This function takes a sequence of arrays and stack them horizontally to make a single array.
```python
import numpy as np

a = np.array((1,2,3))
b = np.array((2,3,4))
c = np.hstack((a,b))

x = np.array([[1],[2],[3]])
y = np.array([[2],[3],[4]])
z = np.hstack((x,y))

print(c)
print(z)
```
The result will be:
```python
[1 2 3 2 3 4]
[[1 2]
 [2 3]
 [3 4]]
```
#### <Strong><font color=tomato> numpy.vstack </font></strong>
This function takes a sequence of arrays and stack them vertically to make a single array.
```python
import numpy as np

a = np.array((1,2,3))
b = np.array((2,3,4))
c = np.vstack((a,b))

x = np.array([[1],[2],[3]])
y = np.array([[2],[3],[4]])
z = np.vstack((x,y))

X = np.random.randint(25,50,(3,2))
Y = np.random.randint(60,85,(3,2))
Z = np.vstack((X,Y))

print(c)
print(z)
print(Z)
```
The result will be:
```python
[[1 2 3]
 [2 3 4]]
[[1]
 [2]
 [3]
 [2]
 [3]
 [4]]
[[30 42]
 [30 25]
 [33 32]
 [74 64]
 [69 64]
 [67 61]]
```
#### <Strong><font color=tomato> numpy.ravel </font></strong>
This function returns a contiguous flattened array.
```python
import numpy as np
x = np.array([[1, 2, 3], [4, 5, 6]])
print(np.ravel(x))
```
The result will be:
```python
[1 2 3 4 5 6]
```
#### <Strong><font color=tomato> Array Calculation </font></strong>
```python
import numpy as np

z = np.array([[1, 2], [3, 4], [5, 6]])
print(z)
print(z[0])
print(z[[0]])
print(z[[[0]]])
print(z[[[[0]]]])
print(z[[[[[0]]]]])

x = np.arange(3)
print(x)
print(x[0])
print(x[[0]])
print(x[[[0]]])
print(x[[[[0]]]])
print(x[[[[[0]]]]])
print(x[[True, True, False]])

label =  np.array([0, 0, 1])
labels =  np.array([[0], [0], [1]])
a = z[label==0]
b = z[labels.ravel()==0]
x = x.reshape((3, 1))
c = x[label==0]
d = x[labels==0]
print([0]==[0])
print(label)
print(labels)
print(a)
print(b)
print(c)
print(d)
print(label==0)
print(labels==0)
```
The result will be:
```python
[[1 2]
 [3 4]
 [5 6]]
[1 2]
[[1 2]]
[[1 2]]
[[[1 2]]]
[[[[1 2]]]]
[0 1 2]
0
[0]
[0]
[[0]]
[[[0]]]
[0 1]
True
[0 0 1]
[[0]
 [0]
 [1]]
[[1 2]
 [3 4]]
[[1 2]
 [3 4]]
[[0]
 [1]]
[0 1]
[ True  True False]
[[ True]
 [ True]
 [False]]
```

## <font color=yellowish><center>The End</center> ##
