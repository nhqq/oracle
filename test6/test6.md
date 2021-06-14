# 姓名：聂涵 学号：201810414215 班级：软件工程18级2班
# 实验6（期末考核） 基于Oracle的```电影订票系统```数据库设计 

- 期末考核要求

  - 自行设计一个信息系统的数据库项目，自拟```某项目```名称。
  - 设计项目涉及的表及表空间使用方案。至少5张表和5万条数据，两个表空间。
  - 设计权限及用户分配方案。至少两类角色，两个用户。
  - 在数据库中建立一个程序包，在包中用PL/SQL语言设计一些存储过程和函数，实现比较复杂的业务逻辑，用模拟数据进行执行计划分析。
  - 设计自动备份方案或则手工备份方案。
  - 设计容灾方案。使用两台主机，通过DataGuard实现数据库整体的异地备份(可选)。

## 实验注意事项，完成时间： 2021-6-15日前上交

- 实验在自己的计算机上完成。
- 文档`必须提交`到你的Oracle项目中的test6目录中。文档必须上传两套，因此你的test6目录中必须至少有两个文件：
  - 一个是Markdown格式的，文件名称是test6.md。
  - 另一个是Word格式的，文件名称是test6_design.docx。参见[test6_design.docx](./test6_design.docx)，文件不用打印。
- 上交后，通过这个地址应该可以打开你的源码：https://github.com/nhqq/oracle/tree/master/test6
- 文档中所有设计和数据都必须是你自己独立完成的真实实验结果。不得抄袭，杜撰。

## 评分标准

| 评分项|评分标准|满分|
|:-----|:-----|:-----|
|文档整体|文档内容详实、规范，美观大方|10|
|表设计|表，表空间设计合理，数据合理|20|
|用户管理|权限及用户分配方案设计正确|20|
|PL/SQL设计|存储过程和函数设计正确|30|
|备份方案|备份方案设计正确|20|

# 1.表设计

## 1.1数据字典

user表

| 列名        | 数据类型     | 字段类型 | 是否为空 | 默认值 | 主/外键 | 备注   |
| ----------- | ------------ | -------- | -------- | ------ | ------- | ------ |
| password    | varchar(255) | varchar  | YES      |        |         | 密码   |
| picture_id  | int          | int      | YES      |        | 外      | 图片id |
| user_gender | int          | int      | NO       | 0      |         | 性别   |
| user_id     | int          | int      | NO       |        | 主      | 用户id |
| username    | varchar(255) | varchar  | YES      |        |         | 用户名 |

movie表

|       列名 | 数据类型     | 字段类型 | 是否为空 | 默认值 | 主/外键 | 备注     |
| ---------: | ------------ | -------- | -------- | ------ | ------- | -------- |
|      actor | text         | text     | YES      |        |         | 主演     |
|       info | text         | text     | YES      |        |         | 电影简介 |
|   movie_id | int          | int      | NO       |        | 主      | 电影id   |
| movie_name | varchar(255) | varchar  | YES      |        |         | 电影名   |
| picture_id | int          | int      | YES      |        | 外      | 图片id   |

movie_cinema表

| 列名            | 数据类型 | 字段类型 | 是否为空 | 默认值 | 主/外键 | 备注          |
| --------------- | -------- | -------- | -------- | ------ | ------- | ------------- |
| cinema_id       | int      | int      | YES      |        | 外      | 电影院id      |
| movie_cinema_id | int      | int      | NO       |        | 主      | 电影_电影院id |
| movie_id        | int      | int      | YES      |        | 外      | 电影id        |

hall表

| 列名      | 数据类型       | 字段类型 | 是否为空 | 默认值 | 主/外键 | 备注     |
| --------- | -------------- | -------- | -------- | ------ | ------- | -------- |
| capacity  | int            | int      | YES      |        |         | 票价     |
| cinema_id | int            | int      | YES      |        | 外      | 电影院id |
| hall_id   | int            | int      | NO       |        | 主      | 播放厅id |
| hall_name | varchar  (255) | varchar  | YES      |        |         | 播放厅名 |

order表

| 列名          | 数据类型     | 字段类型 | 是否为空 | 默认值 | 主/外键 | 备注     |
| ------------- | ------------ | -------- | -------- | ------ | ------- | -------- |
| hall_id       | int          | int      | YES      |        | 外      | 播放厅id |
| order_id      | int          | int      | NO       |        | 主      | 电影票id |
| order_site    | varchar(255) | varchar  | YES      |        |         | 座位     |
| purchase_date | datetime     | datetime | YES      |        |         | 购票日期 |
| session_id    | int          | int      | YES      |        | 外      | 场次id   |
| user_id       | int          | int      | YES      |        | 外      | 用户id   |

picture表

| 列名        | 数据类型       | 字段类型 | 是否为空 | 默认值 | 主/外键 | 备注     |
| ----------- | -------------- | -------- | -------- | ------ | ------- | -------- |
| picture_id  | int            | int      | NO       |        | 主      | 图片id   |
| picture_url | varchar  (255) | varchar  | YES      |        |         | 图片地址 |

