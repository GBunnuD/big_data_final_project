# big_data_final_project
- [Prasad Golla Durga](https://github.com/GD-Prasad)

# Resource file for this project.
- []()

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
 ```python.
gdRDD=sc.textFile("dbfs:/data/gd_fp_data.txt",5);
```
- Moving the data into spark to store data in spark by converting the data into RDD.
 ```python.
# Spliting and Converting all the data into Lower Case.
gdWordsRDD=gdRDD.flatMap(lambda line:line.lower().strip().split(" "));
```
- The data which is pulled from the webpage is in the form of a big sentence.
- For which the data should be split into seperate words.
- We use space to seperate each word and split them.

 ```python.
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
 
 
 
