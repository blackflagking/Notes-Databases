#【调优】生产系统上捕捉SQL Tunning Set

##what
在生产系统上捕捉SQL负载
生成各项指标比较用于调优


##how
cursor cache
awr snapshots
awr baseline
another sql set
10046 trace file(11g+)

##why

##where
> 常用采集方式  
> >游标采集  
> > >1.游标可以最大限度的采集信息  
> >  2.生产环境捕捉频率 4次/天  
> >     
> >AWR实力资料库  
> > >1.AWR报告可以采集TOP的SQL语句  
> > >2.生产环境采集一个月的数据


> sysaux空间存放内容：
> > AWR报告相关信息
> > em信息相关信息
> > 计划基线的报告（可通过包来调）




