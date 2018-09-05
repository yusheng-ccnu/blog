---
title: hadoop伪分布式安装过程
date: 2018-09-04 14:48:31
tags: 
- hadoop
- 大数据
- 分布式计算

categories: 
- hadoop
---

### hadoop 环境搭建
#### 搭建的软件版本
- ubuntu 16.04
- hadoop 2.6.5
- java 1.8

在搭建hadoop环境之前，必须搭建好java的开发环境。
#### java环境
- 在java官网上下载合适版本的jdk

```
https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html
```

- 解压java安装包

```
tar -zxvf jdk-8u181-linux-x64.tar.gz 
```
在/etc/profile中添加如下内容：

```
export JAVA_HOME=/home/hadoop/jdk1.8.0_181  //此处替换jdk安装路径
export JAVA_JRE=${JAVA_HOME}/jre
export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib
export PATH=${JAVA_HOME}/bin:$PATH

```

#### hadoop环境搭建
##### 创建hadoop用户
```
sudo useradd -m hadoop   //创建hadoop用户 
sudo passwd hadoop          //为hadoop用户设置密码
sudo adduser  hadoop sudo  //为hadoop用户添加管理元权限
```
##### 安装ssh，配置无密登录
```
sudo apt-get install openssh-server
```
- 首次登录，可能需要输入密码才可以，但是会在用户文件夹下面创建.ssh的文件夹


```
 ssh localhost 
```

进入用户的.ssh文件夹下


```
cd ~/.ssh/                     # 若没有该目录，请先执行一次ssh localhost
ssh-keygen -t rsa              # 会有提示，都按回车就可以
cat ./id_rsa.pub >> ./authorized_keys  # 加入授权
```
此时再登录localhost就不用再输入密码。

##### 在hadoop官网下载合适的版本，这里下载的hadoop2.6.5，下载地址：
```
http://mirror.bit.edu.cn/apache/hadoop/common/
```
- 解压

```
tar -zxvf hadoop-2.6.5.tar.gz
```
重命名解压后的文件夹并给文件夹的权限给hadoop用户
```
mv ./hadoop-2.6.5/ ./hadoop
sudo chown hadoop ./hadoop
```
进入hadoop安装目录
```
cd /home/hadoop/hadoop
./bin/hadoop version
```
便可以看到hadoop的版本

##### hadoop单击配置（非分布式）

非分布式的环境下无需进行其他配置，可以直接跑hadoop里面附带的例子
```
cd /home/hadoop/hadoop
mkdir ./input
cp ./etc/hadoop/*.xml ./input   # 将配置文件作为输入文件
./bin/hadoop jar ./share/hadoop/mapreduce/hadoop-mapreduce-examples-*.jar grep ./input ./output 'dfs[a-z.]+'
cat ./output/*  
```
##### hadoop 伪分布式环境配置
hadoop的配置文件再hadoo安装目录下的/etc/hadoop目录下面，如在我的环境里就是  /home/hadoop/hadoop/etc/hadoop下面

- 修改core-site.xml配置文件

```
<configuration>
        <property>
             <name>hadoop.tmp.dir</name>
             <value>file:/home/hadoop/hadoop/tmp</value>
             <description>Abase for other temporary directories.</description>
        </property>
        <property>
             <name>fs.defaultFS</name>
             <value>hdfs://localhost:9000</value>
        </property>
</configuration>
```
- 同样还得修改hdfs-site.xml文件

```
<configuration>
        <property>
             <name>dfs.replication</name>
             <value>1</value>
        </property>
        <property>
             <name>dfs.namenode.name.dir</name>
             <value>file:/usr/local/hadoop/tmp/dfs/name</value>
        </property>
        <property>
             <name>dfs.datanode.data.dir</name>
             <value>file:/usr/local/hadoop/tmp/dfs/data</value>
        </property>
</configuration>
```
> **hadoop配置文件说明**
Hadoop 的运行方式是由配置文件决定的（运行 Hadoop 时会读取配置文件），因此如果需要从伪分布式模式切换回非分布式模式，需要删除 core-site.xml 中的配置项。

> 此外，伪分布式虽然只需要配置 fs.defaultFS 和 dfs.replication 就可以运行（官方教程如此），不过若没有配置 hadoop.tmp.dir 参数，则默认使用的临时目录为 /tmp/hadoo-hadoop，而这个目录在重启时有可能被系统清理掉，导致必须重新执行 format 才行。所以我们进行了设置，同时也指定 dfs.namenode.name.dir 和 dfs.datanode.data.dir，否则在接下来的步骤中可能会出错。

配置完成之后需要格式化
```
./bin/hdfs namenode -format
```

格式化完成之后就可以启动hdfs
```
./sbin/start-dfs.sh
```
如果安装java并且配置了环境变量，但是在启动时还是报错：**Error: JAVA_HOME is not set and could not be found.** 则需要在hadoop/etc/hadoop（hadoop为hadoop安装）目录下修改hadoop-env.sh文件，在里面加入
```
export JAVA_HOME=/home/hadoop/jdk1.8.0_181
```

