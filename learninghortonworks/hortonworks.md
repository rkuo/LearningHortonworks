# Hortonworks
[webinar library]: http://hortonworks.com/webinars/#library
[Using Apache Spark Technical Preview with HDP]: http://hortonworks.com/hadoop-tutorial/using-apache-spark-hdp/

This exercise follows [Using Apache Spark Technical Preview with HDP].

## Installation

[hortonworks image]: http://hortonworks.com/hdp/downloads/
[Installing a single-node Hadoop (v2.3.0)]: http://vangjee.wordpress.com/2014/03/05/installing-a-single-node-hadoop-v2-3-0-instance-with-yarn-using-vagrant/
[vagrant box install]: https://github.com/vangj/vagrant-hadoop-2.3.0/blob/master/Vagrantfile
[vagrant box]: https://vagrantcloud.com/emyl/boxes/hortonworks-sandbox-2.1/versions

- download new [hortonworks image] and import into virtualbox

- copy spark to Hortonworks-vm

```
~/Downloads scp -P 2222 spark-1.2.0-bin-hadoop2.4.tgz root@127.0.0.1:/root
root@127.0.0.1's password:
spark-1.2.0-bin-hadoop2.4.tgz                                                                                                 100%  209MB  19.0MB/s   00:11
```
untar the spark,

```
[root@sandbox ~]# tar xvfz spark-1.2.0-bin-hadoop2.4.tgz
spark-1.2.0-bin-hadoop2.4/
...[snip]...
```
Replace the first line in /etc/hosts with 
`127.0.0.1 localhost sandbox.hortonworks.com`

Set YARN environment variables,

```
[root@sandbox ~]# ls /etc/hadoop/conf
argus-security.xml          dfs_data_dir_mount.hist     hadoop-policy.xml  mapred-env.sh          taskcontroller.cfg          xasecure-policymgr-ssl.xml
capacity-scheduler.xml      dfs.exclude                 hdfs-site.xml      mapred-site.xml        task-log4j.properties       yarn-env.sh
commons-logging.properties  hadoop-env.sh               health_check       set-hdfs-agent-env.sh  xasecure-audit.xml          yarn.exclude
core-site.xml               hadoop-metrics2.properties  log4j.properties   slaves                 xasecure-hdfs-security.xml  yarn-site.xml
[root@sandbox ~]# export YARN_CONF_DIR=/etc/hadoop/conf
```

## Apache Ambari
[Apache Ambari: Managing Hadoop and YARN]: http://www.slideshare.net/hortonworks/ambari-yarnwebinarv7ls?qid=d9f008e0-717b-4603-bc86-f1158235a1dd&v=qf1&b=&from_search=10
[Using Apache Ambari to Manage Hadoop Clusters]: https://www.youtube.com/watch?v=uIAeGufvSXA
[start Ambari]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.2.0/bk_using_Ambari_book/content/ambari-chap2-3.html
[Get Started: Ambari for provisioning, managing and monitoring Hadoop]: http://hortonworks.com/kb/get-started-setting-up-ambari/

from root `sh ./start_ambari.sh`, or follow the instruction in [Start Ambari]

```
[root@sandbox ~]# sh ./start_ambari.sh
Starting Ambari...
=============================
Starting hdp-gmetad...
=============================
Started /usr/bin/rrdcached with PID 19120
Started /usr/sbin/gmetad with PID 19151

Starting Ganglia                                          [  OK  ]
Starting Nagios                                           [  OK  ]
Starting Ambari server                                    [  OK  ]
Starting Ambari agent                                     [  OK  ]
====================================

Ambari autostart enabled
To disable auto-start of Ambari do
  # chkconfig ambari off

====================================
[root@sandbox ~]#
```

### Ambari
Open a browser to access Ambari: AdministraLon Tool for Hadoop and YARN
use URL: `http://<VM IP address>:8080/#/login` 
Example: http://192.168.26.157:8080/#/login 
Username/Password: admin/admin

problem: hbase, flume, storm are not started

### Hadoop User Experience (HUE) 
HUE is a Web Interface to analyze data with Hadoopuse URL: `http://<VM IP Address>:8000/accounts/login/?next=/about/` 
Example: http://192.168.26.157:8000/accounts/login/?next=/about/ 
Login ID/Password: hue/1111

## Hadoop CLI
[Using the command line to manage files on HDFS]: http://hortonworks.com/hadoop-tutorial/using-commandline-manage-files-hdfs/

login `ssh -Y root@<VM IP Address>`, with password `hadoop`

-Y
Enables trusted X11 forwarding. Trusted X11 forwardings are not subjected to the X11 SECURITY extension controls.

