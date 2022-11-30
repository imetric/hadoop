# 实验4、Spark安装部署与编程

## 一、实验目的
1.	通过实验掌握基本的Spark编程方法；



## 二、实验平台

1. 操作系统：Linux
2. Hadoop版本：3.0.0或以上版本
3. Python版本：3.7或以上版本4. Java IDE：Eclipse

## 三、实验内容和要求

### 1 部署Spark集群

#### 1.1 安装Spark所需环境
安装Scala

在每台服务器都需要安装Scala，
1. 使用命令sudo apt install scala进行在线安装scala
2. 使用命令scala –version检查scala是否安装成功
3. 使用命令which scala查看scala的安装位置路径
4. 与JDK一样，使用命令sudo vi /etc/profile 修改环境变量的配置文件,之后使用命令 source /etc/profile，使文件修改生效。增加
5. 
```
export SCALA_HOME=/usr/bin/scala
export PATH=$PATH:$SCALA_HOME/bin
```

#### 1.2 下载Spark
在hadoop01节点上，执行以下操作：
```
wget https://mirrors.bfsu.edu.cn/apache/spark/spark-3.2.0/spark-3.2.0-bin-hadoop3.2.tgz
sudo tar -zxvf spark-3.2.0-bin-hadoop3.2.tgz -C /usr/local/
cd /usr/local
sudo mv  spark-3.2.0-bin-hadoop3.2    spark #重命名为 spark
sudo chown -R hadoop ./spark                        #修改文件权限
```
#### 1.3 配置Spark
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
hadoop02
hadoop03
```

#### 1.4 将Spark文件拷贝到其它节点
```
scp -r /usr/local/spark root@hadoop02:/usr/local
scp -r /usr/local/spark root@hadoop03:/usr/local

```
分别修改hadoop02、hadoop03中spark目录的权限，分别登录hadoop02、hadoop03，分别执行
```
sudo chown -R hadoop ./spark 
```



### 2 使用Spark Shell编写代码

#### 2.1进入Spark Shell

```
cd /usr/local/spark/
bin/spark-shell
```

#### 2.2 加载text文件
spark创建sc，可以加载本地文件和HDFS文件创建RDD。这里用Spark自带的本地文件README.md文件测试。
```
scala>val textFile=sc.textFile("file:///usr/local/spark/README.md")
```
加载HDFS文件和本地文件都是使用textFile，区别是添加前缀(hdfs://和file://)进行标识。

#### 2.3 wordcount实例
```
val textFile = sc.textFile("hdfs://...")
val counts = textFile.flatMap(line => line.split(" "))
                 .map(word => (word, 1))
                 .reduceByKey(_ + _)
counts.saveAsTextFile("hdfs://...")
```
#### 2.4 Pi 估算
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
