

>一般来说，将TEXT字段从一个操作频繁的表中拆分出去，成为一个KEY-VALUE结构的独立表，是好处颇多的。

##好处

###1、便于运维

>由于目前Innodb-plugin对于大多数DDL都是会有TABLE-LOCK的。这也就意味着，一张表的DDL时间越长，业务的不可访问时间也就越长。
而决定一条DDL命令执行时长的两个关键因素就是：表行数，表物理文件大小。
TEXT字段的拆分独立，能够很有效的减小主表的物理文件大小。
由此不难看出一张对于业务十分重要或者访问非常频繁的表来说，这样的拆分是能够极大程度上降低运维成本的。


###2、便于缓存方案、数据产品的迁移实施

>Key-大体积Value的数据类型对于MySQL来说本来就不是一个强项。
将TEXT拆分成K-V这样简单结构的表后，很方便就能通过改动较少的代码，实现数据产品的迁移。
无论是Mongo的 _id: value 、redis 的string 、还是memcached的key - value 都可以很轻松的导入数据。
此外，抛开缓存方案不说，光基于节省MySQL磁盘空间的考虑，也可以对于拆分后的独立表单独配置 row-format = compressed 的innodb压缩参数。减小物理文件体积，同时也增多了单个数据页能够存放的内容，一定程度上的提升了QPS。

###3、提高查询性能

>上文提到了，拆分后添加压缩选项后，K-V表的QPS会较之前有提升。
除此之外，这种方案对于 Antelope文件格式的主表查询性能也会有提升（Barracuda文件格式则没有区别）。
由于Antelope的 Compact和Redundant文件格式，对于长字段都会将其最左的786个字节内容保存在Primary Key的数据页中。
而Barracuda 的文件格式，对于TEXT字段，都会将其全部内容存放在off-page中，而Primary Key的数据页中只存放一个20字节的指针。
拆分对于前者可以节省786B的数据页空间，而后者只有20B的空间。这也就是为什么，前者的性能提升会更大