There are good instruction of [Using the command line to manage files on HDFS], this is a short list of common commands. 

* ls – List directories/files

```hadoop fs -ls /hadoop fs -ls /user/hive
```* mkdir – Create new directory 
```
hadoop fs -mkdir /hdfs_test
```* copyFromLocal – Copy files from local **(it means vm local, not Macbook)** to HDFS 
```
hadoop fs -copyFromLocal weblog.data /hdfs_test
```
* put – Same as copyFromLocal 
```
hadoop fs -put weblog.data /hdfs_test
￼```

* copyToLocal – Copy file form hdfs to local

```hadoop fs -copyToLocal /hdfs_test/weblog.data weblog_local.data```
* get – Same as copyToLocal```
hadoop fs -get /hdfs_test/weblog.data weblog_local.data```
* cp – Copy files within hdfs
```
hadoop fs -cp /hdfs_test/weblog.data /hdfs_test/weblog1.data
```

* movefromLocal – Move files from local to hdfs
```
hadoop fs -moveFromLocal weblog.data /hdfs_test```
* rm –r – Remove files```
hadoop fs -rm -r /hdfs_test/weblog.data```

* tail – Display end of file```
hadoop fs -tail /hdfs_test/weblog1.data```

* cat – Display contents for a file(s) 

```
hadoop fs -cat /hdfs_test/weblog1.data
```

### Lab

create a directory, and view it from Hue,

```
[root@sandbox ~]# hadoop fs -mkdir /hdfs_test
[root@sandbox ~]# hadoop fs -ls /
Found 9 items
drwxrwxrwx   - yarn   hadoop          0 2014-10-29 12:57 /app-logs
drwxr-xr-x   - hdfs   hdfs            0 2014-10-29 13:36 /apps
drwxr-xr-x   - hdfs   hdfs            0 2014-10-29 13:30 /demo
drwxr-xr-x   - root   hdfs            0 2014-12-22 05:42 /hdfs_test
drwxr-xr-x   - hdfs   hdfs            0 2014-10-29 12:59 /hdp
drwxr-xr-x   - mapred hdfs            0 2014-10-29 12:57 /mapred
drwxr-xr-x   - hdfs   hdfs            0 2014-10-29 12:57 /mr-history
drwxrwxrwx   - hdfs   hdfs            0 2014-10-29 13:34 /tmp
drwxr-xr-x   - hdfs   hdfs            0 2014-10-29 13:35 /user
[root@sandbox ~]#
```
create two local files, put/copy one file to Hadoop cluster. Both files are still on Macbook, just one file is under Hadoop's management.

```
[root@sandbox ~]# touch txt1 txt2			            # local
[root@sandbox ~]# hadoop fs -put txt1 /hdfs_test/      # copy to Hadoop cluster            
[root@sandbox ~]# ls						            # list local
anaconda-ks.cfg  install.log         spark-1.2.0-bin-hadoop2.4  start_hbase.sh  stop_solr.sh  txt2
argus_tutorial   install.log.syslog  start_ambari.sh            start_solr.sh   txt1
[root@sandbox ~]# hadoop fs -ls /hdfs_test/	        # list Hadoop directory
Found 1 items
-rw-r--r--   1 root hdfs          0 2014-12-22 11:56 /hdfs_test/txt1
[root@sandbox ~]#
```

[file browser in Hue]: https://www.evernote.com/shard/s302/sh/aebdc9af-1cab-468d-90e4-2c3f4b38fa44/02313892d9c7e03798dfd851dead80af/deep/0/File-Browser.png

We can also view this in ![file browser in Hue].

## Prepare the Spark Lab
In Hadoop-hdfs cluster terminal, 

```
[root@sandbox ~]# hadoop fs -mkdir /spark_lab
```

In Macbook local terminal, copy file to hdfs cluster; 

```
~/Projects/spark/ampcamp5/ampcamp5-usb scp -P 2222 hamlet.txt root@127.0.0.1:/root/spark-1.2.0-bin-hadoop2.4/hamlet.txt
root@127.0.0.1's password:
hamlet.txt                                                                                                                        100%   19     0.0KB/s   00:00
```

-P port
Specifies the port to connect to on the remote host. Note that this option is written with a **capital** 'P', because -p is already reserved for preserving the times and modes of the file in rcp(1).

## Run WordCount from spark-shell
### use hamlet.txt 

