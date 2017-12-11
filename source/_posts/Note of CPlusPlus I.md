---
title: Note of CPlusPlus I
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

<font size=4>

This post includes some simple examples of the functions I meet while learning Color Spaces. You can see the post [<font color=cyan>here.</font>](https://evilperfectionist.github.io/ColorSpaces/)

</font>

## <font color=red><center> Content </center></font> ##

<font size=4>

<Strong><font color=tomato>Function Sprintf </font></strong>

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

<Strong><font color=tomato>Method sort() </font></strong>

```python
a = [5, 2, 3, 1, 4]
a.sort()
print (a)
```
The output is [1, 2, 3, 4, 5].

<Strong><font color=tomato>Method plt.hist2d </font></strong>

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

<Strong><font color=tomato>Syntax if not </font></strong>

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
</font>

## <font color=yellowish><center>The End</center> ##
