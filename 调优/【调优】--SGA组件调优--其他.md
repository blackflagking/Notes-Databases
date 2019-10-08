#【调优】--SGA组件调优-
####作者：coresu
####时间：2019.10.8



##栓(latch)
###what
通过hash值确定在哪个bucket上
栓是串行的，持有栓的用户才能在bucket上找相应的（object handle）句柄。
执行计划通过handle提取，handle句柄能够找到执行计划在哪，好比藏宝图handle是堆结构Heap0中的Heap6里面存储折执行计划。
cursor handle <==> object Handle <==> handle

栓的三种状态latch（百万分之一秒级别发生）  
shared--读动作  
exclusive--写动作{增|删|改}   
Null什么都没有  
在shared和exclusive转换是会出现这种状态

###why

###where

###how





###锁 共享锁、排他锁（TX、TM）
#####共享
#####独占



栓我们只能知道此概念，并不能操作它  
select * from v$latchname where name like '%Mutex%';
select gets,miss from v$latch;  
gets:请求  
misses:失效
sleep:CPU对否等待，sleep包含不同级别

mutex的级别比latch更微小
软解析上持有的mutex，之前使用的是latch，在11g的时候该到了mutex上。
工作经验：在AWR报告中mutex可能会排的靠上   
shared pool中的free list
chunk是一片连续的空间


bucket开始比较大，会由于不断的申请被越切越细，bucket链越来越长。  


ora 04031错误:当需要大块的时候，bucket的空间不够了
解决办法是使用自留地
show parameter reserve
工作经验：当时出现4031错误时，解决办法就是把自留地开大，现在很少出现了，因为shared pool一般都足够大。   

library cache中的sql area中
SQL 解析
pl/sql编译 可能有的大对象编译比较费空间，可以使用keep大对象将执行计划pin在内存中的sql area中，kept改成yes，从而避免硬解析使用大chunk，不受LRU算法调优。
好处：避免4031错误
坏处：占用内存空间

##Result cache
####目的:可以解决逻辑读的问题
减少从buffer cache中读buffer，而从Result cache中直接读出结果集。
show parameter result_cache_mode
manual 手动 默认没有 result cache，可以使用hint 强制使用
auto  自动

result_cache_max_result 在shared pool中可以放个结果集





latch征用
发生条件：多个session并发引起的






buffer busy waits
工作经验：解决只能稀释，或从存储架构上解决  







###自动调优的阶段:
9i---PGA  
10g--SGA    
    sga_target    
    sga_max_target
11g--SGA+PGA
    memory_target    
    shared_memory_target
