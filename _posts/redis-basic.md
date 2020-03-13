---
title: Redis基础
date: 2019-11-15 21:48:22
tags:
- redis
categories:
- redis
---

## Redis 简介
Redis 是一个高性能的key-value形式的内存数据库
Redis 支持数据的持久化，可以将内存中的数据保持在磁盘中，重启的时候可以再次加载进行使用。
Redis 不仅仅支持简单的 key-value 类型的数据，同时还提供 list，set，zset，hash 等数据结构的存储。
Redis 支持数据的备份，即 master-slave 模式的数据备份。
Redis 优势
性能极高 – Redis 能读的速度是 110000 次 /s, 写的速度是 81000 次 /s 。
丰富的数据类型 – Redis 支持二进制案例的 Strings, Lists, Hashes, Sets 及 Ordered Sets 数据类型操作。
原子 – Redis 的所有操作都是原子性的，同时 Redis 还支持对几个操作全并后的原子性执行。
丰富的特性 – Redis 还支持 publish/subscribe, 通知，key 过期等等特性。
Redis 与其他 key-value 存储有什么不同？
Redis 有着更为复杂的数据结构并且提供对他们的原子性操作，这是一个不同于其他数据库的进化路径。
Redis 的数据类型都是基于基本数据结构的同时对程序员透明，无需进行额外的抽象。
Redis 运行在内存中但是可以持久化到磁盘，所以在对不同数据集进行高速读写时需要权衡内存，
应为数据量不能大于硬件内存。在内存数据库方面的另一个优点是， 相比在磁盘上相同的复杂的数据结构，在内存中操作起来非常简单，
这样 Redis 可以做很多内部复杂性很强的事情。 同时，在磁盘格式方面他们是紧凑的以追加的方式产生的，因为他们并不需要进行随机访问。
<!-- more -->

### redis 命令
启动客户端
`$redis-cli`
在远程服务上执行命令
`$redis-cli -h host -p port -a password`
for example:
`$redis-cli -h 127.0.0.1 -p 6379 -a "redis"`

### Redis键(key)
1. 基本语法
COMMAND KEY_NAME
`del key` 当存在key时删除key
`dump key` 序列号给定key，返回被序列化的值
`exists key` 检查给定key是否存在
`expire key` seconds 为给定key设置过期时间
`expire key` timestamp 为给定可以设置过期时间，时间参数为UNIX时间戳
`pexpire key` milliseconds 设置key的过期时间，以毫秒计
`keys pattern` 查找指定所有符合给定模式的key
`move key db`  将当前数据库的key移动到给定的数据库db中
`persist key` 移除key的过期时间，key将持久保持
`pttl key` 以毫秒为单位返回key的剩余的过期时间
`ttl key` 以秒为单位返回给定key的过期时间
`randomkey` 从当前数据库中随机返回一个key
`rename key newkey` 修改key的名称
`renamenx key newkey` 仅当newkey不存在时，将key改名为newkey
`type key` 返回key所存储的值类型

### Redis字符串(String)
`set key value` 设置指定key的值
`get key` 获取指定key的值
`getrange key start end` 返回key中字符串值的子字符
`getset key value` 将给定的key的值设为value，并返回key的旧值
`getbit key offset` 对key所储存的字符串值，获取指定偏移量上的位(bit)

### Redis 持久化 RDB和AOF
持久化，就是把数据(如内存中的对象)保存到可以永久保存的设备，例如磁盘
持久化Redis所有数据保持在内存中，对数据的更新将异步保存在磁盘上

