##集群状态查看
######进入系统
cd /home/admin/elasticsearch-1.7.5/bin/service

######切换为admin用户
sudo su admin

######查看日志：
cd /home/admin/elasticsearch-1.7.5/logs

######重启(需要先临时关闭分片分配)：
sudo /home/admin/elasticsearch-1.7.5/bin/service/elasticsearch64 restart

###### 查看文件夹大小
du -ha

###### 删除文件：
sudo rm *.log

######修复配置文件：
sudo chmod 777 /home/admin/elasticsearch-1.7.5/config/elasticsearch.yml
vi /home/admin/elasticsearch-1.7.5/config/elasticsearch.yml

######健康度；
curl -XGET 'http://localhost:9200/_cluster/health?pretty=true'

######统计集群状态：
curl -XGET 'http://localhost:9200/_cluster/stats?pretty=true'

######查看节点信息：
curl -s 'localhost:9200/_cat/nodes?v'

######磁盘使用情况：
curl -s 'localhost:9200/_cat/allocation?v'

######查看所有未分配的节点：
curl -XGET localhost:9200/_cat/shards?h=index,shard,prirep,state,unassigned.reason| grep UNASSIGNED

######查看数据量最大的索引：
curl 'localhost:9200/_cat/indices?bytes=b' | sort -rnk8 | head -10

######设置备份数量：
curl -XPUT 'localhost:9200/_settings' -d '
{
"index" : {
"number_of_replicas" : 1
}
}'

######设置分配策略等：
curl -XPUT 'localhost:9200/_cluster/settings' -d '{
"persistent" : {
"cluster.routing.allocation.enable" : "all",
"discovery.zen.minimum_master_nodes" : 4,
"cluster.routing.allocation.disk.watermark.low": "90%"
}
}'

######设置分片暂时不分配
curl -XPUT 'localhost:9200/_cluster/settings' -d '{
"transient" : {
"cluster.routing.allocation.enable" : "none"
}
}'

######恢复分片分配
curl -XPUT 'localhost:9200/_cluster/settings' -d '{
"transient" : {
"cluster.routing.allocation.enable" : "all"
}
}'

######删除索引
curl -XDELETE 'localhost:9200/dwd_jhs_dacu_predict_jhs_di/'

######延迟分配
curl -XPUT 'localhost:9200/_all/_settings' -d '
{
"settings": {
"index.unassigned.node_left.delayed_timeout": "30s"
}
}'

######进入java后台：
cd /opt/taobao/java/bin

######查看linux进程
ps aux | grep elasticsearch

######查看内存使用情况
sudo /opt/taobao/java/bin/jmap -heap [pid]

######打印gc信息
sudo /opt/taobao/java/bin/jstat -gcutil 244000 10000

######查看mapping
curl -XGET 'localhost:9200/b19fd3816d125127/b19fd3816d125127/_mapping?pretty=true'

##集群扩容
######安装jdk
sudo yum install ali-jdk.x86_64 -b test

######环境变量
sudo vim /etc/profile

######文件末尾添加
export JAVA_HOME = /opt/taobao/java
export PATH = $JAVA_HOME/bin: $PATH

######执行一下
source /etc/profile

######拷贝安装包
sudo scp /home/admin/elasticsearch-1.7.5.tar.gz yanjun.zyj@11.131.227.122:/home/yanjun.zyj

##手动分配未分配的分片
###### node改为某台机器节点名称
```
for index in $(curl  -s 'http://localhost:9200/_cat/shards' | grep UNASSIGNED | awk '{print $1}' | sort | uniq); do
    for shard in $(curl  -s 'http://localhost:9200/_cat/shards' | grep UNASSIGNED | grep $index | awk '{print $2}' | sort | uniq); do
        echo  $index $shard

        curl -XPOST 'localhost:9200/_cluster/reroute' -d '{
            "commands" : [ {
                  "allocate" : {
                      "index" : "'$index'",
                      "shard" : '$shard',
                      "node" : "Master Pandemonium",
                      "allow_primary" : true
                  }
                }
            ]
        }'
    done
done
```
touch xxx.sh
sudo vim xxx.sh
chmod 777 xxx.sh
sudo ./xxx.sh