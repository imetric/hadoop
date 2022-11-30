# 实验4、Spark安装部署与编程

## 一、实验目的
1.	通过实验掌握基本的Spark编程方法；



## 二、实验平台

1. 操作系统：Linux
2. Hadoop版本：3.0.0或以上版本
3. Python版本：3.7或以上版本4. Java IDE：Eclipse

## 三、实验内容和要求

### 1 安装Spart所需环境Scala

#### 1.1 安装Scala

使用命令进行在线安装scala

```
sudo apt install scala
```

#### 1.2 配置环境变量

与JDK一样，使用以下命令，修改环境变量的配置文件。

```
sudo vi /etc/profile 
```
在/etc/profile文件中增加以下代码

```
export SCALA_HOME=/usr/bin/scala
export PATH=$PATH:$SCALA_HOME/bin
```
之后使用命令 source /etc/profile，使文件修改生效。

```
source /etc/profile
```

### 2 部署Spark集群


#### 2.1 下载Spark
在hadoop01节点上，执行以下操作：
```
wget https://mirrors.bfsu.edu.cn/apache/spark/spark-3.2.0/spark-3.2.0-bin-hadoop3.2.tgz
sudo tar -zxvf spark-3.2.0-bin-hadoop3.2.tgz -C /usr/local/
cd /usr/local
sudo mv  spark-3.2.0-bin-hadoop3.2    spark #重命名为 spark
sudo chown -R hadoop ./spark                        #修改文件权限
```
#### 2.2 配置Spark
配置spark-env.sh文件
```
cd /usr/local/spark
cp conf/spark-env.sh.template spark-env.sh
vi conf/spark-env.sh
```
将以下内容添加到spark-env.sh文件中
```
export SCALA_HOME=/usr/bin/scala
export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
export SPARK_MASTER_IP=hadoop01
export SPARK_MASTER_PORT=7077
```

配置slaves
```
vi conf/slaves
```
将以下内容添加到slaves文件中
```
hadoop01
```



### 3 使用Spark Shell编写代码

#### 3.1进入Spark Shell

```
cd /usr/local/spark/
bin/spark-shell
```

#### 3.2 加载text文件
spark创建sc，可以加载本地文件和HDFS文件创建RDD。这里用Spark自带的本地文件README.md文件测试。
```
scala>val textFile=sc.textFile("file:///usr/local/spark/README.md")
```
加载HDFS文件和本地文件都是使用textFile，区别是添加前缀(hdfs://和file://)进行标识。

#### 3.3 wordcount实例
```
val textFile = sc.textFile("hdfs://...")
val counts = textFile.flatMap(line => line.split(" "))
                 .map(word => (word, 1))
                 .reduceByKey(_ + _)
counts.saveAsTextFile("hdfs://...")
```
#### 3.4 Pi 估算
```
val count = sc.parallelize(1 to NUM_SAMPLES).filter { _ =>
  val x = math.random
  val y = math.random
  x*x + y*y < 1
}.count()
println(s"Pi is roughly ${4.0 * count / NUM_SAMPLES}")
```


## 参考资料

1. http://spark.apache.org/examples.html
