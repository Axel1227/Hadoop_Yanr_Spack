# Hadoop_Yarn_Spark環境部署設定紀錄

目標：部署及設定Hadoop、Yarn、Spark分散式運算環境。
環境設定：三台伺服器(1台NameNode、兩台DataNode)

---

說明：

HDFS分散式檔案系統：透過Hadoop的HDFS進行資料的備份管理。資料分佈在各個DataNode存放，當其中一台DataNode損壞可由其他DataNode還原檔案，並由NameNode進行管理。

YARN資源管理平台：透過Hadoop的YARN模組進行排程與資源的分配。

Spark分散式框架：透過Spark分散式框架將取得的資料進行分散式運算。

---

應用：
請參考這篇 XML_Document_Classification_Method_Using_Veracity_Model

(https://github.com/Axel1227/XML_Document_Classification_Method_Using_Veracity_Model)


![jupyter啟動.png](https://github.com/Axel1227/Hadoop_Yanr_Spack-/blob/main/img/jupyter%E5%95%9F%E5%8B%95.png)

![Spark啟動.png](https://github.com/Axel1227/Hadoop_Yanr_Spack-/blob/main/img/Spark%E5%95%9F%E5%8B%95.png)

![hosts-1.png](https://github.com/Axel1227/Hadoop_Yanr_Spack-/blob/main/img/hosts-1.png)

