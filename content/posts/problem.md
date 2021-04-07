---
title: "Problem"
date: 2021-04-06T16:39:49+08:00
draft: true
---

# Mybatis

## 1.错误：1 字节的 UTF-8 序列的字节 1 无效。

出现的问题：1 字节的 UTF-8 序列的字节 1 无效。

主要的原因是：在配置MapperConfig.xml与Mapper.xml的字符编码的问题

解决方法：

将MapperConfig.xml与Mapper.xml的字符编码改为UTF8就可以了

MapperConfig.xml

途径：https://blog.csdn.net/Falling_stars_/article/details/112279192

## 2.错误：Error querying database.