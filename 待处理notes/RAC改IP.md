# RAC改IP   



RAC改IP的步骤


是否改网段
否：直接改底层文件
是：
1. 关闭数据库
2. 禁用、关闭LINSTENER、VIP、SCAN
3. 
> crs关闭  
> 修改OCR中的注册表
> <font color="red">Linux网卡配置文件</font>
> crs开 
4. 修改OCR网络配置
5. 使能开启、关闭LISTENER、VIP、SCAN 
6. 验证是否能用