---
Description: vmstat详解
Keywords:
- command
- vmstat
Tags:
- vmstat
Title: vmstat命令详细说明
Topics:
- command
- vmstat
date: 2017-01-11
---

vmstat命令详细说明

##  vmstat 说明

1. r process 在运行队列中等待的进程数
2. b process 在等待io的进程数
3. swpd memory 已经使用的交换内存(kb)
4. free memory 空闲的物理内存(kb)
5. buff memory 用作缓冲区的内存数(kb)
6. cache memory 用作高速缓存的内存数(kb)
7. si swap 从磁盘交换到内存的交换页数量(kb/s)
8. so swap 从内存交换到磁盘的交换页数据(kb/s)
9. bi IO 发送到块设备的块数(块/秒)
10. bo IO 从块设备中接收的块数(块/秒)
11. in system 每秒的中断数，包括时钟中断
12. cs system 每秒的上下文切换的次数
13. us CPU 用户进程使用的cpu时间(%)
14. sy CPU 系统进程使用的cpu时间(%)
15. id CPU CPU空闲时间(%)
16. wa CPU 等待IO所消耗的cpu时间
17. st CPU 从虚拟设备中获得的时间(%)
