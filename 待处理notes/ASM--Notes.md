# ASM--Notes  




文件系统存在的问题：  
* 并发I/O困难    
* 镜像问题  （PV）
* 扩展问题  --由文件系统边界决定 

ASM 
* 的逻辑卷由于分布在不同的盘上，所以解决的并发I/O的问题。
* 镜像问题解决了。
* 由于是逻辑上的逻辑卷，解决了不可随意拓展的问题。  


ASM发展历史  
ASM的自动化程度比逻辑卷要好，是Oracle自己出的一套技术，本质上也属于逻辑卷技术范畴，以前属于原厂商研发的技术。  90年代产生。
LVM最早不是面向数据库的，原厂商提供的一套软件技术，用于解决磁盘问题。80年代产生。 


ASM系统对DBA的要求：
能看懂相关信息就可以，因为自动化程度很高。  
Oracle推荐在集群中的应用首选ASM，但是在实际工作中，一般使用磁盘柜来做镜像（raid5），这种镜像称为外部镜像。  
条带是必须做的  



asmfile
asmdiskgroup
asmdisk  



asm_poewr_limit为0表示不自动均衡，1~11表示不同级别的力度，1表示慢慢平衡，11为最大平衡使用百分之百的力气用于去平衡。  
此参数可以再中途修改。 


ASM磁盘组命令：  
v$asm_diskgroup  
v$asm_disk  
v$asm_alias  

建立磁盘组：
asmcmd  <==>  模拟的Linux命令形式   

asmca

grid下：  
sqlplus / as sysasm去操作磁盘组di 





linux--->crs(asm(ocr_vote))
linux启动顺序：  
Linux--> crs 
+ocr_vote  
asm instance  














