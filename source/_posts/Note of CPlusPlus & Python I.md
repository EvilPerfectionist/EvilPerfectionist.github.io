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


## <font color=yellowish><center>The End</center> ##