```
scala> val lines = sc.textFile("hamlet.txt")
14/12/25 06:11:10 INFO MemoryStore: ensureFreeSpace(81443) called with curMem=372842, maxMem=278302556
14/12/25 06:11:10 INFO MemoryStore: Block broadcast_2 stored as values in memory (estimated size 79.5 KB, free 265.0 MB)
14/12/25 06:11:11 INFO MemoryStore: ensureFreeSpace(31262) called with curMem=454285, maxMem=278302556
14/12/25 06:11:11 INFO MemoryStore: Block broadcast_2_piece0 stored as bytes in memory (estimated size 30.5 KB, free 264.9 MB)
14/12/25 06:11:11 INFO BlockManagerInfo: Added broadcast_2_piece0 in memory on localhost:51512 (size: 30.5 KB, free: 265.3 MB)
14/12/25 06:11:11 INFO BlockManagerMaster: Updated info of block broadcast_2_piece0
14/12/25 06:11:11 INFO SparkContext: Created broadcast 2 from textFile at <console>:12
lines: org.apache.spark.rdd.RDD[String] = hamlet.txt MappedRDD[5] at textFile at <console>:12

scala> lines.count()
14/12/25 06:11:15 INFO FileInputFormat: Total input paths to process : 1
...[snip]...
14/12/25 06:11:17 INFO TaskSchedulerImpl: Removed TaskSet 0.0, whose tasks have all completed, from pool
14/12/25 06:11:17 INFO DAGScheduler: Job 0 finished: count at <console>:15, took 1.769083 s
res1: Long = 2

```
Get final result,

```
scala> val wordCounts = lines.flatMap(line => line.split(" ")).map(word => (word, 1)).reduceByKey((a, b) => a + b)
14/12/25 06:20:01 INFO BlockManager: Removing broadcast 3
14/12/25 06:20:01 INFO BlockManager: Removing block broadcast_3_piece0
14/12/25 06:20:01 INFO MemoryStore: Block broadcast_3_piece0 of size 1866 dropped from memory (free 277814497)
14/12/25 06:20:01 INFO BlockManagerInfo: Removed broadcast_3_piece0 on localhost:51512 in memory (size: 1866.0 B, free: 265.3 MB)
14/12/25 06:20:01 INFO BlockManagerMaster: Updated info of block broadcast_3_piece0
14/12/25 06:20:01 INFO BlockManager: Removing block broadcast_3
14/12/25 06:20:01 INFO MemoryStore: Block broadcast_3 of size 2512 dropped from memory (free 277817009)
14/12/25 06:20:01 INFO ContextCleaner: Cleaned broadcast 3
wordCounts: org.apache.spark.rdd.RDD[(String, Int)] = ShuffledRDD[8] at reduceByKey at <console>:14

scala> wordCounts.collect()
14/12/25 06:20:13 INFO SparkContext: Starting job: collect at <console>:17
14/12/25 06:20:13 INFO DAGScheduler: Registering RDD 7 (map at <console>:14)
14/12/25 06:20:13 INFO DAGScheduler: Got job 1 (collect at <console>:17) with 3 output partitions (allowLocal=false)
14/12/25 06:20:13 INFO DAGScheduler: Final stage: Stage 2(collect at <console>:17)
14/12/25 06:20:13 INFO DAGScheduler: Parents of final stage: List(Stage 1)
14/12/25 06:20:13 INFO DAGScheduler: Missing parents: List(Stage 1)
...[snip]...
14/12/25 06:20:14 INFO DAGScheduler: Stage 2 (collect at <console>:17) finished in 0.348 s
14/12/25 06:20:14 INFO TaskSchedulerImpl: Removed TaskSet 2.0, whose tasks have all completed, from pool
14/12/25 06:20:14 INFO DAGScheduler: Job 1 finished: collect at <console>:17, took 0.983887 s
res4: Array[(String, Int)] = Array((or,1), (not,1), (be,2), (to,2))

scala> res4.foreach(println)
(or,1)
(not,1)
(be,2)
(to,2)
```

**Not** in `spark-shell`, in terminal run `spark-submit`

