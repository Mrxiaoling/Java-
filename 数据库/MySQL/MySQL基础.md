# 一、MySQL基础

## 1.1、基本用户操作

### 1、登录：

```sql
mysql  -u [username]  -p ;
```



### 2、创建用户：

```sql
mysql> insert into mysql.user(Host,User,Password) 
values("localhost","username",password("userpassword"));
--这样就创建了名为username，密码为：.....的用户
--此处的"localhost"，是指该用户只能在本地登录，不能在另外一台机器上远程登录。如果想远程登录的话，将"localhost"改为"%"，表示在任何一台电脑上都可以登录。也可以指定某台机器可以远程登录。
```

### 3、用户授权：

3.1、以ROOT身份登陆：

```sql
mysql -u root -p
```

3.2、为用户先创建一个数据库

```SQL
mysql>create database testDB;
```

3.3、授权用户拥有某个数据的权限

```sql
mysql>grant all privileges on testDB.* to username@localhost identified by 'password';
--刷新系统权限表
mysql>flush privileges;
--格式：grant 权限 on 数据库.* to 用户名@登录主机 identified by "密码";　

--指定一部分权限给用户
mysql>grant select,update on testDB.* to username@localhost identified by 'password';
mysql>flush privileges;

--授权用户拥有所有数据库的某些权限
mysql>grant select,delete,update,create,drop on *.* to username@"%" identified by "password";
--@"%" 表示对所有非本地主机授权，不包括localhost。（localhost地址设为127.0.0.1，如果设为真实的本地地址，不知道是否可以，没有验证。


```

### 4、删除用户

1）以root用户登陆

2）删除用户

```SQL
Delete FROM user Where User='test' and Host='localhost';
--删除账户及权限
drop user 用户名@'%';
drop user 用户名@ localhost; 

```

### 5、修改指定用户密码

1）以root登陆

2）

```sql
mysql>update mysql.user set password=password('新密码') where User="test" and Host="localhost";
mysql>flush privileges;
```

## 1.2、基本数据库操作

### 1、列出数据库，表

```sql
--列出所有数据库
mysql>show database;
--列出所有表
mysql>show tables
```

### 2、删除数据库，表

```sql
--删除数据库
mysql>drop database 数据库名;
--删除表
mysql>drop table 数据表名;
```

### 3、切换数据库

```sql
mysql>use '数据库名';
```

### 4、显示数据表结构

```sql
mysql>describe 表名;
　--or
mysql>describe 表名;
```

### 5、数据表操作

```sql
create table [表名]（
	name varchar (20),
	owener varchar(20),
	birth DATE,
);
--插入数据到数据表
insert into [表名] values("","","",....);

--删除数据
delete from [表名] where [字段名] = "";

--修改数据
update [表名] set [要修改的字段名]="";

```

## 1.3、MySQL建表约束

### 1.3.1、主键约束

能唯一确定一张表中的一条数据，也就是我们通过给某个字段添加约束，使字段不重复且不为空

```sql
create table user(
	id int primary key,
    name varchar(20)
);

--复合主键
--复合主键中有任何一个字段不同即为不同的数据
create table user(
	id int,
    name varchar(20)，
    password varchar(20),
    primary key (id,name)
);
```

### 1.3.2、自增约束

```sql
create table user(
	id int primary key auto_increment,
    name varchar(20)
);
--可以省略id,id自增
insert into user (name) values('zhangsan');
insert into user (name) values('zhangsan');
```

### 1.3.3、唯一约束

约束修饰的字段的值不可以重复

```SQL
create table user5(
	id int unique,
	name varchar(20)
);
--唯一约束的复合主键中，两个键在一起不重复即为不重复
--比如unique(id,name);
```

### 1.3.4、非空约束

修饰的字段不能为空，null

```sql
create table user9(
id int,
name varchar(20) not null
);
```

### 1.3.5、默认约束

当我们插入字段值的时候，如果没有传值，就会使用默认值

```SQL
create table user10(
	id int,
	name varchar(20),
	age int default 10
);
```

### 1.3.6、外键约束

--涉及到两个表：父表（主表），子表（副表）

```SQL
-- 班级
create table classes(
	id int primary key,
	name varchar(20)
);


-- 学生表
create table students(
	id int primary key,
	name varchar(20),
	class_id int,
	foreign key(class_id) references classes(id)
);

insert into students values(1001,'张三',1);
insert into students values(1002,'张三',2);
insert into students values(1003,'张三',3);
insert into students values(1004,'张三',4);
insert into students values(1005,'lisi',5);
insert into classes values(1,'一班');
insert into classes values(2,'二班');
insert into classes values(3,'三班');
insert into classes values(4,'四班');

--1、主表中没有的数据，在副表中是不可以使用的
--2、主表中的记录被副表引用，是不可以被删除的

```



### 1.3.7、在已创建好的表中增加，删减，修改约束

#### 1）增加约束

```sql
--主键约束
alter table [表名] add primary key(id);
--唯一约束
alter table [表名] add unique  [字段名]；
```

#### 2)删除约束

```SQL
--主键约束
alter table [表名] drop primary key;
--唯一约束
alter table [表名] drop  index [字段名]；
```

#### 3)使用modify修改

```SQL
--主键名
alter table [表名] modify [字段名] [类型] primary key;
--唯一约束
alter table [表名] modify [字段名] [修饰类型] unique；
```



## 1.4、设计范式

### 1、表设计第一范式

--数据表中的所有字段都是不可分割的原子值

| id   | name  | adress                 |
| ---- | ----- | ---------------------- |
| 1    | li    | 中国广东省梅州市平远县 |
| 2    | zhang | 中国广东省梅州市平远县 |
| 3    | wang  | 中国广东省梅州市平远县 |

------如上表所示，假设有需求要按市级地址查找，上表就不能满足要求，也不符合第一范式

| id   | name  | country | province | city   | county |
| ---- | ----- | ------- | -------- | ------ | ------ |
| 1    | li    | 中国    | 广东省   | 梅州市 | 平远县 |
| 2    | zhang | 中国    | 广东省   | 梅州市 | 平远县 |
| 3    | wang  | 中国    | 广东省   | 梅州市 | 平远县 |

