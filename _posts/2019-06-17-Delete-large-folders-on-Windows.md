---
layout: post
title:  "Deleting large folders on Windows"
date:   2019-06-17
categories: life-saver
tags: dos
---

* content
{:toc}

I was updating my backup drive and needed to delete some huge folders on that drive. Deleting large folders in Windows explorer could take quite a while because Windows would first calculate the folder's size, which is time-consuming. Therefore, it would be great to know how to quickly delete huge folders on Windows.



## Solution
Suppose the large folder to be deleted is called `foldername`.

1. Open cmd.
2. Type `cd C:\backup\foldername` to navigate to the folder to be deleted.
3. Type `del /f/q/s *.* > nul` to delete all files in `foldername`, while leaving the folder structure intact (for now). 
    
    Explanation of this command can be found in the [Microsoft documentation](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/del).
4. Type `cd ..` to navigate to the parent folder of `foldername`.
5. Type `rmdir /q/s foldername` to delete `foldername` along with its subfolders. 

    Explanation of this command can be found in the [Microsoft documentation](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/rmdir).