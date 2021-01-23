---
layout: post
title:  "Solving Gitalk validation error with status code 500"
date:   2019-06-20
categories: blog life-saver
tags: gitalk
---

* content
{:toc}

I got a `Error: Validation Failed with status code 500` Gitalk error after publishing a new blog post.



## Solution

Normally, the issue associated with a Gitalk comment section has two labels, namely `Gitalk` and the `.md` file name of the blog post (e.g., `/2019/06/20/gitalk/validation/error/500/`). I found that the problematic issue does not have the latter label. So I manually created the appropriate label and added it to the issue. Then the Gitalk error disappeared after I refreshed the page.