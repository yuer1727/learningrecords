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
<font color="#4590a3">tickTime=2000
initLimit=5
syncLimit=2
dataDir=xxxx/zookeeper/server1/data
dataLogDir=xxx/zookeeper/server1/dataLog
clientPort=2181</font>
server.1=127.0.0.1:2888:3888
server.2=127.0.0.1:2889:3889
server.3=127.0.0.1:2890:3890
标红的几个配置应该官网讲得很清楚了，只是需要注意的是clientPort这个端口如果你是在1台机器上部署多个server,那么每台机器都要不同的clientPort，比如我server1是2181,server2是2182，server3是2183，dataDir和dataLogDir也需要区分下。 
最后几行唯一需要注意的地方就是 server.X 这个数字就是对应 data/myid中的数字。你在3个server的myid文件中分别写入了1，2，3，那么每个server中的zoo.cfg都配server.1,server.2,server.3就OK了。因为在同一台机器上，后面连着的2个端口3个server都不要一样，否则端口冲突，其中第一个端口用来集群成员的信息交换，第二个端口是在leader挂掉时专门用来进行选举leader所用。

5. 进入**zookeeper-3.3.2/bin** 目录中，**./zkServer.sh start**启动一个server,这时会报大量错误？其实没什么关系，因为现在集群只起了1台server，zookeeper服务器端起来会根据**zoo.cfg**的服务器列表发起选举leader的请求，因为连不上其他机器而报错，那么当我们起第二个zookeeper实例后，leader将会被选出，从而一致性服务开始可以使用，这是因为3台机器只要有2台可用就可以选出leader并且对外提供服务(2n+1台机器，可以容n台机器挂掉)。

接下来就可以使用了，我们可以先通过 zookeeper自带的客户端交互程序来简单感受下zookeeper到底做一些什么事情。进入zookeeper-3.3.2/bin（3个server中任意一个）下，./zkCli.sh –server 127.0.0.1:2182,我连的是开着2182端口的机器。

那么，首先我们随便打个命令，因为zookeeper不认识，他会给出命令的help,如下图  
![](/assets/dddd.jpg)


















