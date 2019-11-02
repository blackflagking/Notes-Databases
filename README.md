<p align="center" style="font-weight:bold;"> <font size="30">Coresu的Oracle学习笔记</font></p>


<p align="center">
<a href="https://github.com/blackflagking/Notes-Oracle" target="_blank">
    <img src="https://thumbnail0.baidupcs.com/thumbnail/f2caf679995a2c4470b63ddf72a04083?fid=2977596842-250528-601656470218205&time=1572685200&rt=sh&sign=FDTAER-DCb740ccc5511e5e8fedcff06b081203-z0xnjgf1Dea%2BZd5BTCSX2VZ5SD4%3D&expires=8h&chkv=0&chkbd=0&chkpc=&dp-logid=7090963443295851244&dp-callid=0&size=c710_u400&quality=100&vuk=-&ft=video" width="250"/>
</a>
</p>

<p align="center">
  <a href="https://docs.oracle.com/en/database/oracle/oracle-database/19/cncpt/introduction-to-oracle-database.html#GUID-A42A6EF0-20F8-4F4B-AFF7-09C100AE581E"><img src="https://img.shields.io/badge/阅读-read-brightgreen.svg" alt="阅读"></a>
  <a href="#联系我"><img src="https://img.shields.io/badge/chat-微信-blue.svg" alt="微信"></a>
  <a href="#公众号"><img src="https://img.shields.io/badge/%E5%85%AC%E4%BC%97%E5%8F%B7-OracleGuide-lightgrey.svg" alt="公众号"></a>
  <a href="#公众号"><img src="https://img.shields.io/badge/PDF-Oracle面试突击-important.svg" alt="公众号"></a>
  <a href="#投稿"><img src="https://img.shields.io/badge/support-投稿-critical.svg" alt="投稿"></a>
  <a href="https://xiaozhuanlan.com/javainterview?rel=javaguide"><img src="https://img.shields.io/badge/Oracle-面试指南-important" alt="投稿"></a>
</p>

<h3 align="center">CORESU的BLOG</h3>


<p align="center">此部分为Oracle数据库学习笔记，  
如果大家有向共同交流促进或对所述文章又不懂的地方可在issue部分指出  
或通过Email与我进行交流！不久之后，coresu在自己的云端服务器推出自己的blog，请大家期待吧！</p>

## 目录

