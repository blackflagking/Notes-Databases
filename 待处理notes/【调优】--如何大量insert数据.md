# 【调优】--如何大量数据（导表）
### 作者:coresu  
### 时间:2019:10:16


## 如何对一个表在短时间内进行大批量的insert操作。

1. 优先考虑是否能去掉索引，导入完表之后再建立索引，因为索引对insert的影响很大。

```shell
insert into tab1 select * from tab2;  
commit;
```
【经验】：千万级的数据可在1小时内完成,但产生归档arch非常快，需关注归档的产生量，  
及时使用RMAN或者UMAN进行备份，避免arch目录撑爆导致的数据量丢失现象。

2. nologging

```shell
alter table tab1 nologging;    
insert /*+ append */ into tab1 select * from tab2;    
commit;   
alter table tab1 logging;  
```
缺点：需要关机到mount状态，改为noarchivelog状态  

append会对表加排它锁，阻塞除select意外的所有DML操作
非归档模式下，使用append减少redo日志的产生。
归档模式下，需使用append+logging减少redo日志的产生。
取消日志大量减少arch文件数量进而缩短时间

【经验】：千万级的数据可在45分钟内完成，适合串行的单进程方式，如果多进程并发时，后发起的进程会enqueue等待。  

3. 数据源为select全表扫描情况  
```shell
insert into tab1 select /*+ parallel */ * from tab2;  
commit;
```
* 作用：可加parallel的hint来提高其并发  
* 限制：最大并发度受初始化参数parallel_max_servers限制 
* 查看：并发进程以v$px_session视图，或ps -ef |grep ora_p方式查看

```shell
alter session enable parallel dml;    
insert /*+ parallel */ into tab1 select * from tab2;   
commit;  
```


4. 并发insert
insert into tab1 select * from tab2 partition (p1);    
insert into tab1 select * from tab2 partition (p2);    
insert into tab1 select * from tab2 partition (p3);    
insert into tab1 select * from tab2 partition (p4);  

　　对于分区表可以利用tab1进行多个进程的并发insert，分区越多，可以启动的进程越多。  
我曾经试过insert 2.6亿行记录的一个表，8个分区，8个进程  
如果用方法2，单个进程完成可能要40分钟，但是由于是有8个分区8个进程，后发进程有enqueue，所以因此需要的时间为40分钟×8;  
虽然单个进程需要110分钟，但是由于能够并发进程执行，所以总共需要的时间就约为110分钟了。


5. 批量绑定(bulk binding)
DECLARE TYPE dtarray IS TABLE OF VARCHAR2(20) INDEX BY BINARY_INTEGER;
    v_col1 dtarray; 
    v_col2 dtarray;
    v_col3 dtarray;  
BEGIN 
    SELECT col1, col2, col3 BULK COLLECT INTO v_col1, v_col2, v_col3  FROM tab2;  FORALL i IN 1 .. v_col1.COUNT 
    insert into tab1  WHERE tab1.col1 = v_col1;  
END;  


作用：在线处理，不必停机。
原因：当循环执行一个绑定变量的sql语句时候，在PL/SQL 和SQL引擎(engines)中，会发生大量的上下文切换(context switches)。bulk binding能将数据批量的从plsql引擎传到sql引擎，从而减少上下文切换过程，提升效率。


6. 用copy的方法进行插入
　　sqlplus -s user/pwd  
< runlog.txt set copycommit 2;  
  set arraysize 5000;  
    copy from user/pwd@sid - to user/pwd@sid - insert tab1 using  select * from tab2;   
    exit 
    EOF
　　，注意此处insert没有into关键字。该方法的好处是可以设置copycommit和arrarysize来一起控制commit的频率，上面的方法是每10000行commit一次   





Oracle优化：大量数据插入或更新
2015-08-15 10:42:50 Knuuy 阅读数 15273更多
分类专栏： ORACLE数据库
版权声明：本文为博主原创文章，遵循 CC 4.0 BY-SA 版权协议，转载请附上原文出处链接和本声明。
本文链接：https://blog.csdn.net/knuuy/article/details/47667655
最近遇到的一个面试题，印象很深记录如下：

面试官：现在有一张表数据量达很大，要把里面记录时间的那行更新到当前最新日期，每次更新都很卡，机器变慢影响业务，怎么优化。

我的想法是：大量的数据更新肯定会写记录，而大量的写记录又会触发lgwr,所以机器变卡的原因是内存暂满还有在写重做日记。如果操作是添加日志组或增大日志的大小，又或者调整SGA里面各种池的大小，其实也是没有用，顶多就是延迟发生故障。



所以只要跳过写记录那块，那么问题就可以根本解决。



一般数据库都是在归档模式下运行（Oracle数据库有联机重做日志，这个日志是记录对数据库所做的修改，比如插入，删除，更新数据等，对这些操作都会记录在联机重做日志里），跳过就要关闭归档模式，还有就是修改表在nologging状态下。这样两个状态下就可以不写日志，但是也是在万分确定不会出错的情况下才能使用。



不过这样操作，数据库就要关闭重启到mount的状态，再关闭归档，这样在生产库是不允许的，而且关闭归档是影响整个数据库的，其他业务也会陷入无法恢复的境地，整个数据库无法使用rman做增量备份，很多备份方案都会受到限制。



所以我最后说了先清空缓存，然后把那个表设置为nologging的状态，再进行操作。（单独这个表没有写日志，其他表也照常。）
面试官：是基本这样，但是还有一种更加优化的方法。我最后也没想出来，面试官就告诉我：用create table 复制....+ nologging的方法，当时就心领神会大。虽然不明白为什么同样在nologging的情况下create 会比 直接update 会优化的好，是因为create 是 DDL 吗？继续百度。但我还是先整理这个最终优化方案。



