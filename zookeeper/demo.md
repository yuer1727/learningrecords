#zookeeper介绍
&nbsp;&nbsp;zookeeper是一个为分布式应用提供一致性服务的软件，它是开源的Hadoop项目中的一个子项目，并且根据google发表的《The Chubby lock service for loosely-coupled distributed systems》论文来实现的，接下来我们首先来安装使用下这个软件，然后再来探索下其中比较重要一致性算法。

#zookeeper安装和使用
>zookeeper的安装基本上可以按照 http://hadoop.apache.org/zookeeper/docs/current/zookeeperStarted.html 这个页面上的步骤完成安装，这里主要介绍下部署一个集群的步骤，因为这个官方页面似乎讲得并不是非常详细(Running Replicated Zookeeper)。
    
1. 由于手头机器不足，所以在一台机器上部署了3个server,如果你手头也比较紧，也可以这么做。那么我建了3个文件夹，如下<br/>
**server1   server2   server3**

2. 然后每个文件夹里面解压一个zookeeper的下载包，并且还建了几个文件夹，总体结构如下,最后那个是下载过来压缩包的解压文件<br/>
**data dataLog logs zookeeper-3.3.2**

3. 那么首先进入data目录，创建一个myid的文件，里面写入一个数字，比如我这个是server1,那么就写一个1，server2对应myid文件就写入2，server3对应myid文件就写个3

4. 然后进入**zookeeper-3.3.2/conf**目录，那么如果是刚下过来，会有3个文件，**configuration.xml, log4j.properties,zoo_sample.cfg**,这3个文件我们首先要做的就是在这个目录创建一个**zoo.cfg**的配置文件，当然你可以把**zoo_sample.cfg**文件改成**zoo.cfg**，配置的内容如下所示： 
>tickTime=2000
initLimit=5
syncLimit=2
dataDir=xxxx/zookeeper/server1/data
dataLogDir=xxx/zookeeper/server1/dataLog
clientPort=2181
server.1=127.0.0.1:2888:3888
server.2=127.0.0.1:2889:3889
server.3=127.0.0.1:2890:3890


