session表

| 列名            | 数据类型        | 字段类型 | 是否为空 | 默认值                           | 主/外键 | 备注          |
| --------------- | --------------- | -------- | -------- | -------------------------------- | ------- | ------------- |
| date            | datetime        | datetime | YES      |                                  |         | 开场时间      |
| hall_id         | int             | int      | YES      |                                  | 外      | 播放厅id      |
| movie_cinema_id | int             | int      | YES      |                                  | 外      | 电影_电影院id |
| price           | decimal  (10,2) | decimal  | YES      |                                  |         | 价格          |
| session_id      | int             | int      | NO       |                                  | 主      | 场次id        |
| site            | varchar  (255)  | varchar  | NO       | 000000000000000  000000000000000 |         | 场次座位情况  |

cinema表

| 列名         | 数据类型       | 字段类型 | 是否为空 | 默认值 | 主/外键 | 备注       |
| ------------ | -------------- | -------- | -------- | ------ | ------- | ---------- |
| address      | varchar  (255) | varchar  | YES      |        |         | 电影院地址 |
| cinema_id    | int            | int      | NO       |        | 主      | 电影院id   |
| cinema_name  | varchar  (255) | varchar  | YES      |        |         | 电影院名   |
| cinema_phone | varchar  (255) | varchar  | YES      |        |         | 联系方式   |
| picture_id   | int            | int      | YES      |        | 外      | 图片id     |

## 1.2设计表空间

```sql
create tablespace tspace1
datafile 'D:\environment\oracle\oradata\ORCL\tspace1.DBF'
size 200M
autoextend on;

create tablespace tspace2
datafile 'D:\environment\oracle\oradata\ORCL\tspace2.DBF'
size 200M
autoextend on;
```

![](img\image-20210614212949077.png)

![](img\image-20210614213226838.png)

## 1.3设计表

cinema

~~~sql
create table cinema(
cinema_id number primary key,
picture_id number,
cinmea name varchar(20)not nu11,
address varchar(20) not null);
~~~



![](img\image-20210614225538911.png)

movie

~~~sql
create table movie (
cinema_ id number primary key,
cinmea name varchar(20) not null,
actor varchar(20) not nul1,
picture_id number,
info varchar (255) );
~~~

![](img\image-20210614225610463.png)

hall

~~~sql
create table hall(
hall_id number primary key,
cinema_id number,
hall_name varchar(20) not null,
capacity number not null
);
~~~

![](img\image-20210614231932321.png)

order

~~~sql
create table order1(
order_id number primary key,
site varchar(50) not null,
odate varchar(20) not null,
hall_id number,
session_id number,
user_id number);
~~~

![](img\image-20210614232140832.png)

picture

~~~sql
create table picture(
picture_id number primary key,
url varchar(20) not null);
~~~

![](img\image-20210614232233883.png)



session

~~~sql
create table session1(
session_id number primary key,
sdate varchar(20) not null,
price varchar(20) not null,
site varchar(20) not null,
hall_id number,
movie_cinema_id number);
~~~

![](img\image-20210614232256552.png)

user

~~~sql
create table user1(
user_id number primary key);
alter table user1 add username varchar(20);	
alter table user1 add password varchar(20);
alter table user1 add gender varchar(20);
alter table user1 add picture_id number;
~~~

![](C:\Users\15615\Desktop\image-20210614232628649.png)

```sql
CREATE TABLE movie_cinema(
  2  movie_cinema_id number primary key,
  3  movie_id number,
  4  cinema_id number);
```

![](img\image-20210615005418501.png)



## 1.4插入数据

~~~sql
<!--插入演播厅-->
INSERT INTO hall VALUES (1, 1, '1号演播厅', 30);
INSERT INTO hall VALUES (2, 1, '2号演播厅	', 30);
INSERT INTO hall VALUES (3, 1, '3号演播厅', 30);
INSERT INTO hall VALUES (4, 2, 'A演播厅', 30);
INSERT INTO hall VALUES (5, 2, 'B演播厅', 30);
INSERT INTO hall VALUES (6, 3, 'C演播厅', 30);
INSERT INTO hall VALUES (7, 3, 'D演播厅', 30);
INSERT INTO hall VALUES (8, 4, 'E演播厅', 30);
INSERT INTO hall VALUES (9, 4, 'F演播厅', 30);
INSERT INTO hall VALUES (10, 5, 'G演播厅', 30);
INSERT INTO hall VALUES (11, 5, 'H演播厅', 30);
INSERT INTO hall VALUES (12, 6, 'I演播厅', 30);
INSERT INTO hall VALUES (13, 6, 'J演播厅', 30);
INSERT INTO hall VALUES (14, 7, 'K演播厅', 30);
INSERT INTO hall VALUES (15, 7, 'L演播厅', 30);
INSERT INTO hall VALUES (16, 8, 'M演播厅', 30);
INSERT INTO hall VALUES (17, 9, 'N演播厅', 30);
INSERT INTO hall VALUES (18, 10, 'O演播厅', 30);
INSERT INTO hall VALUES (19, 11, 'P演播厅', 30);
<!--插入电影院-->
INSERT INTO cinema VALUES(1,1,'金逸国际影城','地址：昌黎县碣阳大街北侧民生路东(民生广场商业楼二楼)');
INSERT INTO cinema VALUES(2,'中影国际影城','地址：海港区秦皇岛开发区秦皇西大街88号',2);

