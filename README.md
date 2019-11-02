<p align="center" style="font-weight:bold;"> <font size="30" }>Coresu的Oracle学习笔记</font></p>


<p align="center">
<a href="https://github.com/Snailclimb/JavaGuide" target="_blank">
    <img src="https://thumbnail0.baidupcs.com/thumbnail/f2caf679995a2c4470b63ddf72a04083?fid=2977596842-250528-601656470218205&time=1572685200&rt=sh&sign=FDTAER-DCb740ccc5511e5e8fedcff06b081203-z0xnjgf1Dea%2BZd5BTCSX2VZ5SD4%3D&expires=8h&chkv=0&chkbd=0&chkpc=&dp-logid=7090963443295851244&dp-callid=0&size=c710_u400&quality=100&vuk=-&ft=video" width="250"/>
</a>
</p>

<p align="center">
  <a href="https://docs.oracle.com/en/database/oracle/oracle-database/19/cncpt/introduction-to-oracle-database.html#GUID-A42A6EF0-20F8-4F4B-AFF7-09C100AE581E"><img src="https://img.shields.io/badge/阅读-read-brightgreen.svg" alt="阅读"></a>
  <a href="#联系我"><img src="https://img.shields.io/badge/chat-微信-blue.svg" alt="微信"></a>
  <a href="#公众号"><img src="https://img.shields.io/badge/%E5%85%AC%E4%BC%97%E5%8F%B7-JavaGuide-lightgrey.svg" alt="公众号"></a>
  <a href="#公众号"><img src="https://img.shields.io/badge/PDF-Java面试突击-important.svg" alt="公众号"></a>
  <a href="#投稿"><img src="https://img.shields.io/badge/support-投稿-critical.svg" alt="投稿"></a>
  <a href="https://xiaozhuanlan.com/javainterview?rel=javaguide"><img src="https://img.shields.io/badge/Oracle-面试指南-important" alt="投稿"></a>
</p>

<h3 align="center">CORESU的BLOG</h3>


<p align="center">此部分为Oracle数据库学习笔记，  
如果大家有向共同交流促进或对所述文章又不懂的地方可在issue部分指出  
或通过Email与我进行交流！不久之后，coresu在自己的云端服务器推出自己的blog，请大家期待吧！</p>

## 目录

- [Oracle](#java)
    - [体系](#基础)
    - [备份恢复](#容器)
    - [单实例调优](#并发)
    - [ASM相关问题](#)
    - [DG-DataGuard](#)
    - [OGG-GoldrenGate](#io)
    - [RAC-Real Application Cluster](#java-8)
    - [Oracle学习中常见问题汇总](#java学习常见问题汇总)
    
- [Oracle学习中常见问题汇总](#java学习常见问题汇总)
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
