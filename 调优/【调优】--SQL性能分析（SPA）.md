# 【调优】SQL性能分析--（SPA)

## what
SPA是11G新特性
## why
主要是检测升级之后性能退化的SQL语句
## where
升级、迁移、参数修改、I/O子系统变更
## how
1、在primary database上捕获SQL负载、生成SQL Tunning Set   
2、建立中转表，将SQL Tunning Set倒入中转表，导出中转表并转到auxiliary database    
3、在auxiliary database上导入中准表，并解压中转表的数据到SQL Tunning Set    
4、创建SPA任务，首先生成10g的trail，然后在11g中再生成11g的trail   
5、执行比较任务，再生成SPA报告   
6、分析性能退化的SQL语句   
7、在SQLTunning中根据建议优化

数据泵导入导出在调优中的作用：
当数据库高负荷的时候，建表容易但是是否真实有效难以保证。

在调参的时候如何寻找最佳值，一般不在product库上调整。
而选择在模拟库上做调整，Oracle在11g的时候提出的真应用（REAL APP）的概念，
以实现工作重放，重放可以使用多变重放，反复调整。

如何多变重放？利用数据库闪回技术，实现多遍重放。
包括升级，改硬件都可以以这种方式实现。
重放分两种：
（1）数据库级别的重放
（2）SQL级别的重放——sql performance analyze（SPA)——在EM中看SPA

SPA的EM入口
（1）主页->spa
（2）server->sql tuning set->spa
（3）server->顾问系统

数据泵有一点好处是可以预估导出的数据量有多大。


regress回退



计划基线捕获分类：
自动捕获——通过参数设置show parameter baseline
手动捕获


sysaux空间都存什么：
awr
em
计划基线的报告（可通过包来调）

sql plan control ——>SQL plan baseline
capture sqlplan baselines(是否开启捕获计划极限)
计划极限是accepted说明就是计划基线

激活基线因为不断的捕获信息加重本身的系统负担，一般在工作中很少用到

执行计划中可来自三个方面：计划基线、动态采样、统计信息



四种情况导致执行计划被LRU算法换出内存：
1、收集统计信息
2、删除索引
3、授予对象权限
4、更新表结构


工作经验：
由于统计信息动作会是一部分的执行计划失效，所以在工作中不要在白天任务繁忙的时候使用，因为数据库的shared pool要保持高击中率。

工作中尽量做加法，少做减法，因为一旦删除很少能够恢复回来。


