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
<font size=4>

The output is: [5 plus 3 is 8] is a string 13 chars long

So what the function does is to write the content of the second parameter into the string(first parameter).

</font>
