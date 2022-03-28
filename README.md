#### Oracle数据备份恢复

* 工具：sqlplus expdp/impdp

* 版本：Oracle18

* 环境：Centos 7x

  

  **1、sqlplus登录数据库**

  ```shell
  管理员：
  # sqlplus /@实例名 as sysdba
  用户远程连接：
  # sqlplus 用户名/密码@127.0.0.1:1521/实例名
  
  ```

  **2、创建用户**（如已存用户则不需要执行这步）

  ```sql
  首先需要创建表空间
  SQL> create tablespace 表空间名 logging
       datafile 'D:\xxx\xxxxx.dbf'
       size 1024m autoextend on next 50m maxsize
       unlimited extent management local;
       
  创建用户/schema并设置默认表空间
  SQL> create user 用户名 identified by 密码 default tablespace 表空间名;
  
  授权用户的表空间
  SQL> grant dba,resource,unlimited tablespace to 用户名;
  
  查看表空间是否创建成功
  SQL> select * from v$tablespace;
  
  查看用户权限授权详情
  SQL> select * from dba_role_privs;
  ```

  

  **3、新建逻辑目录，用于指定备份文件**

  ```sql
  创建一个逻辑目录名mydata，可自定义名字
  SQL> create directory mydata '/home/oracle/mydata';
  
  查看逻辑目录是否创建成功
  SQL> select * from dba_directories;
  
  授权用户访问逻辑目录
  SQL> grant read,write on directory mydata to 用户名;
  
  ```

  **4、使用expdp工具导出数据**

  ```shell
  # expdp 用户名/密码@127.0.0.1:1521/实例名 directory=逻辑目录名 schemas=一般跟用户名一致 dumpfile=备份出来的文件名.dmp logfile=日志文件.log;
  ```

  **5、使用impdp工具导入数据**

  * 注意导入时的数据库中若不存在schemas，则需要重复第二步创建，schemas保持一致
  * 逻辑目录也需要创建，创建方法为第三步

  ```shell
  # impdp 用户名/密码@127.0.0.1:1521/实例名 directory=逻辑目录名 dumpfile=备份文件.dmp full=y logfile=日志文件.log;
  ```

  **谨慎：删除用户及数据**

  ```sql
  SQL> drop user 用户名 cascade;
  ```