- [Oracle](#java)
    - [体系](#体系)
    - [备份恢复](#备份恢复)
    - [实例调优](#实例调优)
    - [ASM相关问题](#ASM相关问题)
    - [DG-DataGuard](#DG-DataGuard)
    - [OGG-GoldrenGate](#OGG-GoldrenGate)
    - [RAC-RealApplicationCluster](#RAC-RealApplicationCluster)
    - [Oracle学习中常见问题汇总](#java学习常见问题汇总)
    
- [Oracle学习中常见问题汇总](#java学习常见问题汇总)
    - [Oracle\ materialize\ view无法自动刷新问题.md](./Oracle--常见问题/Oracle\ materialize\ view无法自动刷新问题.md)
    - [Oracle--DBCA起不来的防火墙原因](./Oracle--常见问题/Oracle--DBCA起不来的防火墙原因.md)
    - [Oracle备份恢复之recover database的四条语句区别](./Oracle--常见问题/Oracle备份恢复之recover database的四条语句区别.md)
    - [Oracle数据库在truncate不能flashback table的原因](./Oracle--常见问题/Oracle数据库在truncate不能flashback table的原因.md)
    - [Oracle查询表空间使用情况以及其他查询](./Oracle--常见问题/Oracle查询表空间使用情况以及其他查询.md)
    - [Oracle正常安装sqlplus命令无法登录](./Oracle--常见问题/Oracle正常安装sqlplus命令无法登录.md)
    - [当数据库出现OC4J Configuration issue](./Oracle--常见问题/当数据库出现OC4J Configuration issue.md)
    - [恢复数据库后以read only打开提示recovery的原因](./Oracle--常见问题/恢复数据库后以read only打开提示recovery的原因.md)
    - [数据泵在调优过程中的作用](./Oracle--常见问题/数据泵在调优过程中的作用.md)
    - [如何查看日志切换的时间](./Oracle--常见问题/如何查看日志切换的时间.md)
    - [诊断Oracle Redo Log引发的性能问题](./Oracle--常见问题/诊断Oracle Redo Log引发的性能问题.md)
    - [验证表空间是否自动回收空间](./Oracle--常见问题/验证表空间是否自动回收空间.md)

- [体系](#体系)
    - [【体系】--Insert语句在Oracle实例中的的运作流程](./Oracle--体系/【体系】--Insert语句在Oracle实例中的的运作流程.md)
    - [【体系】--select语句在Oracle实例中的运作过程](./Oracle--体系/【体系】--select语句在Oracle实例中的运作过程.md)
    - [【体系】--update语句在Oracle实例中的运作过程](./Oracle--体系/【体系】--update语句在Oracle实例中的运作过程.md)

- [备份恢复](#备份恢复)
    - [recover database using backup control file报system01.dbf文件修复](./Oracle--备份恢复/recover database using backup control file报system01.dbf文件修复.md) 


- [实例调优](#实例调优)
    - [【调优】--解析Direct Path Read](./Oracle--调优/【调优】--解析Direct Path Read.md)
    - [【调优】--解读10046事件](./Oracle--调优/【【调优】--解读10046事件.md)
    - [【调优】--解读10053事件](./Oracle--调优/【调优】--解读10053事件.md)
    - [【调优】--解读自动维护任务](./Oracle--调优/【调优】--解读自动维护任务.md)


- [ASM相关问题](#ASM相关问题)  
    - [ASM的connect的和mounted的区别](./Oracle--ASM/ASM的connect的和mounted的区别.md)

- [DG-DataGuard](#DG-DataGuard)
    - [【DG】v$database的switchover_status字段sessions active阐述](./Oracle--DG/【DG】v$database的switchover_status字段sessions active阐述.md)
    - [【DG】防火墙与SELINUX导致的DG主备库失连接](./Oracle--DG/【DG】防火墙与SELINUX导致的DG主备库失连接.md)


 - [OGG-GoldrenGate](#OGG-GoldrenGate)
    - [【OGG】OCI Error ORA-02291违反完整约束条件](./Oracle--OGG/【OGG】OCI Error ORA-02291违反完整约束条件.md)
    - [【OGG】导致单向联通失败的几点原因](./Oracle--OGG/【【OGG】导致单向联通失败的几点原因.md)

 - [RAC-RealApplicationCluster](#RAC-RealApplicationCluster)
    - [【RAC】历史及简介](./Oracle--RAC/【RAC】历史及简介.md)
    - [【RAC】常用命令总结](./Oracle--RAC/【RAC】常用命令总结.md)
    - [【RAC】NFS权限问题root出现Permission denied](./Oracle--RAC/【RAC】NFS权限问题root出现Permission denied.md)
    - [【RAC】RAC安装过程提示not a shared subnet错误](./Oracle--RAC/【RAC】RAC安装过程提示not a shared subnet错误.md)





- [工具](#工具)
    - [Git](#git)
    - [Docker](#Docker)
- [资源](#资源)
    - [书单](#书单)
- [待办](#待办)
- [说明](#说明)

### 为什么要做这个开源文档？

最初源于博主想在学习技术的过程中，记录下自己学习的点滴。   
相信大家一定经历过，一个小BUG因为自己的无助被困N天的痛苦经历。  
博主也想借助GitHub这个平台将自己在学习Oracle过程中碰到的一些错误，以及是如何解决的分享给大家。

### 投稿

由于我个人能力有限，很多知识点无法涉及，或正在学习的路上；   
因此你可以对知识进行补充。对于值得分享的文章将在本博文中展出；  
你可以通过[Email](#email)方式向博主投稿或者纠错； 
博主期待你的加入...

### 联系我

添加我的微信备注“Github”,coresu便会**“通过”**您的好友请求。

![个人微信](https://thumbnail0.baidupcs.com/thumbnail/8a100bc9acacae939f438ac2431346b5?fid=2977596842-250528-815582455701815&time=1572688800&rt=sh&sign=FDTAER-DCb740ccc5511e5e8fedcff06b081203-uTPmitKSJE5X4%2BD39XDqzymEN3E%3D&expires=8h&chkv=0&chkbd=0&chkpc=&dp-logid=7091503216580004742&dp-callid=0&size=c710_u400&quality=100&vuk=-&ft=video)

大家也可以通过Email的方式联系我，记得在邮件中表明来自GitHub哦😛！  
##### Email:coresu@icloud.com


### Contributor

本篇文章的贡献者现在仅有博主一人，期待更多人的加入，让更多人能够爱上数据库。

<a href="https://github.com/blackflagking">
    <img src="https://thumbnail0.baidupcs.com/thumbnail/f2caf679995a2c4470b63ddf72a04083?fid=2977596842-250528-601656470218205&time=1572685200&rt=sh&sign=FDTAER-DCb740ccc5511e5e8fedcff06b081203-z0xnjgf1Dea%2BZd5BTCSX2VZ5SD4%3D&expires=8h&chkv=0&chkbd=0&chkpc=&dp-logid=7090963443295851244&dp-callid=0&size=c710_u400&quality=100&vuk=-&ft=video" width="45px"></a>

### 公众号

公众号还在积极筹备中...

![我的公众号]()
