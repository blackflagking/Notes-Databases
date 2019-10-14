# SQL ordered by Elapsed Time

### 时间：2019:10:13

> SQL查询
 1. 查询oracle快照
 ```shell
 select t.snap_id as snap_id,
        to_char(t.startup_time, 'yyyy-mm-dd hh24:mi:ss') as "SQL第一次执行时间",
        to_char(t.begin_interval_time, 'yyyy-mm-dd hh24:mi:ss') as "快照开始时间",
        to_char(t.end_interval_time, 'yyyy-mm-dd hh24:mi:ss') as "快照结束时间"
   from DBA_HIST_SNAPSHOT t
  order by snap_id desc;
       select * from dba_hist_snapshot x ;
       select * from v$database;
       select * from v$instance;
```

2. 执行以下SQL，查询S执行时间最长的前10条SQL语句 
```shell
 select *
   from (select round((t2.elapsed_time_delta / 1000000), 2) as "elapsed_time(s)",
                t2.executions_delta as "executions",
                round(((t2.elapsed_time_delta / 1000000) /
                      t2.executions_delta),
                      2) as "Elapsed Time per Exec (s)",
                round((t2.cpu_time_delta / t2.elapsed_time_delta) * 100, 2) as "%CPU",
                --round(t1.user_io_wait_time / t2.elapsed_time_delta * 100, 2) as "%IO",
                t1.sql_id as sql_id,
                t1.module as "sql module",
                t1.sql_text as sql_text,
                t1.sql_fulltext as sql_fulltext,
                to_char(t3.begin_interval_time, 'yyyy-mm-dd hh24:mi:ss') as "begin time",
                to_char(t3.end_interval_time, 'yyyy-mm-dd hh24:mi:ss') as "end time"
           from v$sqlarea t1, DBA_HIST_SQLSTAT t2, DBA_HIST_SNAPSHOT t3
          where t1.sql_id = t2.sql_id
            and t2.snap_id = t3.snap_id
            and t2.executions_delta is not null
            and t2.executions_delta <> 0
            and t2.elapsed_time_delta is not null
            and t2.elapsed_time_delta <> 0
               -- and t1.parsing_schema_name in ('FASP_PLATFORM')
            and t2.snap_id = &snap_id
          order by t2.elapsed_time_delta desc)
  where rownum <= 10;
 ```

> 名词解释
1. SQL ordered by Elapsed Time  
记录了执行总和时间的TOP SQL(请注意是监控范围内该SQL的执行时间总和，而不是单次SQL执行时间 Elapsed Time = CPU Time + Wait Time)。
Elapsed Time(S): SQL语句执行用总时长，此排序就是按照这个字段进行的。注意该时间不是单个SQL跑的时间，而是监控范围内SQL执行次数的总和时间。单位时间为秒。Elapsed Time = CPU Time + Wait Time
CPU Time(s): 为SQL语句执行时CPU占用时间总时长，此时间会小于等于Elapsed Time时间。单位时间为秒。
Executions: SQL语句在监控范围内的执行次数总计。
Elap per Exec(s): 执行一次SQL的平均时间。单位时间为秒。
% Total DB Time: 为SQL的Elapsed Time时间占数据库总时间的百分比。
SQL ID: SQL语句的ID编号，点击之后就能导航到下边的SQL详细列表中，点击IE的返回可以回到当前SQL ID的地方。
SQL Module: 显示该SQL是用什么方式连接到数据库执行的，如果是用SQL*Plus或者PL/SQL链接上来的那基本上都是有人在调试程序。一般用前台应用链接过来执行的sql该位置为空。
SQL Text: 简单的sql提示，详细的需要点击SQL ID。

2. SQL ordered by CPU Time:
记录了执行占CPU时间总和时间最长的TOP SQL(请注意是监控范围内该SQL的执行占CPU时间总和，而不是单次SQL执行时间)。

3. SQL ordered by Gets:
记录了执行占总buffer gets(逻辑IO)的TOP SQL(请注意是监控范围内该SQL的执行占Gets总和，而不是单次SQL执行所占的Gets)。

4. SQL ordered by Reads:
记录了执行占总磁盘物理读(物理IO)的TOP SQL(请注意是监控范围内该SQL的执行占磁盘物理读总和，而不是单次SQL执行所占的磁盘物理读)。

5. SQL ordered by Executions:
记录了按照SQL的执行次数排序的TOP SQL。该排序可以看出监控范围内的SQL执行次数。

6. SQL ordered by Parse Calls:
记录了SQL的软解析次数的TOP SQL。说到软解析(soft prase)和硬解析(hard prase)，就不能不说一下Oracle对sql的处理过程。

7. SQL ordered by Sharable Memory:
记录了SQL占用library cache的大小的TOP SQL。Sharable Mem (b)：占用library cache的大小，单位是byte。

8. SQL ordered by Version Count:
记录了SQL的打开子游标的TOP SQL。

9. SQL ordered by Cluster Wait Time:
记录了集群的等待时间的TOP SQL