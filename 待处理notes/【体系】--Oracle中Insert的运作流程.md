# 【体系】--Oracle中Insert的运作流程

1、向DB BUFFER申请内存空间。
2、产生REDO、UNDO记录（UNDO产生REDO信息），索引段REDO信息，在UNDO里面记录相关数据行号。
3、COMMIT
SCN增加
将Redo log buffer 写入Redo log file
启动DBWR，将缓存中的数据写入磁盘的脏数据块写入
告诉用户 Commit完成。