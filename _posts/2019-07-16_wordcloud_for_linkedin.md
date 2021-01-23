---
layout: post
title:  "Generating wordcloud for LinkedIn background photo"
date:   2019-07-16
categories: fun
tags: python text-analysis
---

* content
{:toc}

I want a background photo for my LinkedIn account. Generating a wordcloud image from my LinkedIn profile seems a good idea.



## Preparation

Same as [this blog post](https://largecats.github.io/blog/2019/06/19/Text-analysis-with-movie-reviews/).

## Method

All code can be found [here](https://github.com/largecats/text-analysis/blob/master/wordcloud).

### Obtain a text version of LinkedIn profile

Copy the relevant LinkedIn profile sections into a `.txt` file.

(It would be more convenient to scrape the LinkedIn profile, but LinkedIn has policies against web scraping, see [here](https://www.quora.com/How-do-I-scrape-LinkedIn).)

### Text processing

Next, we process the text to prepare for wordcloud generation. This has been discussed in detail [in another blog post](https://largecats.github.io/blog/2019/06/19/Text-analysis-with-movie-reviews/).

First, import the necessary modules.
```python
from wordcloud import WordCloud, STOPWORDS 
import matplotlib.pyplot as plt 
import pandas as pd 
import os
import random
import nltk
```
Then, read in the text file.
```python
# set working directory
path = ""
os.chdir(path)

fileName = input("Please enter text file name: ")
with open(fileName, 'r') as file:
    text = file.read().replace('\n', ' ')

print(text)
```
Using sample text file `text.txt` containing the first two paragraphs of [Python's wikipedia page](https://en.wikipedia.org/wiki/Python_(programming_language)), the output is as follows.
```
Please enter text file name: text.txt
Python is an interpreted, high-level, general-purpose programming language. Created by Guido van Rossum and first released in 1991, Python's design philosophy emphasizes code readability with its notable use of significant whitespace. Its language constructs and object-oriented approach aim to help programmers write clear, logical code for small and large-scale projects.  Python is dynamically typed and garbage-collected. It supports multiple programming paradigms, including procedural, object-oriented, and functional programming. Python is often described as a "batteries included" language due to its comprehensive standard library.  Python was conceived in the late 1980s as a successor to the ABC language. Python 2.0, released 2000, introduced features like list comprehensions and a garbage collection system capable of collecting reference cycles. Python 3.0, released 2008, was a major revision of the language that is not completely backward-compatible, and much Python 2 code does not run unmodified on Python 3. Due to concern about the amount of code written for Python 2, support for Python 2.7 (the last release in the 2.x series) was extended to 2020. Language developer Guido van Rossum shouldered sole responsibility for the project until July 2018 but now shares his leadership as a member of a five-person steering council.  Python interpreters are available for many operating systems. A global community of programmers develops and maintains CPython, an open source[32] reference implementation. A non-profit organization, the Python Software Foundation, manages and directs resources for Python and CPython development.
```
For convenience, we convert the text into lowercase. We then define a set of `stopwords` that are to be ignored in the wordcloud. `STOPWORDS` comes with the `wordcloud` module, we can add custom stopwords via the `update()` method of `set`.
```python
# turn to lowercase
text = text.lower()

# define words that are to be ignored in the word cloud
stopwords = set(STOPWORDS)
# stopwords.update(["project", 'use', 'hospital', 'bi', 'diagnosis'])
```
Then, we tokenize the text into a list of individual words. To normalize these words for the wordcloud, we tag them with their part-of-speech (e.g., verb, noun) and convert the words back to their original form based on these tags. E.g., "databases" becomes "database", "developed" becomes "develop". Finally, we merge these lemmatized words into a string, which is to be fed to the word cloud generator.
```python
# tokenize into words
from nltk.tokenize import word_tokenize
words = word_tokenize(text)

# pos tagging
tags = nltk.pos_tag(words)

# lemmatization
from nltk.stem.wordnet import WordNetLemmatizer
lem = WordNetLemmatizer()
lemWords = []
for i in range(len(words)):
    word = words[i]
    tag = tags[i][1]
    if 'VB' in tag:
        lemWord = lem.lemmatize(word, "v")
    elif tag == "PRP":
        lemWord = word
    else:
        lemWord = lem.lemmatize(word)
    lemWords.append(lemWord)

finalText = ' '.join(lemWords)
```

### Generate wordcloud
The recommended size for the LinkedIn background photo is 1584x396 px, so that is the size of our word cloud. The remaining code is taken from [the documentation for the word cloud module](https://amueller.github.io/word_cloud/auto_examples/a_new_hope.html).
```python
# linkedin background photo size is 1564x396
wordcloud = WordCloud(width = 1584, height = 396, 
                background_color ='black',
                min_font_size = 5,
                stopwords = stopwords,
                random_state = 42).generate(finalText) 

# for grey scale
def grey_color_func(word, font_size, position, orientation, random_state = None, **kwargs):
    return "hsl(0, 0%%, %d%%)" % random.randint(60, 100)

# plot the wordcloud image
plt.figure(figsize = (8, 2), facecolor = None) 
plt.imshow(wordcloud)
plt.imshow(wordcloud.recolor(color_func = grey_color_func, random_state = 3), interpolation = "bilinear")
plt.axis("off") 
plt.tight_layout(pad = 0) 
plt.savefig("wordcloud.png")
plt.show()
```

## Result

![]({{ site.baseurl }}{{ site.baseurl }}{{ site.baseurl }}/images/wordcloud.png){:width="800px"}
<div align="center">
<sup>Word cloud of sample text.</sup>
</div>