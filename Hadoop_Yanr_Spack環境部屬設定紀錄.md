### Hadoop/Yanr/Spack環境部屬設定紀錄

需啟動才看的到：

1.  Hadoop DataNode、NameNode Web UI：10.0.0.207/50070
    
    ```c
    start-all.sh
    ```
    
2.  (以此為主Yarn) Hadoop Yarn Web UI 查看 pyspark：10.0.0.207/8088
    
    ```c
    PYSPARK_DRIVER_PYTHON=ipython PYSPARK_DRIVER_PYTHON_OPTS="notebook" HADOOP_CONF_DIR=/usr/local/hadoop/etc/hadoop MASTER=yarn-client pyspark
    ```
    
![jupyter啟動.png](https://github.com/Axel1227/Hadoop_Yanr_Spack-/blob/main/img/jupyter%E5%95%9F%E5%8B%95.png)

![Spark啟動.png](https://github.com/Axel1227/Hadoop_Yanr_Spack-/blob/main/img/Spark%E5%95%9F%E5%8B%95.png)

### (一)、Hadoop環境安裝設定(含重點)

```
(1). VitualBox  Ubuntu16.04LTS  JAVA8
(2). NameNode → master (192.168.56.100)
(3). DataNode → data1/data2/data3 (192.168.56.101＆102＆103)
(4). 以上須注意不可安裝JAVA7版本，必須裝JAVA8，JAVA7太舊，會不支援後續的Spark安裝程序。
(5). dsa key做完多做rsa，不做在後面start-all.sh會錯誤。
(6). Ubuntu16版本和14版IP設定不同，必須注意介面卡1為enp0s3，介面卡2為enp0s8，需特別注意。

重開機 → shutdown -r now 

機器配備：
usermaster → CPU-8/RAM-16G
userdata1 → CPU*2/RAM-8G
userdata2 → CPU*8/RAM-6G

網址
10.0.0.207:50070
10.0.0.207:8088
10.0.0.207:8080

ERROR→WARN yarn.Client: Neither spark.yarn.jars nor spark.yarn.archive is set,

falling back to uploading libraries under SPARK_HOME.

<https://lovebear.top/2020/02/23/Spark_Yarn_Warning_Sparkjars/>

ERROR→Unable to load native-hadoop library for your platform... 
using builtin-java classes where applicable

<https://blog.csdn.net/xiaohutong1991/article/details/108532275>
pyspark --master yarn --deploy-mode client
```

### (二)、安裝JAVA、check路徑：

```
java -version
sudo apt-get update
sudo apt-get install openjdk-8-jdk
java -version
update-alternatives --display java
```

### (三)、SSH無密碼登入：

```
(SSH無密碼登入)      (dsa)
sudo apt-get install ssh
sudo apt-get install rsync
ssh-keygen -t dsa -P '' -f ~/.ssh/id_dsa
ll /home/hduser/.ssh
ll ~/.ssh
cat ~/.ssh/id_dsa.pub >> ~/.ssh/authorized_keys

(SSH無密碼登入)      (rsa)
ssh-keygen -t rsa -P ""
cat $HOME/.ssh/id_rsa.pub >> $HOME/.ssh/authorized_keys
(測試登入)           ssh localhost
```

### (四)、Hadoop下載及安裝：

```
(下載安裝Hadoop 2.10.0版本) Hadoop.apache.org/release.html

wget <http://apache.stu.edu.tw/hadoop/common/hadoop-2.10.0/hadoop-2.10.0.tar.gz>
sudo tar -zxvf hadoop-2.10.0.tar.gz
sudo mv hadoop-2.10.0 /usr/local/hadoop
ll /usr/local/hadoop
```

### (五)、設定環境變數：

```bash
sudo gedit ~/.bashrc

export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
export HADOOP_HOME=/usr/local/hadoop
export PATH=$PATH:$HADOOP_HOME/bin
export PATH=$PATH:$HADOOP_HOME/sbin
export HADOOP_MAPRED_HOME=$HADOOP_HOME
export HADOOP_COMMON_HOME=$HADOOP_HOME
export HADOOP_HDFS_HOME=$HADOOP_HOME
export YARN_HOME=$HADOOP_HOME
export HADOOP_COMMON_LIB_NATIVE_DIR=$HADOOP_HOME/lib/native
export HADOOP_OPTS="-Djava.library.path=$HADOOP_HOME/lib"
export JAVA_LIBRARY_PATH=$HADOOP_HOME/lib/native:$JAVA_LIBRARY_PATH

source ~/.bashrc
```

### (六)、修改hadoop_env.sh：

```bash
sudo gedit /usr/local/hadoop/etc/hadoop/hadoop-env.sh

export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64

```

### (七)、修改core-site.xml：

```xml
sudo gedit /usr/local/hadoop/etc/hadoop/core-site.xml

<property>
   <name>fs.default.name</name>
   <value>hdfs://localhost:9000</value>
</property>
```

### (八)、修改yarn-site.xml：

```xml
sudo gedit /usr/local/hadoop/etc/hadoop/yarn-site.xml

<property>
   <name>yarn.nodemanager.aux-services</name>
   <value>mapreduce_shuffle</value>
</property>
<property>
   <name>yarn.nodemanager.aux-services.mapreduce.shuffle.class</name>
   <value>org.apache.hadoop.mapred.ShuffleHandler</value>
</property>
<property>
  <name>yarn.nodemanager.vmem-check-enabled</name>
  <value>false</value>
</property>
```

### (九)、修改mapred-site.xml：

```xml
複製mapred-site.xmltemplate樣板如下：
sudo cp /usr/local/hadoop/etc/hadoop/mapred-site.xml.template /usr/local/hadoop/etc/hadoop/mapred-site.xml

sudo gedit /usr/local/hadoop/etc/hadoop/mapred-site.xml

<property>
   <name>mapreduce.framework.name</name>
   <value>yarn</value>
</property>
```

### (十)、修改hdfs-site.xml：

```xml
sudo gedit /usr/local/hadoop/etc/hadoop/hdfs-site.xml 

<property>
   <name>dfs.replication</name>
   <value>3</value>
</property>
<property>
   <name>dfs.datanode.data.dir</name>
   <value>file:/usr/local/hadoop/hadoop_data/hdfs/datanode</value>
</property>
```

### (十一)、建立與格式化HDFS 目錄：

```
sudo mkdir -p /usr/local/hadoop/hadoop_data/hdfs/namenode
sudo mkdir -p /usr/local/hadoop/hadoop_data/hdfs/datanode
sudo chown hduser:hduser -R /usr/local/hadoop
hadoop namenode -format
```

### (十二)、啟動Hadoop：

```
start-all.sh
jps
```

### (十三)、開啟Hadoop Resource­Manager Web介面：

```
<http://localhost:8088/>
```

### (十四)、NameNode HDFS Web介面：

```
<http://localhost:50070/>
```

## 以上為單機器設定Hadoop，以下開始為多叢集設定

### (十五)、編輯網路設定檔設定固定IP：

```bash
sudo gedit /etc/network/interfaces

# interfaces(5) file used by ifup(8) and ifdown(8)
auto lo
iface lo inet loopback
auto enp0s3
iface enp0s3inet dhcp
auto enp0s8
iface enp0s8 inet static
address         192.168.56.101
netmask         255.255.255.0
network         192.168.56.0
broadcast       192.168.56.255

```

### (十六)、設定hostname：

```
sudo gedit /etc/hostname

data1
```

### (十七)、設定hosts檔案：

```
sudo gedit /etc/hosts

192.168.56.100 master
192.168.56.101 data1
192.168.56.102 data2
192.168.56.103 data3
```

### (十八)、修改core-site.xml：

```xml
sudo gedit /usr/local/hadoop/etc/hadoop/core-site.xml

<property>
   <name>fs.default.name</name>
   <value>hdfs://master:9000</value>
</property>
```

### (十九)、修改yarn-site.xml：

```xml
sudo gedit /usr/local/hadoop/etc/hadoop/yarn-site.xml

<property>
     <name>yarn.resourcemanager.resource-tracker.address</name>
     <value>master:8025</value>
</property>
<property>
     <name>yarn.resourcemanager.scheduler.address</name>
     <value>master:8030</value>
</property>
<property>
     <name>yarn.resourcemanager.address</name>
     <value>master:8050</value>
</property>
<property>
        <name>yarn.nodemanager.pmem-check-enabled</name>
        <value>false</value>
</property>
<property>
        <name>yarn.nodemanager.vmem-check-enabled</name>
        <value>false</value>
</property>

#說明
yarn.nodemanager.pmem-check-enabled
啟動一個執行緒檢查每個任務正使用實體記憶體量，如果任務超出分配值，則直接將其殺掉，預設是true。
yarn.nodemanager.vmem-check-enabled
啟動一個執行緒檢查每個任務正使用虛擬記憶體量，如果任務超出分配值，則直接將其殺掉，預設是true。
```

### (二十)、修改mapred-site.xml：

```xml
sudo gedit /usr/local/hadoop/etc/hadoop/mapred-site.xml

<property>
   <name>mapred.job.tracker</name>
   <value>master:54311</value>
</property>
```

### (二一)、修改hdfs-site.xml：

```xml
sudo gedit /usr/local/hadoop/etc/hadoop/hdfs-site.xml

<property>
   <name>dfs.replication</name>
   <value>3</value>
</property>
<property>
   <name>dfs.datanode.data.dir</name>
   <value> file:/usr/local/hadoop/hadoop_data/hdfs/datanode</value>
</property>
```

### (二二)、確認網路設定：

```xml
ifconfig
```

### (二三)、複製data1伺服器至data2、data3、master：

```
sudo gedit /etc/network/interfaces

data2 -> 192.168.56.102
data3 -> 192.168.56.103
master -> 192.168.56.100
```

### (二四)、主機名稱更改：

```
sudo gedi /etc/hostname

data2
data3
master
```

### (二五)、master連線至data1、data2、data3建立HDFS目錄：

```
ssh data1
sudo rm -rf /usr/local/hadoop/hadoop_data/hdfs
sudo mkdir -p /usr/local/hadoop/hadoop_data/hdfs/datanode
sudo chown hduser:hduser -R /usr/local/hadoop
exit

ssh data2
sudo rm -rf /usr/local/hadoop/hadoop_data/hdfs
sudo mkdir -p /usr/local/hadoop/hadoop_data/hdfs/datanode
sudo chown hduser:hduser -R /usr/local/hadoop
exit

ssh data3
sudo rm -rf /usr/local/hadoop/hadoop_data/hdfs
sudo mkdir -p /usr/local/hadoop/hadoop_data/hdfs/datanode
sudo chown hduser:hduser -R /usr/local/hadoop
exit
```

### (二六)、建立與格式化NameNode HDFS 目錄：

```
sudo rm -rf /usr/local/hadoop/hadoop_data/hdfs
mkdir -p /usr/local/hadoop/hadoop_data/hdfs/namenode
sudo chown -R hduser:hduser /usr/local/hadoop
hadoop namenode -format
```

### (二七)、啟動Hadoop、cheak行程：

```
start-all.sh
jps
```

### (二八)、Hadoop Resource Manager、NameNode Web UI：

```
<http://master:8088/>
<http://master:50070/>
```

### (二九)、下載安裝Scala：

```
wget <http://www.scala-lang.org/files/archive/scala-2.12.10.tgz>
tar xvf scala-2.12.10.tgz
sudo mv scala-2.12.10 /usr/local/scala
```

### (三十)、修改scala環境變數：

```bash
sudo gedit ~/.bashrc

export SCALA_HOME=/usr/local/scala
export PATH=$PATH:$SCALA_HOME/bin

source ~/.bashrc
```

### (三一)、下載安裝Spark：

```bash
wget [<http://apache.stu.edu.tw/spark/spark-2.4.6/spark-2.4.6-bin-hadoop2.7.tgz>](<http://apache.stu.edu.tw/spark/spark-2.4.6/spark-2.4.6-bin-hadoop2.7.tgz>)
tar zxf spark-2.4.6-bin-hadoop2.7.tgz
sudo mv spark-2.4.6-bin-hadoop2.7 /usr/local/spark/
```

### (三二)、修改spark環境變數

```bash
sudo gedit ~/.bashrc

export SPARK_HOME=/usr/local/spark
export PATH=$PATH:$SPARK_HOME/bin

source ~/.bashrc
```

### (三三)、設定pyspark 顯示訊息：

```bash
cd /usr/local/spark/conf
cp log4j.properties.template log4j.properties
```

### (三四)、修改log4j.properties：

```bash
sudo gedit log4j.properties

原本是INFO改為WARN
```

### SPARK啟動

```
/usr/local/spark/sbin/start-all.sh

本機模式
pyspark --master local[*]
YARN模式
HADOOP_CONF_DIR=/usr/local/hadoop/etc/hadoop pyspark --master yarn --deploy-mode client
pyspark --master yarn --deploy-mode client
SPARK Standalone Cluster模式
pyspark --master spark://master:7077

模式查看：
sc.master
```

### 節點訊息查看

```jsx
hdfs dfsadmin -report
```

### usermaster、userdata1、userdata2設定

```bash
usermaster、userdata1、userdata2
環境變數設定
sudo vi ~/.bashrc
```
![usermaster.png](https://github.com/Axel1227/Hadoop_Yanr_Spack-/blob/main/img/usermaster.png)
![usermaster_2.png](https://github.com/Axel1227/Hadoop_Yanr_Spack-/blob/main/img/usermaster_2.png)
![usermaster_3.png](https://github.com/Axel1227/Hadoop_Yanr_Spack-/blob/main/img/usermaster_3.png)

```bash
usermaster、userdata1、userdata2
core-site.xml設定
sudo vi /usr/local/hadoop/etc/hadoop/hadoop-env.sh
```

![env.png](https://github.com/Axel1227/Hadoop_Yanr_Spack-/blob/main/img/env.png)

```bash
usermaster、userdata1、userdata2
core-site.xml設定
sudo vi /usr/local/hadoop/etc/hadoop/core-site.xml
```
![core-site-1.png](https://github.com/Axel1227/Hadoop_Yanr_Spack-/blob/main/img/core-site-1.png)
![core-site-2.png](https://github.com/Axel1227/Hadoop_Yanr_Spack-/blob/main/img/core-site-2.png)
![core-site-3.png](https://github.com/Axel1227/Hadoop_Yanr_Spack-/blob/main/img/core-site-1.png)

```bash
usermaster、userdata1、userdata2
yarn-site.xml設定
sudo vi /usr/local/hadoop/etc/hadoop/yarn-site.xml
```

![yarn-site-1.png](https://github.com/Axel1227/Hadoop_Yanr_Spack-/blob/main/img/yarn-site-1.png)
![yarn-site-2.png](https://github.com/Axel1227/Hadoop_Yanr_Spack-/blob/main/img/yarn-site-2.png)
![yarn-site-3.png](https://github.com/Axel1227/Hadoop_Yanr_Spack-/blob/main/img/yarn-site-3.png)

```bash
usermaster、userdata1、userdata2
mapred-site.xml設定
sudo vi /usr/local/hadoop/etc/hadoop/mapred-site.xml
```

![mapred-site-1.png](https://github.com/Axel1227/Hadoop_Yanr_Spack-/blob/main/img/mapred-site-1.png)
![mapred-site-2.png](https://github.com/Axel1227/Hadoop_Yanr_Spack-/blob/main/img/mapred-site-2.png)
![mapred-site-3.png](https://github.com/Axel1227/Hadoop_Yanr_Spack-/blob/main/img/mapred-site-3.png)

```bash
usermaster、userdata1、userdata2
hdfs-site.xml設定
sudo vi /usr/local/hadoop/etc/hadoop/hdfs-site.xml
```

![hdfs-site-1.png](https://github.com/Axel1227/Hadoop_Yanr_Spack-/blob/main/img/hdfs-site-1.png)
![hdfs-site-2.png](https://github.com/Axel1227/Hadoop_Yanr_Spack-/blob/main/img/hdfs-site-2.png)
![hdfs-site-3.png](https://github.com/Axel1227/Hadoop_Yanr_Spack-/blob/main/img/hdfs-site-3.png)

```bash
usermaster、userdata1、userdata2
網路設定檔設定
sudo vi /etc/network/interfaces
```

![interfaces-1.png](https://github.com/Axel1227/Hadoop_Yanr_Spack-/blob/main/img/interfaces-1.png)

```bash
usermaster、userdata1、userdata2
主機名稱設定
sudo vi /etc/hostname
```

![hostname.png](https://github.com/Axel1227/Hadoop_Yanr_Spack-/blob/main/img/hostname.png)

```bash
usermaster、userdata1、userdata2
主機名稱設定
sudo vi /etc/hosts
```

![hosts-1.png](https://github.com/Axel1227/Hadoop_Yanr_Spack-/blob/main/img/hosts-1.png)
![hosts-2.png](https://github.com/Axel1227/Hadoop_Yanr_Spack-/blob/main/img/hosts-2.png)
![hosts-3.png](https://github.com/Axel1227/Hadoop_Yanr_Spack-/blob/main/img/hosts-3.png)