```
[root@sandbox spark-1.2.0-bin-hadoop2.4]# export YARN_CONF_DIR=/etc/hadoop/conf
[root@sandbox spark-1.2.0-bin-hadoop2.4]# ./bin/spark-submit --class org.apache.spark.examples.SparkPi    --master yarn-cluster  --num-executors 3 --driver-memory 512m  --executor-memory 512m   --executor-cores 1  lib/spark-examples*.jar 10
Spark assembly has been built with Hive, including Datanucleus jars on classpath
14/12/25 10:05:01 WARN util.NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
14/12/25 10:05:02 INFO client.RMProxy: Connecting to ResourceManager at sandbox.hortonworks.com/10.0.2.15:8050
...[snip]...
14/12/25 10:07:21 INFO yarn.Client: Application report for application_1419479162634_0001 (state: RUNNING)
14/12/25 10:07:22 INFO yarn.Client: Application report for application_1419479162634_0001 (state: FINISHED)
14/12/25 10:07:22 INFO yarn.Client:
	 client token: N/A
	 diagnostics: N/A
	 ApplicationMaster host: sandbox.hortonworks.com
	 ApplicationMaster RPC port: 0
	 queue: default
	 start time: 1419501929445
	 final status: SUCCEEDED
	 tracking URL: http://sandbox.hortonworks.com:8088/proxy/application_1419479162634_0001/A
	 user: root
[root@sandbox spark-1.2.0-bin-hadoop2.4]#
```

If you put tracking URL in browser, you will see display. ![hadoop console] 

But in my case, it did not work, I have to replace `sandbox.hortonworks.com` with `localhost`.

[hadoop console]: https://www.evernote.com/shard/s302/sh/0592e3a6-97d7-4e71-9ad6-eb5ff0985566/cea44b492955b6bdb43e398beaacbcb8/deep/0/localhost-8088-cluster-app-application_1419479162634_0001-A.png

### use log file

In vm terminal,- Copy a text file into hadoop hdfs
```
[root@sandbox spark-1.2.0-bin-hadoop2.4]# hadoop fs -copyFromLocal /etc/hadoop/conf/log4j.properties /tmp/data
[root@sandbox spark-1.2.0-bin-hadoop2.4]# hadoop fs -ls /tmp/data
-rw-r--r--   1 root hdfs       8058 2014-12-25 12:11 /tmp/data
```- Run Wordcount from spark-shell```
[root@sandbox spark-1.2.0-bin-hadoop2.4]# ./bin/spark-shell
Spark assembly has been built with Hive, including Datanucleus jars on classpath
...[snip]...
```
pdf file uses different double quote “”, need to change it to ""```
scala> val file = sc.textFile("hdfs://sandbox.hortonworks.com:8020/tmp/data")
14/12/25 12:20:33 INFO MemoryStore: ensureFreeSpace(163705) called with curMem=0, maxMem=278302556
14/12/25 12:20:33 INFO MemoryStore: Block broadcast_0 stored as values in memory (estimated size 159.9 KB, free 265.3 MB)
14/12/25 12:20:34 INFO MemoryStore: ensureFreeSpace(22692) called with curMem=163705, maxMem=278302556
14/12/25 12:20:34 INFO MemoryStore: Block broadcast_0_piece0 stored as bytes in memory (estimated size 22.2 KB, free 265.2 MB)
14/12/25 12:20:34 INFO BlockManagerInfo: Added broadcast_0_piece0 in memory on localhost:33171 (size: 22.2 KB, free: 265.4 MB)
14/12/25 12:20:34 INFO BlockManagerMaster: Updated info of block broadcast_0_piece0
14/12/25 12:20:34 INFO SparkContext: Created broadcast 0 from textFile at <console>:12
file: org.apache.spark.rdd.RDD[String] = hdfs://sandbox.hortonworks.com:8020/tmp/data MappedRDD[1] at textFile at <console>:12

scala> val counts = file.flatMap(line => line.split(" ")).map(word => (word, 1)).reduceByKey(_ + _)
14/12/25 12:24:41 INFO FileInputFormat: Total input paths to process : 1
counts: org.apache.spark.rdd.RDD[(String, Int)] = ShuffledRDD[4] at reduceByKey at <console>:14
```
Save the result,

```
scala> counts.saveAsTextFile("hdfs://sandbox.hortonworks.com:8020/tmp/wordcount")
14/12/25 12:26:49 INFO deprecation: mapred.tip.id is deprecated. Instead, use mapreduce.task.id
14/12/25 12:26:49 INFO deprecation: mapred.task.id is deprecated. Instead, use mapreduce.task.attempt.id
...[snip]...
14/12/25 12:26:58 INFO TaskSchedulerImpl: Removed TaskSet 1.0, whose tasks have all completed, from pool
14/12/25 12:26:58 INFO DAGScheduler: Job 0 finished: saveAsTextFile at <console>:17, took 8.443665 s
```
The file can be viewed in Hue web gui, see ![wordcount result part-1]

[wordcount result part-1]: https://www.evernote.com/shard/s302/sh/9c39194a-0ed9-4661-a724-6af479b87496/afc3904d9066806ba17f02e2310e1958/deep/0/part-00000---File-Viewer.png

