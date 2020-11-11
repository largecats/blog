---
layout: post
title:  "Inserting images in blog post"
date:   2019-06-17
categories: blog life-saver
tags: markdown
---

* content
{:toc}

I want to insert an image in a blog post in a way that is consistent when viewing it locally and after pushing it to GitHub. This means I cannot use absolute path. Also, I do not want to use another online drive to store my pictures. This means I cannot use an url of the image. That leaves me with using relative path.



## Solution

1. Create a folder called "images" at the root of the local repository.
2. Put the image to be inserted there.
3. Use the format `![](/images/image-name.png){:width="800px"}` to insert image in the corresponding markdown file.
4. Follow step 10 or 11 [here](https://largecats.github.io/2019/06/17/Build-blog/) to view locally or publish to the blog site.