---
layout: post
title:  "Solving Qt plugin error when calling matplotlib.pyplot"
date:   2019-06-18
categories: life-saver
tags: python
---

* content
{:toc}

I recently installed Anaconda 3 with Python 3.6.8. Calling `matplotlib.pylot` raises the error
```
This application failed to start because it could not find or load the Qt platform plugin "windows" in "" 
Reinstalling the application may fix this problem. 
```



## Solution

The solution that worked for me was to type
```
pip install pyqt5
```
in Anaconda prompt.