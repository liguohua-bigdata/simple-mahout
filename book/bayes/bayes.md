##<<新闻分类示例>>
####一、Download and extract the 20news-bydate.tar.gz from the 20newsgroups dataset to the working directory.
1.下载数据集
```
   wget http://101.96.10.65/people.csail.mit.edu/jrennie/20Newsgroups/20news-bydate.tar.gz
```
2.解压数据集
```
  tar -zxvf 20news-bydate.tar.gz
```
3.上传数据集到HDFS
```
0.精简数据集
   删除一部分数据，不需要那么大的数据集
    cd 20news-bydate-train
    rm -rf rec* talk* sci* comp*

    cd ../20news-bydate-test
    rm -rf rec* talk* sci* comp*
    
1.在hdfs上创建目录
    hadoop fs -mkdir  /input/mahout/20news_all
    
2.上传数据到hdfs
    hadoop fs -put -p  ./20news-bydate-test/  /input/mahout/20news_all/
    hadoop fs -put -p  ./20news-bydate-train/  /input/mahout/20news_all/
```

4.HDFS执行效果
![](images/Snip20161114_100.png)      

####二、Convert the full 20 newsgroups dataset into a < Text, Text > SequenceFile.
执行命令：
```
cd /${MAHOUT_HOME}/bin
./mahout seqdirectory -i /input/mahout/20news_all -o /input/mahout/20news_all_seq
```

shell执行效果：
![](images/Snip20161114_103.png)    
hadoo yarn web执行效果：
![](images/Snip20161114_101.png)    
hadoo hdfs web执行效果：
![](images/Snip20161114_104.png)    


####三、Convert and preprocesses the dataset into a < Text, VectorWritable > SequenceFile containing term frequencies for each document.
执行命令：
```
cd /${MAHOUT_HOME}/bin

./mahout seq2sparse \
-i /input/mahout/20news_all_seq/ \
-o /input/mahout/20news_all_vec/ \
-lnorm \
-nv \
-wt tfidf
```

shell执行效果：
![](images/Snip20161114_105.png)    
hadoo yarn web执行效果：
![](images/Snip20161114_106.png)    
hadoo hdfs web执行效果：
![](images/Snip20161114_108.png)    

####四、Split the preprocessed dataset into training and testing sets.  
执行命令：
```
cd /${MAHOUT_HOME}/bin

./mahout split \
-i /input/mahout/20news_all_vec/tfidf-vectors/ \
-tr /input/mahout/20news_all_rt/train-vectors/ \
-te /input/mahout/20news_all_rt/test-vectors/ \
-rp 20 \
-ow \
-seq \
-xm sequential

其中参数的意义如下：
•	-tr训练集     
•	-te测试集
•	-rp参数设定的是测试数据集占总数据集的百分比，以下代码设定为20%！   

```

shell执行效果：
![](images/Snip20161114_109.png)    
hadoo yarn web执行效果：
![](images/Snip20161114_111.png)    
hadoo hdfs web执行效果：
![](images/Snip20161114_110.png)    


4.Train the classifier.
执行命令：
```
cd /${MAHOUT_HOME}/bin

./mahout trainnb  \
-i /input/mahout/20news_all_rt/train-vectors/ \
-el \
-o /input/mahout/20news_all_mi/nbmodel/ \
-li /input/mahout/20news_all_mi/labelindex/ \
-ow \
-c 
```

shell执行效果：
![](images/Snip20161114_113.png)    
hadoo yarn web执行效果：
![](images/Snip20161114_114.png)    
hadoo hdfs web执行效果：
![](images/Snip20161114_115.png)    

####五、Test the classifier.
执行命令：
```
./mahout testnb \
-i /input/mahout/20news_all_rt/test-vectors/ \
-m /input/mahout/20news_all_mi/nbmodel/ \
-l /input/mahout/20news_all_mi/labelindex/ \
-o /input/mahout/20news_all_testing \
-ow \
-c
```
shell执行效果：
![](images/Snip20161114_116.png)    
![](images/Snip20161114_119.png)    
hadoo yarn web执行效果：
![](images/Snip20161114_117.png)    
hadoo hdfs web执行效果：
![](images/Snip20161114_118.png)    


----

