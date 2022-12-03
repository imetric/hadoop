# 实验1、HDFS编辑实践

## 一、实验目的

1. 理解HDFS在Hadoop体系结构中的角色；
2. 熟练使用HDFS操作常用的Shell命令；
3. 熟练使用Python对HDFS进行操作。

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

### 4.在本地创建hdfs.py

在本地创新一个hdfs.py文件，其中hdfs是文件名，可以自定义。hdfs.py文件的代码如下：

```
from pyarrow import fs
hdfs = fs.HadoopFileSystem(host="hadoop01",port=9000)
hdfs.create_dir('/my')
```

### 5.使用Filezila软件进行文件传输

编写的Python代码可以在本地电脑上编辑，编辑完成后，上传到服务器上运行。

本地文件可以使用Filezila软件上传到服务器。

## 三、实验内容和要求

编程实现以下指定功能，并利用Hadoop提供的Shell命令完成相同任务：

### 2.创建目录

创建一个输入目录，目录名称为:/1130182。

> Shell

```
hadoop fs -mkdir /1130182  
```

> Python

```
hdfs.create_dir('/1130182')
```

### 3.从本地上传文件到HDFS

向HDFS中上传任意文本文件，如果指定的文件在HDFS中已经存在，由用户指定是追加到原有文件末尾还是覆盖原有的文件；

> Shell

```
hadoop fs -put /usr/local/hadoop/README.txt /1130182/readme.txt 
```

> Python

```
fs.copy_files('file:///usr/local/hadoop/README.txt','hdfs://hadoop01:9000/1130182/readme.txt')
```

注意：

* file:///usr/local/hadoop/README.txt 为本地文件的完整路径，要以file://开头。
* hdfs://hadoop01:9000/my/readme.txt为存放到HDFS的文件路径，要以hdfs://开头，hadoop01是服务器地址，9000是hdfs的端口

### 4.从HDFS下载文件到本地路径

从HDFS中下载指定文件，如果本地文件与要下载的文件名称相同，则自动对下载的文件重命名；

> Shell

```
hadoop fs -get /1130182/readme.txt ~/
cat ~/readme.txt
```

> Python

```
fs.copy_files('hdfs://hadoop01:9000/1130182/readme.txt','file:///home/hadoop/code/readme.txt')

```

### 5.打印文件内容

将HDFS中指定文件的内容输出到终端中；

> Shell

```
hadoop fs -cat /1130182/readme.txt 
```

> Python

```
with hdfs.open_input_file("/1130182/readme.txt") as f:
    print(f.readall())
```

### 6.显示文件属性

显示HDFS中指定的文件的读写权限、大小、创建时间、路径等信息；

> Shell

```
hadoop fs -ls  /1130182/readme.txt
```

> Python

```
fileinfo = hdfs.get_file_info('/1130182/readme.txt')
print(fileinfo)
```

### 7.删除文件

* 删除HDFS中指定的文件；

```
hadoop fs -rm  /1130182 
```

> Python

```
hdfs.delete_file('/1130182/readme.txt')
```

### 8.完整的Python文件

```
from pyarrow import fs

# 初始化，hadoop01为主机名，9000为端口号
hdfs = fs.HadoopFileSystem(host="hadoop01",port=9000)

# 在HDSF上创建一个目录
hdfs.create_dir('/1130182')

# 将本地一个文件上传到HDFS
fs.copy_files('file:///usr/local/hadoop/README.txt','hdfs://hadoop01:9000/1130182/readme.txt')

# 将HDFS的文件下载到本地
fs.copy_files('hdfs://hadoop01:9000/1130182/readme.txt','file:///home/hadoop/code/readme.txt')

# 打印HDFS上指定文件的内容
with hdfs.open_input_file("/1130182/readme.txt") as f:
    print(f.readall())

# 显示HDFS文件的属性
fileinfo = hdfs.get_file_info('/1130182/readme.txt')
print(fileinfo)

# 删除指定文件
hdfs.delete_file('/1130182/readme.txt')
```

## 参考资料

* https://www.w3cschool.cn/hadoop/hadoop\_hdfs\_operations.html
* https://arrow.apache.org/docs/python/generated/pyarrow.fs.HadoopFileSystem.html#pyarrow.fs.HadoopFileSystem.delete\_dir
* http://hadoop.apache.org/docs/r3.3.1/hadoop-project-dist/hadoop-common/FileSystemShell.html
* http://hadoop.apache.org/docs/r1.0.4/cn/hdfs\_shell.html
