# Writing Analysis
## Introduction

For my project, I decided to create a tool that analyzes writing level and style using the Flesch-Kincaid index, average sentence length and the standard deviation within that, as well as repetitiveness of word use. When I was in high school, I had an English teacher tell my class that a good way to keep a reader engaged was to make sure the sentences in your writing varied in length and that you did not start too many of your sentences with the same word. In order to do this in his class, we would manually count the words of our sentences when editing as well as go through our work and circle the first word to make sure we were not being too repetitive. To this day I still use this technique, as I find it is an excellent and easy way to imrove my writing. So, I thought it would be a great project idea to create a program that would speed up the process!

The Flesch-Kincaid readability tests are two different tests that indicate the reading ease and suggested grade level of writing. The reading ease test will return a number between _zero_ and _one hundred_, the higher the number corresponds inversley to the texts reading ease. It uses this formula:


\begin{equation}
\ 206.835 - 1.015( Total Words / Total Sentences ) - 84.6( Total Syllables / Total Words )
\end{equation}

The chart below illustrates the correlation between the reading ease score and the level of difficulty of the writing.

![Reading Ease](https://lh3.googleusercontent.com/broaSYuKWSgDp6GEcuVKd76Jt_AeHFi26vqRMyfs0uZV1FmTkvehJZ1oerWAb4uIEnj_bRXWw0dYOBlhkDpfKHFjt4gZD1_u5b0nGK8yZWAvSIzt-Cd_TFUf4IZLG08Y37IO5jce)

Similarly, the grade level text will return a number that corresponds with the American grade level the text is estimated to be able to be read at. This test uses this formula:


\begin{equation}
\ 0.39( Total Words / Total Sentences ) + 11.8( Total Syllables / Total Words ) - 15.59
\end{equation}

My search to find data on the ideal sentence length and variation came up pretty slim. However, I did find a tool online for purchase which measured the sentence length of your writing and its standard deviation called ProWritingAid. I did not choose to purchase this program, but I did find that they provided a brief explanation in one of their demonstrations that gave some decent information on the topic. According to them, most published writer vary their sentences on average between eleven and eighteen words. Any more would be too complicated and any less would be too choppy. These seemed like decent enough numbers for me to go off of, and so I began...

## Data

In order to test my design. I had friends and family send me essays they had written for various classes. The writers aged from high school to college seniors. It was important to me to find writers at different levels who studied different subject so I could see the variance across the outcomes. Here is my data set:


```python
import pandas
Data = pandas.read_csv("PData1.csv")
Data
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Title</th>
      <th>Text</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>The Balfour Declaration</td>
      <td>The Balfour Declaration, published November 2n...</td>
    </tr>
    <tr>
      <td>1</td>
      <td>Tolerance, Feminism, and the Downside of Globa...</td>
      <td>During the Enlightenment, works such as Candid...</td>
    </tr>
    <tr>
      <td>2</td>
      <td>Common App Essay</td>
      <td>Following many hours of travel—flying, then dr...</td>
    </tr>
    <tr>
      <td>3</td>
      <td>The Role of Setting in “Disgrace”</td>
      <td>Published in 1999, J.M. Coetzee’s novel revolv...</td>
    </tr>
    <tr>
      <td>4</td>
      <td>Designer Babies</td>
      <td>If given the chance to choose the sex, physiq...</td>
    </tr>
    <tr>
      <td>5</td>
      <td>Freedom and Remorse</td>
      <td>Many authors use the same literary tools to w...</td>
    </tr>
    <tr>
      <td>6</td>
      <td>Mansaf and the Sustainability of Traditional J...</td>
      <td>Living in the desert has proven time and time ...</td>
    </tr>
    <tr>
      <td>7</td>
      <td>The Final Unveiling</td>
      <td>Huda Shaarawi is regarded as one of the founde...</td>
    </tr>
    <tr>
      <td>8</td>
      <td>America’s Lunch Line</td>
      <td>Imagine a group of kindergarteners, all of the...</td>
    </tr>
    <tr>
      <td>9</td>
      <td>More Than Surface Value: A Dialogue</td>
      <td>This scene takes place at a prestigious art mu...</td>
    </tr>
    <tr>
      <td>10</td>
      <td>The Narratives of a Universal Enemy</td>
      <td>As the world experiences the consequences of g...</td>
    </tr>
    <tr>
      <td>11</td>
      <td>Messel and the Evolving Berlin Apartment</td>
      <td>Since the mid nineteenth century, the barrack ...</td>
    </tr>
    <tr>
      <td>12</td>
      <td>“Little Yellow Riding Hood” A Grim (or maybe n...</td>
      <td>Once upon a time when half-hearted promises we...</td>
    </tr>
    <tr>
      <td>13</td>
      <td>Humanity’s Demise</td>
      <td>The outbreak of the first world war was a comb...</td>
    </tr>
    <tr>
      <td>14</td>
      <td>A Comparison of Australian and New Zealand Att...</td>
      <td>During the nineteenth and twentieth century, s...</td>
    </tr>
    <tr>
      <td>15</td>
      <td>Helping Relationships in the Real World</td>
      <td>For the last two years I have been working as ...</td>
    </tr>
    <tr>
      <td>16</td>
      <td>For the “Greater Good” of the Economy: Gender ...</td>
      <td>From the 1820s to the 1920s, the history of th...</td>
    </tr>
    <tr>
      <td>17</td>
      <td>Love is Love</td>
      <td>The issue of interracial relationships has his...</td>
    </tr>
    <tr>
      <td>18</td>
      <td>African Literature</td>
      <td>African literature is a means through which Af...</td>
    </tr>
  </tbody>
</table>
</div>



## Method
First, I wrote a function to get the average word count for the sentences of a text, as well as one to get the total wordcount.


```python
def average_wordcount(text):
    wordcounts = []
    sentences = text.split('.')
    for sentence in sentences:
        words = sentence.split(' ')
        wordcounts.append(len(words))
        avg_wordcount = sum(wordcounts)/len(wordcounts)
    return avg_wordcount
```


```python
def wordcount(text):
    simplewords = text.split(' ')
    num_words = len(simplewords)
    return num_words
```

My next step was to get the standard deviation of the sentence lengths.


```python
import statistics

def stnd_dev(text):
    wordcounts = []
    sentences = text.split('.')
    for sentence in sentences:
        words = sentence.split(' ')
        wordcounts.append(len(words))
    return (statistics.stdev(wordcounts))   
```

Finally, I created a function to find all of beginning sentence words and display them.


```python
def startwords(text):
    mylist = text.split()  
    bigrams = zip(mylist, mylist[1:])
    return [b[1] for b in bigrams if b[0].endswith('.')]
```

Now, I applied this to the data I collected. I had some friends send me old essays they had written, and I compiled those along with some of my own. Let's see how our writing styles vary in terms of sentence length.


```python
Data['Average Sentence Length'] = Data.Text.apply(average_wordcount)
Data['Standard Deviation'] = Data.Text.apply(stnd_dev)
Data['Start Words'] = Data.Text.apply(startwords)
Data
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Title</th>
      <th>Text</th>
      <th>Average Sentence Length</th>
      <th>Standard Deviation</th>
      <th>Start Words</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>The Balfour Declaration</td>
      <td>The Balfour Declaration, published November 2n...</td>
      <td>19.500000</td>
      <td>12.525155</td>
      <td>[These, The, The, It, It, His, I, A, This, The...</td>
    </tr>
    <tr>
      <td>1</td>
      <td>Tolerance, Feminism, and the Downside of Globa...</td>
      <td>During the Enlightenment, works such as Candid...</td>
      <td>22.063830</td>
      <td>10.174614</td>
      <td>[Candide, The, Candide, All, Pangloss, Despite...</td>
    </tr>
    <tr>
      <td>2</td>
      <td>Common App Essay</td>
      <td>Following many hours of travel—flying, then dr...</td>
      <td>23.466667</td>
      <td>10.874625</td>
      <td>[Eager, My, He, When, We, Each, My, Faces, Pho...</td>
    </tr>
    <tr>
      <td>3</td>
      <td>The Role of Setting in “Disgrace”</td>
      <td>Published in 1999, J.M. Coetzee’s novel revolv...</td>
      <td>21.226415</td>
      <td>14.070085</td>
      <td>[Coetzee’s, Disgrace, Coetzee, Throughout, Coe...</td>
    </tr>
    <tr>
      <td>4</td>
      <td>Designer Babies</td>
      <td>If given the chance to choose the sex, physiq...</td>
      <td>23.509804</td>
      <td>12.207565</td>
      <td>[Couples, People, As, TThe, The, However,, The...</td>
    </tr>
    <tr>
      <td>5</td>
      <td>Freedom and Remorse</td>
      <td>Many authors use the same literary tools to w...</td>
      <td>22.950000</td>
      <td>11.041409</td>
      <td>[This, In, “The, Montresor, However,, All, His...</td>
    </tr>
    <tr>
      <td>6</td>
      <td>Mansaf and the Sustainability of Traditional J...</td>
      <td>Living in the desert has proven time and time ...</td>
      <td>28.527778</td>
      <td>13.739383</td>
      <td>[A, One, The, Some, Stemming, Mansaf,, Lamb, H...</td>
    </tr>
    <tr>
      <td>7</td>
      <td>The Final Unveiling</td>
      <td>Huda Shaarawi is regarded as one of the founde...</td>
      <td>20.553191</td>
      <td>12.719052</td>
      <td>[She, In, Perhaps, When, The, According, Even,...</td>
    </tr>
    <tr>
      <td>8</td>
      <td>America’s Lunch Line</td>
      <td>Imagine a group of kindergarteners, all of the...</td>
      <td>26.362637</td>
      <td>13.850404</td>
      <td>[Some, Others, The, Each, This, The, Today,, T...</td>
    </tr>
    <tr>
      <td>9</td>
      <td>More Than Surface Value: A Dialogue</td>
      <td>This scene takes place at a prestigious art mu...</td>
      <td>19.784615</td>
      <td>11.557061</td>
      <td>[A, Ingres’, GOYA, GOYA:, BUYER:, You’re, GOYA...</td>
    </tr>
    <tr>
      <td>10</td>
      <td>The Narratives of a Universal Enemy</td>
      <td>As the world experiences the consequences of g...</td>
      <td>25.000000</td>
      <td>10.685219</td>
      <td>[Reflecting, The, Mark, The, Groups, Mamdani, ...</td>
    </tr>
    <tr>
      <td>11</td>
      <td>Messel and the Evolving Berlin Apartment</td>
      <td>Since the mid nineteenth century, the barrack ...</td>
      <td>21.648855</td>
      <td>8.349043</td>
      <td>[Berlin’s, However,, Analyzing, Understanding,...</td>
    </tr>
    <tr>
      <td>12</td>
      <td>“Little Yellow Riding Hood” A Grim (or maybe n...</td>
      <td>Once upon a time when half-hearted promises we...</td>
      <td>17.447059</td>
      <td>12.290633</td>
      <td>[For, Their, One, Little, It, She, Some, Soon,...</td>
    </tr>
    <tr>
      <td>13</td>
      <td>Humanity’s Demise</td>
      <td>The outbreak of the first world war was a comb...</td>
      <td>24.496503</td>
      <td>8.630828</td>
      <td>[Political, World, For, This, Dangerous, Twent...</td>
    </tr>
    <tr>
      <td>14</td>
      <td>A Comparison of Australian and New Zealand Att...</td>
      <td>During the nineteenth and twentieth century, s...</td>
      <td>18.514019</td>
      <td>9.073458</td>
      <td>[Beginning, However,, The, Historical, Terra, ...</td>
    </tr>
    <tr>
      <td>15</td>
      <td>Helping Relationships in the Real World</td>
      <td>For the last two years I have been working as ...</td>
      <td>22.613333</td>
      <td>10.624924</td>
      <td>[In, In, I, I, A, Typically, Sometimes, Other,...</td>
    </tr>
    <tr>
      <td>16</td>
      <td>For the “Greater Good” of the Economy: Gender ...</td>
      <td>From the 1820s to the 1920s, the history of th...</td>
      <td>23.984615</td>
      <td>11.004960</td>
      <td>[Whether, In, I, By, In, By, One, In, This, Se...</td>
    </tr>
    <tr>
      <td>17</td>
      <td>Love is Love</td>
      <td>The issue of interracial relationships has his...</td>
      <td>21.913043</td>
      <td>8.871493</td>
      <td>[There, Over, With, In, Through, Through, How,...</td>
    </tr>
    <tr>
      <td>18</td>
      <td>African Literature</td>
      <td>African literature is a means through which Af...</td>
      <td>22.467532</td>
      <td>7.347725</td>
      <td>[The, When, By, Why, This, By, Born, He, In, H...</td>
    </tr>
  </tbody>
</table>
</div>



Now that I had recovered the average sentence length, standard deviation, and first words of the sentences in the data, I went into calculating the Flesch-Kincaid reading ease and grade level scores. The first set of new data I needed was the syllable count for each text.


```python
def syllable_count(word):
    word = word.lower()
    count = 0
    vowels = "aeiouy"
    if word[0] in vowels:
        count += 1
    for index in range(1, len(word)):
        if word[index] in vowels and word[index - 1] not in vowels:
            count += 1
    if word.endswith("e"):
        count -= 1
    if count == 0:
        count += 1
    return count
```

Next, I found the number of sentences in the text.


```python
def sentence_count(text):
    simplesents = text.split('.')
    num_sents = len(simplesents)
    return num_sents
```

And finally, the word count.


```python
def wordcount(text):
    simplewords = text.split(' ')
    num_words = len(simplewords)
    return num_words
```

I added this new data to my data table to ensure my code was working correctly.


```python
Data['Syllable Count'] = Data.Text.apply(syllable_count)
Data['Sentence Count'] = Data.Text.apply(sentence_count)
Data['Word Count'] = Data.Text.apply(wordcount)
Data.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Title</th>
      <th>Text</th>
      <th>Average Sentence Length</th>
      <th>Standard Deviation</th>
      <th>Start Words</th>
      <th>Syllable Count</th>
      <th>Sentence Count</th>
      <th>Word Count</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>The Balfour Declaration</td>
      <td>The Balfour Declaration, published November 2n...</td>
      <td>19.500000</td>
      <td>12.525155</td>
      <td>[These, The, The, It, It, His, I, A, This, The...</td>
      <td>2698</td>
      <td>84</td>
      <td>1555</td>
    </tr>
    <tr>
      <td>1</td>
      <td>Tolerance, Feminism, and the Downside of Globa...</td>
      <td>During the Enlightenment, works such as Candid...</td>
      <td>22.063830</td>
      <td>10.174614</td>
      <td>[Candide, The, Candide, All, Pangloss, Despite...</td>
      <td>3566</td>
      <td>94</td>
      <td>1981</td>
    </tr>
    <tr>
      <td>2</td>
      <td>Common App Essay</td>
      <td>Following many hours of travel—flying, then dr...</td>
      <td>23.466667</td>
      <td>10.874625</td>
      <td>[Eager, My, He, When, We, Each, My, Faces, Pho...</td>
      <td>956</td>
      <td>30</td>
      <td>675</td>
    </tr>
    <tr>
      <td>3</td>
      <td>The Role of Setting in “Disgrace”</td>
      <td>Published in 1999, J.M. Coetzee’s novel revolv...</td>
      <td>21.226415</td>
      <td>14.070085</td>
      <td>[Coetzee’s, Disgrace, Coetzee, Throughout, Coe...</td>
      <td>3646</td>
      <td>106</td>
      <td>2145</td>
    </tr>
    <tr>
      <td>4</td>
      <td>Designer Babies</td>
      <td>If given the chance to choose the sex, physiq...</td>
      <td>23.509804</td>
      <td>12.207565</td>
      <td>[Couples, People, As, TThe, The, However,, The...</td>
      <td>4002</td>
      <td>102</td>
      <td>2297</td>
    </tr>
  </tbody>
</table>
</div>



Now that I had all the data I needed, I calculated the readability score for each text.


```python
def FRE(text):
    score = 206.835 - 1.015 * (wordcount(text) / sentence_count(text)) - 84.6 * (syllable_count(text) / wordcount(text))
    return score
```

Then, the estimated grade level.


```python
def grade_level(text):
    new_score = 0.39 * (wordcount(text) / sentence_count(text)) + 11.8 * (syllable_count(text) / wordcount(text)) - 15.59
    return new_score
```


```python
Data['Flesh-Kincaid Reading Ease'] = Data.Text.apply(FRE)
Data["Flesh-Kincaid Grade Level"] = Data.Text.apply(grade_level)
Data.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Title</th>
      <th>Text</th>
      <th>Average Sentence Length</th>
      <th>Standard Deviation</th>
      <th>Start Words</th>
      <th>Syllable Count</th>
      <th>Sentence Count</th>
      <th>Word Count</th>
      <th>Flesh-Kincaid Reading Ease</th>
      <th>Flesh-Kincaid Grade Level</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>The Balfour Declaration</td>
      <td>The Balfour Declaration, published November 2n...</td>
      <td>19.500000</td>
      <td>12.525155</td>
      <td>[These, The, The, It, It, His, I, A, This, The...</td>
      <td>2698</td>
      <td>84</td>
      <td>1555</td>
      <td>41.260336</td>
      <td>12.103212</td>
    </tr>
    <tr>
      <td>1</td>
      <td>Tolerance, Feminism, and the Downside of Globa...</td>
      <td>During the Enlightenment, works such as Candid...</td>
      <td>22.063830</td>
      <td>10.174614</td>
      <td>[Candide, The, Candide, All, Pangloss, Despite...</td>
      <td>3566</td>
      <td>94</td>
      <td>1981</td>
      <td>33.155874</td>
      <td>13.870234</td>
    </tr>
    <tr>
      <td>2</td>
      <td>Common App Essay</td>
      <td>Following many hours of travel—flying, then dr...</td>
      <td>23.466667</td>
      <td>10.874625</td>
      <td>[Eager, My, He, When, We, Each, My, Faces, Pho...</td>
      <td>956</td>
      <td>30</td>
      <td>675</td>
      <td>64.178833</td>
      <td>9.897296</td>
    </tr>
    <tr>
      <td>3</td>
      <td>The Role of Setting in “Disgrace”</td>
      <td>Published in 1999, J.M. Coetzee’s novel revolv...</td>
      <td>21.226415</td>
      <td>14.070085</td>
      <td>[Coetzee’s, Disgrace, Coetzee, Throughout, Coe...</td>
      <td>3646</td>
      <td>106</td>
      <td>2145</td>
      <td>42.495333</td>
      <td>12.359231</td>
    </tr>
    <tr>
      <td>4</td>
      <td>Designer Babies</td>
      <td>If given the chance to choose the sex, physiq...</td>
      <td>23.509804</td>
      <td>12.207565</td>
      <td>[Couples, People, As, TThe, The, However,, The...</td>
      <td>4002</td>
      <td>102</td>
      <td>2297</td>
      <td>36.581342</td>
      <td>13.751463</td>
    </tr>
  </tbody>
</table>
</div>



Here I cleaned up my data to display the important information to the user. And Ta-Da! My program is complete


```python
Data[["Title", "Average Sentence Length", "Standard Deviation", "Start Words", "Flesh-Kincaid Reading Ease", "Flesh-Kincaid Grade Level"]]
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Title</th>
      <th>Average Sentence Length</th>
      <th>Standard Deviation</th>
      <th>Start Words</th>
      <th>Flesh-Kincaid Reading Ease</th>
      <th>Flesh-Kincaid Grade Level</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>The Balfour Declaration</td>
      <td>19.500000</td>
      <td>12.525155</td>
      <td>[These, The, The, It, It, His, I, A, This, The...</td>
      <td>41.260336</td>
      <td>12.103212</td>
    </tr>
    <tr>
      <td>1</td>
      <td>Tolerance, Feminism, and the Downside of Globa...</td>
      <td>22.063830</td>
      <td>10.174614</td>
      <td>[Candide, The, Candide, All, Pangloss, Despite...</td>
      <td>33.155874</td>
      <td>13.870234</td>
    </tr>
    <tr>
      <td>2</td>
      <td>Common App Essay</td>
      <td>23.466667</td>
      <td>10.874625</td>
      <td>[Eager, My, He, When, We, Each, My, Faces, Pho...</td>
      <td>64.178833</td>
      <td>9.897296</td>
    </tr>
    <tr>
      <td>3</td>
      <td>The Role of Setting in “Disgrace”</td>
      <td>21.226415</td>
      <td>14.070085</td>
      <td>[Coetzee’s, Disgrace, Coetzee, Throughout, Coe...</td>
      <td>42.495333</td>
      <td>12.359231</td>
    </tr>
    <tr>
      <td>4</td>
      <td>Designer Babies</td>
      <td>23.509804</td>
      <td>12.207565</td>
      <td>[Couples, People, As, TThe, The, However,, The...</td>
      <td>36.581342</td>
      <td>13.751463</td>
    </tr>
    <tr>
      <td>5</td>
      <td>Freedom and Remorse</td>
      <td>22.950000</td>
      <td>11.041409</td>
      <td>[This, In, “The, Montresor, However,, All, His...</td>
      <td>47.882232</td>
      <td>12.037850</td>
    </tr>
    <tr>
      <td>6</td>
      <td>Mansaf and the Sustainability of Traditional J...</td>
      <td>28.527778</td>
      <td>13.739383</td>
      <td>[A, One, The, Some, Stemming, Mansaf,, Lamb, H...</td>
      <td>31.583651</td>
      <td>15.699610</td>
    </tr>
    <tr>
      <td>7</td>
      <td>The Final Unveiling</td>
      <td>20.553191</td>
      <td>12.719052</td>
      <td>[She, In, Perhaps, When, The, According, Even,...</td>
      <td>45.997567</td>
      <td>11.706434</td>
    </tr>
    <tr>
      <td>8</td>
      <td>America’s Lunch Line</td>
      <td>26.362637</td>
      <td>13.850404</td>
      <td>[Some, Others, The, Each, This, The, Today,, T...</td>
      <td>28.405065</td>
      <td>15.600907</td>
    </tr>
    <tr>
      <td>9</td>
      <td>More Than Surface Value: A Dialogue</td>
      <td>19.784615</td>
      <td>11.557061</td>
      <td>[A, Ingres’, GOYA, GOYA:, BUYER:, You’re, GOYA...</td>
      <td>55.025728</td>
      <td>10.252876</td>
    </tr>
    <tr>
      <td>10</td>
      <td>The Narratives of a Universal Enemy</td>
      <td>25.000000</td>
      <td>10.685219</td>
      <td>[Reflecting, The, Mark, The, Groups, Mamdani, ...</td>
      <td>17.631322</td>
      <td>16.772732</td>
    </tr>
    <tr>
      <td>11</td>
      <td>Messel and the Evolving Berlin Apartment</td>
      <td>21.648855</td>
      <td>8.349043</td>
      <td>[Berlin’s, However,, Analyzing, Understanding,...</td>
      <td>25.954030</td>
      <td>14.770909</td>
    </tr>
    <tr>
      <td>12</td>
      <td>“Little Yellow Riding Hood” A Grim (or maybe n...</td>
      <td>17.447059</td>
      <td>12.290633</td>
      <td>[For, Their, One, Little, It, She, Some, Soon,...</td>
      <td>44.271396</td>
      <td>11.173187</td>
    </tr>
    <tr>
      <td>13</td>
      <td>Humanity’s Demise</td>
      <td>24.496503</td>
      <td>8.630828</td>
      <td>[Political, World, For, This, Dangerous, Twent...</td>
      <td>17.504271</td>
      <td>16.656756</td>
    </tr>
    <tr>
      <td>14</td>
      <td>A Comparison of Australian and New Zealand Att...</td>
      <td>18.514019</td>
      <td>9.073458</td>
      <td>[Beginning, However,, The, Historical, Terra, ...</td>
      <td>18.179345</td>
      <td>15.076965</td>
    </tr>
    <tr>
      <td>15</td>
      <td>Helping Relationships in the Real World</td>
      <td>22.613333</td>
      <td>10.624924</td>
      <td>[In, In, I, I, A, Typically, Sometimes, Other,...</td>
      <td>41.293428</td>
      <td>12.872390</td>
    </tr>
    <tr>
      <td>16</td>
      <td>For the “Greater Good” of the Economy: Gender ...</td>
      <td>23.984615</td>
      <td>11.004960</td>
      <td>[Whether, In, I, By, In, By, One, In, This, Se...</td>
      <td>33.020836</td>
      <td>14.367425</td>
    </tr>
    <tr>
      <td>17</td>
      <td>Love is Love</td>
      <td>21.913043</td>
      <td>8.871493</td>
      <td>[There, Over, With, In, Through, Through, How,...</td>
      <td>47.485261</td>
      <td>11.836871</td>
    </tr>
    <tr>
      <td>18</td>
      <td>African Literature</td>
      <td>22.467532</td>
      <td>7.347725</td>
      <td>[The, When, By, Why, This, By, Born, He, In, H...</td>
      <td>29.591281</td>
      <td>14.468297</td>
    </tr>
  </tbody>
</table>
</div>



## Conclusion

Overall, I am pretty happy with how this program turned out. However, one limitation of my design is that although it does come up with a calculated grade level score, it does not actually check the correctness of the writing or give feedback on specific style or syntax. Continuing,  I would like to investigate further if there were ways return the text marked up with any sentences that were outliers in terms of length or structure. This would provide the user with much more specific areas that need correction. 

One tool I would like to look into more in the future for this is spacy. I am really intrigued by spacy and I think if I had more time with it I would be able to do a lot to further my project and look deeper into analyzing and editing sentence structure and syntax.

Additionally, I believe I could make this program more useful by collecting works of recognized authors and scholars and seeing if I could find a correlation between their sentence length and structure, or perhaps similarities across disciplines. 

But despite its limitations, I believe my project was successful. I gained more confidence with Python and created something that will actually be useful to myself and others. Already by looking at the sentence length for all of the pieces that I contributed I can see that I tend to write sentences that are a bit too lengthy, at least according to ProWritingAid... Seeing the data lined up together was very interesting to me, and my friends and I liked being able to see what level we are supposedly writing at. I am excited to continue with Python and see what else I can create!


```python

```
