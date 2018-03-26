# 高性能 MySQL

[TOC]

## 第三章 服务器性能剖析
### 3.3 剖析 MySQL 查询
> pt-query-digest (慢日志分析工具)

## 第五章 创建高性能的索引

### 5.3.2 前缀索引和索引选择性

前缀选择性的提升幅度

```
select count(distinct left(city, 3))/count(*) as sel3,
    count(distinct left(city, 4))/count(*) as sel4,
    count(distinct left(city, 5))/count(*) as sel5,
    count(distinct left(city, 6))/count(*) as sel6,
    count(distinct left(city, 7))/count(*) as sel7,
from sakila.city_demo
```

sel3 | sel4 | sel5 | sel6 | sel7
---|---|---|---|---
0.0239 | 0.0293 | 0.0305 | 0.0309 | 0.0310

查询显示当前缀长度达到7的时候，在增加前缀的长度，选择性提升的幅度已经很小了。
