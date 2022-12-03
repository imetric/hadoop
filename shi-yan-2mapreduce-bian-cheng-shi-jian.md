# 实验2、MapReduce编程实践

## 一、实验目的

1. 通过实验掌握基本的MapReduce编程方法；
2. 掌握用MapReduce解决来进行单词词频统计。

## 二、实验平台

1. 操作系统：Linux
2. Hadoop版本：3.0.0或以上版本
3. Python版本：3.7或以上版本

## 三、实验前准备

> 首先通过SSH远程连接到服务器，并执行以下命令，进入python的环境

### 1.登录服务器

如果服务器是远程（如购买的云服务器），使用用户名hadoop进行登录，可以使用Putty软件远程连接服务器进行操作，详见。

### 2. 如果使用老师的服务器

如果第一次使用老师分配的云服务器，需要先配置好云服务器。配置方法参考：

{% content-ref url="shi-yong-lao-shi-fen-pei-de-teng-xun-yun-fu-wu-qi.md" %}
[shi-yong-lao-shi-fen-pei-de-teng-xun-yun-fu-wu-qi.md](shi-yong-lao-shi-fen-pei-de-teng-xun-yun-fu-wu-qi.md)
{% endcontent-ref %}

### 3.进入虚拟环境

输入以下命令进入我们创建的虚拟环境 hadoop

```
conda activate hadoop
```

### 4.在本地创建创建python文件

在本地创建一个wordcount.py文件，其中hdfs是文件名，可以自定义。hdfs.py文件的代码如下：

```
from mrjob.job import MRJob
import re

WORD_RE = re.compile(r"[\w']+")


class MRWordFreqCount(MRJob):

    def mapper(self, _, line):
        for word in WORD_RE.findall(line):
            yield (word.lower(), 1)

    def combiner(self, word, counts):
        yield (word, sum(counts))

    def reducer(self, word, counts):
        yield (word, sum(counts))


if __name__ == '__main__':
    MRWordFreqCount.run()
```

### 5.使用Filezila软件进行文件传输

编写的Python代码可以在本地电脑上编辑，编辑完成后，上传到服务器上运行。

本地文件可以使用Filezila软件上传到服务器。

## 三、实验内容和要求

本实验使用Hadoop对指定文件统计各单词出现的次数。

### 1.准备文本文件

连接服务器，进入\~/code文件夹，如果没有创建code文件夹，先创建。

```
mkdir ~/code
cd ~/code
```

运行以下命令，下载wordcount.txt

```
cd ~/code
wget https://labeye.oss-cn-beijing.aliyuncs.com/hadoop/word.txt
```

### 2.编写wordcount代码

可以将wordcount.py代码在本地电脑上进行编辑，编辑完成后，将文件上传到服务器上，建议存放在\~/code/目录下。

> wordcount.py文件内容

```
from mrjob.job import MRJob
import re

WORD_RE = re.compile(r"[\w']+")


class MRWordFreqCount(MRJob):

    def mapper(self, _, line):
        for word in WORD_RE.findall(line):
            yield (word.lower(), 1)

    def combiner(self, word, counts):
        yield (word, sum(counts))

    def reducer(self, word, counts):
        yield (word, sum(counts))


if __name__ == '__main__':
    MRWordFreqCount.run()
```

### 3.执行wordcount.py

在服务器端口执行以下代码运行。

```
cd ~/code
python wordcount.py -r hadoop word.txt>wordcount.txt
```

执行以上命令时，系统会自动将服务器上的文件word.txt上传到HDFS中，并且运行Mapreduce任务。Mapreduce任务运行完成后，会自动下载运行结果到服务器上，并保存为wordcount.txt文件。

### 4.查看运行结果

可以通过以下命令，直接在命令窗口查看运行结果。

```
cat ~/code/wordcount.txt
```

也可以使用Filezilla下载文件到自己电脑进行查看。

## 四、思考

上面的实验内容是统计一个文本文件中的单词个数，如果要同时统计多个文件中单词的个数，请问如何操作？

## 参考内容

* [https://raw.githubusercontent.com/Yelp/mrjob/master/mrjob/examples/](https://github.com/Yelp/mrjob/blob/master/mrjob/examples)
*
