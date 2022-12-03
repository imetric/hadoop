# 实验3、MapReudce高级编程

## 一、实验目的

1. 通过实验掌握基本的MapReduce编程方法；
2. 掌握用MapReduce解决一些常见的数据处理问题，包括数据去重、数据排序和数据挖掘等。

## 二、实验平台

1. 操作系统：Linux
2. Hadoop版本：3.0.0或以上版本
3. Python版本：3.7或以上版本

## 三、实验前准备

> 首先通过SSH远程连接到服务器，并执行以下命令，进入python的环境

### 1.登录服务器

如果服务器是远程（如购买的云服务器），使用用户名hadoop进行登录，可以使用Putty软件远程连接服务器进行操作，详见。

### 2. 如果使用老师的服务器

#### 2.1  修改hosts

输入以下命令，进入hosts文件编辑界面。hosts文件存在于/etc/hosts

```
sudo vi /etc/hosts
```

删除以下内容

```
127.0.1.1 localhost.localdomain VM-0-2-ubuntu
```

增加以一上内容，这样就可以直接使用hadoop01来访问这台主机了。注意10.206.0.2为你主机的内网IP地址、

```
10.206.0.2 hadoop01
```

然后依次输入 **Esc**， **:wq**，**回车**

#### 2.2 重新配置无密码登录

```
rm -r ~/.ssh
ssh-keygen -t rsa -P '' -f ~/.ssh/id_rsa
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
chmod 0600 ~/.ssh/authorized_keys
ssh hadoop01
```

### 3.进入虚拟环境

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

连接服务器，进入\~/code/test2文件夹，如果没有创建code/test2文件夹，先创建。

```
mkdir ~/code
mkdir ~/code/test2
cd ~/code/test2
```

运行以下命令，下载A.txt、B.txt

```
cd ~/code/test2
wget https://labeye.oss-cn-beijing.aliyuncs.com/hadoop/test2/A.txt
wget https://labeye.oss-cn-beijing.aliyuncs.com/hadoop/test2/B.txt

```

#### 1.3 编写代码

> 思路：

1. map函数功能是按行读取文件，然后key为这一行的内容，value可以随意
2. reduce函数将相同key的输出一次即可

可以将mp\_dedup.py代码在本地电脑上进行编辑，编辑完成后，将文件上传到服务器上，建议存放在\~/code/目录下。

> mp\_dedup.py文件内容

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

#### 1.4 执行mp\_dedup.py

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

### 2 实验JOIN操作

#### 2.1 数据说明

order.txt和pd.txt两个文件。

其中order.txt存放商品的订单数据。每一行表示一个订单，第一列表示订单id（id），第二列表示商品id（pid），第三列表示订单数量(amount)。

```
1001	01	1
1002	02	2
1003	03	3
1004	01	4
1005	02	5
1006	03	6
```

pd.txt表示品牌信息，每一行表示一个品牌，第一列表示品牌id(pid)，第二列表示品牌名称(pname)。

```
01	小米 
02	华为 
03	格力
```

#### 2.2 要求

整合订单文件和品牌文件，将商品信息表中的数据根据商品pid合并到订单数据表中，按pid和id的大小进行排序。

```
1001	小米	1
1004	小米	4
1002	华为	2
1005	华为	5
1003	格力	3
1006	格力	6
```

#### 2.3 思路

如果你想写一个完善健壮的map reduce程序，我建议你首先弄清楚输入数据的格式、输出数据的格式，然后自己手动构建输入数据并手动计算出输出数据，这个过程中你会发现一些写程序中需要特别处理的地方：

1. 实现join的key是哪个，是1个字段还是2个字段，本例中key是品牌id(pid)，1个字段
2. 每个集合中key是否可以重复，本例中数据order.txt可重复，数据pd.txt的key不可以重复
3. 每个集合中key的对应值是否可以不存在，本例中有品牌没有订单，所以order.txt的key可以为空

第1条会影响到hadoop启动脚本中key.fields和partition的配置，第2条会影响到map-reduce程序中具体的代码实现方式，第3条同样影响代码编写方式。

hadoop实现join操作的思路

具体思路是给每个数据源加上一个数字标记label，这样hadoop对其排序后同一个字段的数据排在一起并且按照label排好序了，于是直接将相邻相同key的数据合并在一起输出就得到了结果。

1. map阶段：给order.txt和表pd.txt加标记，其实就是多输出一个字段，比如pd.txt加标记为a,order.txt加标记为b；
2. partion阶段：根据品牌id(pid)为第一主键，标记label为第二主键进行排序和分区
3. reduce阶段：由于已经按照第一主键、第二主键排好了序，将相邻相同key数据合并输出

#### 2.4 操作

运行以下命令，下载order.txt、pd.txt

```
cd ~/code/
mkdir join 
cd join
wget https://labeye.oss-cn-beijing.aliyuncs.com/hadoop/join/pd.txt
wget https://labeye.oss-cn-beijing.aliyuncs.com/hadoop/join/order.txt

```

在\~/code目录下，创建join.py文件，文件内容参考：

```
from mrjob.job import MRJob

class MRJoin(MRJob):

    def mapper(self, _, line):
        #将每一行文字拆分
        words = line.split()
        if len(words)==2:#如果拆分出2列，则是 pd.txt
            key = words[0]
            yield (key, ('a',line))
        elif len(words)==3: #如果拆分出3列，则是 order.txt
            key = words[1]
            yield (key, ('b',line))

    def reducer(self, key, values):
        brand = None
        lines = list(values)
        # 遍历values，找也品牌名称
        # 判断数据来源
        for (seed, line) in lines:
            ws = line.split()
            if seed == 'a':
                brand = ws[1]
                break
        # 遍历打印
        for (seed, line) in lines:
            ws = line.split()
            if seed == 'b':
                value = '{} {}  {}'.format(ws[0],brand,ws[2])
                yield("",value)

if __name__ == '__main__':
    MRJoin.run()
```

## 参考内容

* [https://raw.githubusercontent.com/Yelp/mrjob/master/mrjob/examples/](https://github.com/Yelp/mrjob/blob/master/mrjob/examples)

