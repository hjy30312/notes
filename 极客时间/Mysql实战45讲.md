#### 01 | 基础架构：一条SQL查询语句是如何执行的？

![](https://github.com/hjy30312/picBed/blob/master/img/1.jpg?raw=true)

​																MySQL的逻辑架构图

大体来说，MySQL可以分为Server层和存储引擎层两部分。

Server层包括连接器、查询缓存、分析器、优化器、执行器等，涵盖 MySQL 的大多数核心服务功能。

存储引擎层负责数据的存储和提取。其架构模式是插件式的，支持InnoDB、MyISAM等多个存储引擎。