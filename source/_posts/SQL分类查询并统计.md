---
title: SQL分类查询并统计
date: 2019-03-05 15:47:08
tags: Sql Server
excerpt: SQL聚合统计查询……
---
# 获取业务数据，并分类别统计

## 描述

业务上需要在工作流中统计2017年和2018年的使用到的流程数据，并根据不同的流程进行统计，现阶段没有现成的统计数据，于是准备在数据库中直接拉取。
	
问题难点，数据中包括有processID值，并不是队列表的主键，需要进行去重，另外再根据不同流程的ID来进行分类，写成了如下的sql解决了问题。

## 解决方案
```sql
 select b.alias,count(alias) as 条数 
  from (select processID,数据源表 where Created between '2017-01-01' and '2018-01-01' group by processID,Alias) b
  group by b.alias
```
