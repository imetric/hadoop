# 实验3、MapReudce高级编程




## 一、实验目的
1.	通过实验掌握基本的MapReduce编程方法；
2.  掌握用MapReduce解决一些常见的数据处理问题，包括数据去重、数据排序和数据挖掘等。



## 二、实验平台

1. 操作系统：Linux
2. Hadoop版本：3.0.0或以上版本
3. Python版本：3.7或以上版本


## 三、实验前准备

> 首先通过SSH远程连接到服务器，并执行以下命令，进入python的环境


### 1.登录服务器

如果服务器是远程（如购买的云服务器），使用用户名hadoop进行登录，可以使用Putty软件远程连接服务器进行操作，详见。

### 2.进入虚拟环境

输入以下命令进入我们创建的虚拟环境 hadoop

```
conda activate hadoop
```

## 三、实验内容和要求


### 1.编程实现文件合并和去重操作


#### 1.1 要求
对于两个输入文件，即文件A.txt和文件B.txt，请编写MapReduce程序，对两个文件进行合并，并剔除其中重复的内容，得到一个新的输出文件C.txt。下面是输入文件和输出文件的一个样例供参考。

输入文件A.txt的样例如下：

```
20150101    x
20150102    y
20150103    x
20150104    y
20150105    z
20150106    x
```

输入文件B.txt的样例如下：

```
20150101    y
20150102    y
20150103    x
20150104    z
20150105    y
```

根据输入文件A.txt和B.txt合并得到的输出文件C.txt的样例如下：

```
20150101    x
20150101    y
20150102    y
20150103    x
20150104    y
20150104    z
20150105    y
20150105    z
20150106    x
```

#### 1.2 准备文本文件

连接服务器，进入~/code/test2文件夹，如果没有创建code/test2文件夹，先创建。

```
mkdir ~/code
mkdir ~/code/test2
cd ~/code/test2
```

运行以下命令，下载A.txt、B.txt

```
cd ~/code/test2
wget https://raw.githubusercontent.com/imetric/hadoop/master/data/test2/A.txt
wget https://raw.githubusercontent.com/imetric/hadoop/master/data/test2/B.txt

```

#### 1.3 编写代码

> 思路：

1. map函数功能是按行读取文件，然后key为这一行的内容，value可以随意
2. reduce函数将相同key的输出一次即可



可以将mp_dedup.py代码在本地电脑上进行编辑，编辑完成后，将文件上传到服务器上，建议存放在~/code/目录下。



> mp_dedup.py文件内容

```
from mrjob.job import MRJob

class DeDuplication(MRJob):

    def mapper(self, _, line):
        yield (line, 1)

    def reducer(self, line, counts):
        yield (line, "")

if __name__ == '__main__':
    DeDuplication.run()
```

#### 1.4 执行mp_dedup.py

在服务器端口执行以下代码运行。

```
cd ~/code
python mp_dedup.py -r hadoop test2>dedup.txt
```
执行以上命令时，系统会自动将服务器上的文件test2文件夹里的A.txt和B.txt文件上传到HDFS中，并且运行Mapreduce任务。Mapreduce任务运行完成后，会自动下载运行结果到服务器上，并保存为dedup.txt文件。

#### 1.5 查看运行结果

可以通过以下命令，直接在命令窗口查看运行结果。
```
cat ~/code/dedup.txt
```

也可以使用Filezilla下载文件到自己电脑进行查看。

## 四、思考

上面的实验内容是统计一个文本文件中的单词个数，如果要同时统计多个文件中单词的个数，请问如何操作？

## 参考内容
- [https://raw.githubusercontent.com/Yelp/mrjob/master/mrjob/examples/](https://github.com/Yelp/mrjob/blob/master/mrjob/examples)
- 
