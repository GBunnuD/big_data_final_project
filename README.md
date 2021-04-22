# Big Data Final Project.
<table>
<td align="center"><a href="https://github.com/GD-Prasad"><img src="https://avatars.githubusercontent.com/u/59986885?s=400&u=df8057f5d9aa0936da702cdb1a5a776ceddf12a5&v=4" width="100px;" alt=""/><br /><sub><b>GD Prasad</b></sub></a><br /></td></table>

# Resource file for this project.
- [Code for the Final Project](https://databricks-prod-cloudfront.cloud.databricks.com/public/4027ec902e239c93eaaa8714f173bcfc/2727846016963951/2533952050554329/2854637756275340/latest.html)

# Requiremnets.
- Python Programming langauge.
- Databricks Community Edition.
- Databricks PySpark Api
- Spark Processing Engine

# About Data:
- I have selected The Project Gutenberg eBook of Hindu Magic, which is a free source and has text with no images.
- This text contains punctuations and some gerneral words like I, the , on , etc.

# Commands Used:
#### Importing Data from external webpage in text form with no images.
```python 
    import urllib.request as ur 
    #Importing Data from webpage with no Images.
    ur.urlretrieve("https://www.gutenberg.org/files/65121/65121-0.txt", "/tmp/gd_fp_data.txt");
    dbutils.fs.mv("file:/tmp/gd_fp_data.txt","dbfs:/data/gd_fp_data.txt"); 
```
 - We use urllib library to import data from the webpage to the notebook ann is beinng stored in the tmp folder as gd_fp_data.txt.
 - Here the data is the the temp folder and not in the data folder. So, We move the data from tmp to dbfs.
 
 #### Filtering the Data.
 ```python
gdRDD=sc.textFile("dbfs:/data/gd_fp_data.txt",5);
```
- Moving the data into spark to store data in spark by converting the data into RDD.
 ```python
# Spliting and Converting all the data into Lower Case.
gdWordsRDD=gdRDD.flatMap(lambda line:line.lower().strip().split(" "));
```
- The data which is pulled from the webpage is in the form of a big sentence.
- For which the data should be split into seperate words.
- We use space to seperate each word and split them.

 ```python
# To remove Stop words.
from pyspark.ml.feature import StopWordsRemover as swr
remove =swr();
stopWords = remove.getStopWords();
gdCleanData=gdWordsRDD.filter(lambda wrds: wrds not in stopWords);
```
- Once the data is being split it still contains Stop words, which needs to be Filtered.
- So import stopWordsRemover function from pyspark.ml.feature.
- It contains most of the Stop Words and it can be used to remove all the Stop Words in our Data.
- The Data is now being filtered from Stop Words. 

```python
# To remove punctutations.
import re
cleanData = gdCleanData.map(lambda words: re.sub(r'[^a-zA-Z]','',words));

# To Remove Space.
gdIKVPairsRDD = cleanData.filter(lambda x: x != "");
 ```
 - The Data still consists of punctuations and Blank spaces, which needs to be removed.
 - So using re library to remove punctuations with lambda expression.
 - Here the punctuations will be replaced by spaces.
 - Using Lambda expression to remove spaces in the data.
 
 ```python
 # Setting Map for the words with the number of repetitions.
gdIKVPairsRDDs= gdIKVPairsRDD.map(lambda word:(word,1));
gdresultsRDD=gdIKVPairsRDDs.reduceByKey(lambda acc,value:acc+value);
# Filtering Top 25 Words with highest repetitions.
gdResult = gdresultsRDD.map(lambda x: (x[1], x[0])).sortByKey(False).take(15);
```
- Mapping each word with a number 1.
- So that we can just add the Map value while same word is found in the lambda function.
- Once the words are mapped with there repetition number.
- Only top 15 higest words repeted are being filtered.
```python
print(gdResult);
```
- Printing the result to view the top 10 words.
```python 
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
from collections import Counter

# preparing chart information
source = 'The Project Gutenberg eBook of Hindu Magic'
title = 'Top Words in ' + source
xlabel = 'word'
ylabel = 'count' 
```
- Importing all required libraries to polt a graph.
- Creating the chart by setiing the source,titile of the chart, x label as words and y label as count for the graph.
```python
# create Pandas dataframe from list of tuples
df = pd.DataFrame.from_records(gdResult, columns =[xlabel, ylabel]) ;
print(df);

# create plot (using matplotlib)
plt.figure(figsize=(10,6));
sns.barplot(xlabel, ylabel, data=df, palette="husl").set_title(title);
```
- Creating a pandas dataframe to store the data as a data frame.
- the data frame consits of data with it's label.
- Using matplotlib to plot a graph.
 
