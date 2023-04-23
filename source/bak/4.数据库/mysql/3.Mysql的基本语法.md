## 建库

```mysql
 create database test;
```

## 查看当前库

```mysql

show databases;
```

## 进入当前使用的库

```mysql
use test;
```

## 建表

```mysql
create table test(
    id varchar(10) primary key not null comment 'id',
    name varchar(10) not null comment '名字'
);

create table test2(    
     id varchar(10) primary key not null comment 'id', 
     test1_id  varchar(10) not null comment '',
     name2 varchar(10) not null comment '' 
);


```

## 查看库下表

```mysql
show tables;
```

## 查看表结构

```mysql
desc test;
```

## 插入数据

```mysql
insert into test(id,name)values('1','a');
```

## 查询

```mysql
select * from test where id='1';
```
## 更新
```mysql
update test set name ='b' where id='1';
```

## 删除
```mysql
    delete from  test where id='1';
```

## 关联查询

```mysql

select * from test left join test2 on test.id=test2.test1_id;

select * from test right join test2 on test.id=test2.test1_id;

select * from test full  join test2;
```
## 分组

```mysql

select * from test group by id ;

select * from test group by id having name='b' ;

```

## 索引

```mysql

    //普通索引
    create index name on test(name(10));

    //创建唯一索引
    create UNIQUE index name on test(name(10));

    //创建全文索引
    create FULLTEXT index name on test(name(10));

   //删除索引
   drop index name on test
```

## 导出数据

```mysql
 SELECT * FROM test  INTO OUTFILE '/tmp/test.txt';
```

## 导入数据

```mysql

  #执行sql
  source /tmp/test.sql;

  #导入文本
  load data local infile 'test.txt' into table test;
```
## 函数
- sum()
- ifnull()
- count()
- distinct 去重
- replace()


