[root@core1 ~]# crs_stat check crs
CRS-0184: Cannot communicate with the CRS daemon.



[root@core1 ~]# crsctl start res ora.crsd -init
CRS-2672: Attempting to start 'ora.ctssd' on 'core1'
CRS-2676: Start of 'ora.ctssd' on 'core1' succeeded
CRS-2672: Attempting to start 'ora.evmd' on 'core1'
CRS-2672: Attempting to start 'ora.asm' on 'core1'
CRS-2676: Start of 'ora.evmd' on 'core1' succeeded
CRS-2676: Start of 'ora.asm' on 'core1' succeeded
CRS-2672: Attempting to start 'ora.crsd' on 'core1'
CRS-2676: Start of 'ora.crsd' on 'core1' succeeded
[root@core1 ~]# crsctl check cluster
CRS-4535: Cannot communicate with Cluster Ready Services
CRS-4529: Cluster Synchronization Services is online
CRS-4534: Cannot communicate with Event Manager

[root@core1 ~]# crsctl check cluster
CRS-4537: Cluster Ready Services is online
CRS-4529: Cluster Synchronization Services is online
CRS-4533: Event Manager is online

[root@core1 ~]# crsctl stat res -t -init
--------------------------------------------------------------------------------
NAME           TARGET  STATE        SERVER                   STATE_DETAILS       
--------------------------------------------------------------------------------
Cluster Resources
--------------------------------------------------------------------------------
ora.asm
      1        ONLINE  ONLINE       core1                    Started             
ora.cluster_interconnect.haip
      1        ONLINE  ONLINE       core1                                        
ora.crf
      1        ONLINE  ONLINE       core1                                        
ora.crsd
      1        ONLINE  ONLINE       core1                                        
ora.cssd
      1        ONLINE  ONLINE       core1                                        
ora.cssdmonitor
      1        ONLINE  ONLINE       core1                                        
ora.ctssd
      1        ONLINE  ONLINE       core1                    ACTIVE:135845900    
ora.diskmon
      1        OFFLINE OFFLINE                                                   
ora.drivers.acfs
      1        ONLINE  ONLINE       core1                                        
ora.evmd
      1        ONLINE  ONLINE       core1                                        
ora.gipcd
      1        ONLINE  ONLINE       core1                                        
ora.gpnpd
      1        ONLINE  ONLINE       core1                                        
ora.mdnsd
      1        ONLINE  ONLINE       core1 