1.假设t表里面需要修改的字段为v_time，创建一个表t_1先不要把v_time放进去： create table t_1 as select v1,v2 from t  nologging；

2.再使用添加列默认值的方式把v_time添加进入：alter table t_1 add v_time date default sysdate;

这样就完成这个‘最终优化方案。



这个是更新数据，那么大量插入新数据呢？也是用到nologging，但还有一个‘append’；

append 个人理解解析就是：插入数据的时候在表的高水位线之上直接插入数据，数据比较快，

分别验证

1、非归档模式+表logging下对产生redo的量的影响

2、非归档模式+表nologging下对产生redo的量的影响

3、归档模式+表logging下对产生redo的量的影响

4、归档模式+表nologging下对产生redo的量的影响


使用如下命令查看redo size，注意，这个是累加的，当你exit后，在查看会变成0
 
select b.name,a.value from v$mystat a,v$statname b where a.statistic#=b.statistic# and b.name='redo size';
 
复制表结构 where 1=0
 
create table redo_test as select * from dba_objects where 1=0;

1、非归档模式+logging选项
insert into redo_test select * from dba_objects;
 
SQL> insert into redo_test select * from dba_objects;
 
72305 rows created.
 
SQL> select b.name,a.value from v$mystat a,v$statname b where a.statistic#=b.statistic# and b.name='redo size';
 
NAME      VALUE
-------------------------------------------------------------------------------------------------------------------------------- ----------
redo size    8441916
 
大约产生了8M的redo
 
exit 
 
 
SQL> insert  /*+ append */ into redo_test select * from dba_objects;
 
72305 rows created.
 
SQL> select b.name,a.value from v$mystat a,v$statname b where a.statistic#=b.statistic# and b.name='redo size';
 
NAME      VALUE
-------------------------------------------------------------------------------------------------------------------------------- ----------
redo size       2048

结果对比：
非归档模式+logging的情况下，普通insert产生的redo有8M，但是使用append的方式插入产生的redo只有2048bye，基本上可以忽略不计，效果非常明显的。


2、非归档模式+nologging选项

alter table redo_test nologging; 开启<span style="color: rgb(51, 51, 51); font-family: arial; font-size: 13px; line-height: 20.0200004577637px;">不用生成日记模</span>式
exit
 
SQL> insert into redo_test select * from dba_objects;
 
72305 rows created.
 
SQL> select b.name,a.value from v$mystat a,v$statname b where a.statistic#=b.statistic# and b.name='redo size';
 
NAME      VALUE
-------------------------------------------------------------------------------------------------------------------------------- ----------
redo size    8245308
 
还是8M
 
 
SQL> insert  /*+ append */ into redo_test select * from dba_objects;
 
72305 rows created.
 
SQL> select b.name,a.value from v$mystat a,v$statname b where a.statistic#=b.statistic# and b.name='redo size';
 
NAME      VALUE
-------------------------------------------------------------------------------------------------------------------------------- ----------
redo size    8260224
 
SQL> select 8260224-8245308 from dual;
 
8260224-8245308
---------------
          14916

在非归档模式下通过insert /*+ append */ into方式批量加载数据可以大大减少redo产生。


3、归档模式+logging选项下对产生redo的量的影响

先启动归档模式

SQL> alter table redo_test logging; 修改回去写记录模式
 
Table altered.
 
exit
 
---insert插入数据
SQL> insert into redo_test select * from dba_objects;
 
72305 rows created.
 
SQL> select b.name,a.value from v$mystat a,v$statname b where a.statistic#=b.statistic# and b.name='redo size';
 
NAME      VALUE
-------------------------------------------------------------------------------------------------------------------------------- ----------
redo size    8228800
 
SQL> 
 
发现产生了8m的日志
 
 
--使用append的方式插入
SQL> insert  /*+ append */ into redo_test select * from dba_objects;
 
72305 rows created.
 
SQL> select b.name,a.value from v$mystat a,v$statname b where a.statistic#=b.statistic# and b.name='redo size';
 
NAME      VALUE
-------------------------------------------------------------------------------------------------------------------------------- ----------
redo size    8481404

在归档模式+logging下，不管有没有使用append（高水位插入）的方式都会产生大量的redo，日志还是要写的，与预想的结果吻合。


4、归档模式+nologging选项下对产生redo的量的影响-重点

--修改为nologging模式--切莫忘
  alter table redo_test nologging;
  
 exit
 
 
 
SQL> insert into redo_test select * from dba_objects;
 
72305 rows created.
 
SQL> select b.name,a.value from v$mystat a,v$statname b where a.statistic#=b.statistic# and b.name='redo size';
 
NAME      VALUE
-------------------------------------------------------------------------------------------------------------------------------- ----------
redo size    8422872
 
使用普通插入的方式生成了8m日记
 
SQL> insert  /*+ append */ into redo_test select * from dba_objects;
 
72305 rows created.
 
SQL> 
SQL> select b.name,a.value from v$mystat a,v$statname b where a.statistic#=b.statistic# and b.name='redo size';
 
NAME      VALUE
-------------------------------------------------------------------------------------------------------------------------------- ----------
redo size    8438572
 
SQL> select 8438572-8422872 from dual;
 
8438572-8422872
---------------
          15700

在归档+nologging的情况下使用append高水位插入只有15700的redo，536倍的大小被减去了，所以nologging+append是在归档模式下性能优化的好方法。

记得还有一个小插曲，最近身体不舒服，喉咙痛痰多，声音都变老了，一个人事见到我说你还挺年轻的啊再见。