<!--插入5万条电影-->
declare
i number := 2;
begin
for i in 2..50000 loop
insert into movie(movie_id,movie_name,actor,picture_id,info)
values
(i,'电影名','演员',i,'介绍');
end loop;
commit;
end;

<!--插入关系电影于电影院-->
declare
i number :=1;
begin
for i in 1..25000 loop
insert into movie_cinema(movie_cinema_id,movie_id,cinema_id)
values
(i,i,i);
end loop
commit;
end;


declare
i number :=25001;
begin
for i in 25001..50000 loop
insert into movie_cinema(movie_cinema_id,movie_id,cinema_id)
values
(i,i,i);
end loop
commit;
end;
<!--插入场次-->
declare
i number :=1;
begin
for i in 1..20 loop
insert into session1(session_id,sdate,price,site,hall_id,movie_cinema_id)
values
(i,'2021-06-10','30','30',i,i);
end loop
commit;
end;
<!--插入用户-->
insert into user1 values(1,'niehan','123','男','3');
~~~



![](img\image-20210615002022892.png)

![](img\image-20210614235115941.png)

![](img\image-20210615005121751.png)

![](img\image-20210615005920656.png)

![](img\image-20210615010409803.png)

![](img\image-20210615011034268.png)

![](img\image-20210615011234486.png)



# 2.用户管理

## 2.1创建角色

```sql
create role role1;
create role role2;
```

![](img\image-20210614214158846.png)

## 2.2分配权限

~~~sql
grant connect,resource,dba,CREATE TABLE,CREATE VIEW to role1;
~~~

![](img\image-20210614215318710.png)

## 2.3创建用户

~~~sql
CREATE USER user1
IDENTIFIED BY 123
DEFAULT TABLESPACE tspace1
TEMPORARY TABLESPACE temp;

CREATE USER user2
IDENTIFIED BY 123
DEFAULT TABLESPACE tspace2
TEMPORARY TABLESPACE temp;
~~~

![](img\image-20210614215415310.png)

## 2.4分配权限给角色

~~~sql
grant role1 to user1;
grant connect,resource to role2;
grant role2 to user2;
~~~



![](img\image-20210614215621209.png)

## 2.5切换登录user1

~~~sql
conn user1/123;
~~~

![](img\image-20210614223955264.png)



# 3.PL/SQL设计

## 3.1设计存储过程

~~~sql
create or replace PACKAGE MyPack IS
	-- 函数
  FUNCTION Get_BookAmount(T NUMBER) RETURN NUMBER;
  	-- 存储过程
  PROCEDURE Get_READERS(V_SEX VARCHAR);
END MyPack;
/

FUNCTION Get_BookAmount(T NUMBER) RETURN NUMBER
  AS
    N NUMBER(20,2);
    BEGIN
      SELECT COUNT(*) into N  FROM BOOK B;
      RETURN N;
    END;

PROCEDURE Get_READERS(V_SEX VARCHAR)
  AS
    LEFTSPACE VARCHAR(2000);
    begin
      for v in
      (SELECT ID,NAME,SEX,PHONE,PHOTO FROM READER WHERE V_SEX = SEX)
      LOOP
        DBMS_OUTPUT.PUT_LINE(NAME);
      END LOOP;
    END;
END MyPack;
/
~~~

# 4.备份方案

## 4.1手工方案

手工备份数据库：就是将数据文件导出到磁盘，恢复的时候直接导入就行

> 导出／导入 利用Export可将数据从数据库中提取出来，利用Import则可将提取出来的数据送回到Oracle数据库中去。 简单导出数据（Export）和导入数据（Import） Oracle支持三种方式类型的输出： 
>
> （１）、表方式（T方式），将指定表的数据导出。 
>
> （２）、用户方式（U方式），将指定用户的所有对象及数据导出。 
>
> （３）、全库方式（Full方式），瘵数据库中的所有对象导出。 数据导入（Import）的过程是数据导出（Export）的逆过程，分别将数据文件导入数据库和将数据库数据导出到数据文件。

## 4.2自动备份

```sql
run{
configure retention policy to redundancy 1;
configure controlfile autobackup on;
configure controlfile autobackup format for device type disk to 'D:\environment\oracle';
configure default device type to disk;
crosscheck backup;
crosscheck archivelog all;
allocate channel c1 device type disk;
allocate channel c2 device type disk;
allocate channel c3 device type disk;
backup incremental level 0 database format 'D:\environment\oracle\level0_%d_%T_%U.bak';
report obsolete;
delete noprompt obsolete;
delete noprompt expired backup;
delete noprompt expired archivelog all;
release channel c1;
release channel c2;
release channel c3;
}
```