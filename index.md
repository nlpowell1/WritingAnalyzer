# Writing Analyzer 
## Introduction

For this project, I decided to create a tool that analyzes writing level and style using the Flesch-Kincaid index, average sentence length and the standard deviation within that, as well as repetitiveness of word use. When I was in high school, I had an English teacher tell my class that a good way to keep a reader engaged was to make sure the sentences in your writing varied in length and that you did not start too many of your sentences with the same word. In his class, in order to do this, we would manually count the words of our sentences when editing as well as go through our work and circle the first word to make sure we were not being too repetitive.  I find it is an excellent and easy way to improve my writing and I still use this technique to this day! So, I thought it would be a great idea to create a program that would speed up the process!

The Flesch-Kincaid readability tests are two different tests that indicate the reading ease and suggested grade level of writing. The reading ease test will return a number between _zero_ and _one hundred_, the higher the number corresponds inversley to the texts reading ease. It uses this formula:

206.835 - 1.015( Total Words / Total Sentences ) - 84.6( Total Syllables / Total Words )

The chart below illustrates the correlation between the reading ease score and the level of difficulty of the writing.

![Reading Ease](https://lh3.googleusercontent.com/broaSYuKWSgDp6GEcuVKd76Jt_AeHFi26vqRMyfs0uZV1FmTkvehJZ1oerWAb4uIEnj_bRXWw0dYOBlhkDpfKHFjt4gZD1_u5b0nGK8yZWAvSIzt-Cd_TFUf4IZLG08Y37IO5jce)

Similarly, the grade level text will return a number that corresponds with the American grade level the text is estimated to be at. This test uses this formula:

0.39( Total Words / Total Sentences ) + 11.8( Total Syllables / Total Words ) - 15.59

My search to find data on the ideal sentence length and variation came up pretty slim. However, I did find a tool online for purchase which measured the sentence length of your writing and its standard deviation called ProWritingAid. I did not choose to purchase this program, but I did find that they provided a brief explanation in one of their demonstrations that gave some decent information on the topic. According to them, most published writers vary their sentences on average between eleven and eighteen words. Any more would be too complicated and any less would be too choppy. These seemed like decent enough numbers for me to go off of, and so I began...

## Data

In order to test my design. I had friends and family send me essays they had written for various classes. The writers aged from high school students to college seniors. It was important to me to find writers at different levels who studied different subject so I could see the variance across the outcomes. Here is my data set:

import pandas
Data = pandas.read_csv("PData1.csv")
Data
