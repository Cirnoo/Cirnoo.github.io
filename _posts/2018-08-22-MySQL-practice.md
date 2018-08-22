---
layout: post
title:  "MySQL 练习题(一)"
date:   2018-8-22
categories: MySQL笔记
tags: MySQL
excerpt: MySQL初学小练习
---

这几天在学MySQL，整理了学校发的练习题，记录在这里。

###  原始数据

```sql
use `0818`; -- database is '0818'
create table Student(Snum varchar(10),Sname nvarchar(10),Sage datetime,Ssex nvarchar(10));
insert into Student values('01' , N'赵雷' , '1990-01-01' , N'男');
insert into Student values('02' , N'钱电' , '1990-12-21' , N'男');
insert into Student values('03' , N'孙风' , '1990-05-20' , N'男');
insert into Student values('04' , N'李云' , '1990-08-06' , N'男');
insert into Student values('05' , N'周梅' , '1991-12-01' , N'女');
insert into Student values('06' , N'吴兰' , '1992-03-01' , N'女');
insert into Student values('07' , N'郑竹' , '1989-07-01' , N'女');
insert into Student values('08' , N'王菊' , '1990-01-20' , N'女');
create table Course(Cnum varchar(10),Cname nvarchar(10),Tnum varchar(10));
insert into Course values('01' , N'语文' , '02');
insert into Course values('02' , N'数学' , '01');
insert into Course values('03' , N'英语' , '03');
create table Teacher(Tnum varchar(10),Tname nvarchar(10));
insert into Teacher values('01' , N'张三');
insert into Teacher values('02' , N'李四');
insert into Teacher values('03' , N'王五');
create table SC(Snum varchar(10),Cnum varchar(10),score decimal(18,1));
insert into SC values('01' , '01' , 80);
insert into SC values('01' , '02' , 90);
insert into SC values('01' , '03' , 99);
insert into SC values('02' , '01' , 70);
insert into SC values('02' , '02' , 60);
insert into SC values('02' , '03' , 80);
insert into SC values('03' , '01' , 80);
insert into SC values('03' , '02' , 80);
insert into SC values('03' , '03' , 80);
insert into SC values('04' , '01' , 50);
insert into SC values('04' , '02' , 30);
insert into SC values('04' , '03' , 20);
insert into SC values('05' , '01' , 76);
insert into SC values('05' , '02' , 87);
insert into SC values('06' , '01' , 31);
insert into SC values('06' , '03' , 34);
insert into SC values('07' , '02' , 89);
insert into SC values('07' , '03' , 98);
insert into SC values('09' , '03' , 98);
```

###  习题与题解
```sql
-- 1、查询"01"课程比"02"课程成绩高的学生的信息及课程分数

use `0818`;
select student.*,sc1.score score1,sc2.score score2 from student
inner join sc sc1 on sc1.snum=student.snum and sc1.cnum='01'
left join sc sc2 on sc2.snum=student.snum and sc2.cnum='02'
where sc1.score > ifnull(sc2.score,-1);

-- 1.1、查询同时存在"01"课程和"02"课程的情况

select student.*,sc1.score score1,sc2.score score2 from student
inner join sc sc1 on sc1.snum=student.snum and sc1.cnum='01'
inner join sc sc2 on sc2.snum=student.snum and sc2.cnum='02';

-- 2、存在"01"课程但可能不存在"02"课程的情况(不存在时显示为null)(以下存在相同内容时不再解释)

select student.*,sc1.score score1,sc2.score score2 from student
inner join sc sc1 on sc1.snum=student.snum and sc1.cnum='01'
left join sc sc2 on sc2.snum=student.snum and sc2.cnum='02';


-- 3、查询平均成绩大于等于60分的同学的学生编号和学生姓名和平均成绩

select student.snum,student.Sname,avg(score) from student
left join sc on sc.Snum=student.Snum
group by student.Snum having avg(score)>60;

-- 4.1、查询在sc表存在成绩的学生信息的SQL语句。

select student.* from student
left join sc on sc.snum=student.Snum
group by Sname having count(Cnum)>0;

-- 4.2、查询在sc表中不存在成绩的学生信息的SQL语句。

select student.* from student
left join sc on sc.snum=student.Snum
group by Sname having count(Cnum)=0;

-- 5、查询"李"姓老师的数量 

select * from teacher where Tname like '李%';

-- 6、查询学过"张三"老师授课的同学的信息 

select student.* from student 
left join sc on sc.Snum=student.Snum
left join teacher on teacher.tnum=sc.Cnum
group by student.Snum having Tname='张三';

-- -- 或者可以这样
 select tnum from teacher where tname = '张三';
 select cnum from course where tnum = ( select tnum from teacher where tname = '张三');
 select snum from sc where cnum = ( select cnum from course where tnum = ( select tnum from teacher where tname = '张三'));
 select * from student where snum in(select snum from sc where cnum = ( select cnum from course where tnum = ( select tnum from teacher where tname = '张三')));
 
-- 8、查询没学过"张三"老师授课的同学的信息 

 select * from student where Snum not in  -- in 后面接一张表，可以查询是否在表中
 (select s.snum from teacher t
 inner join course c   on t.Tnum = c.Tnum
 inner join sc     on c.Cnum = sc.Cnum
 inner join student s  on sc.Snum = s.Snum
 where tname = '张三');
 
-- 9、查询学过编号为"01"并且也学过编号为"02"的课程的同学的信息

select * from student where snum in
(select sc.Snum from sc where Cnum='01') and snum in
(select sc.Snum from sc where Cnum='02');

-- 10、查询学过编号为"01"但是没有学过编号为"02"的课程的同学的信息

select * from student where snum in
(select sc.Snum from sc where Cnum='01') and snum not in
(select sc.Snum from sc where Cnum='02');

-- 11、查询没有学全所有课程的同学的信息 

select * from student where snum in 
(select sc.Snum from sc group by Snum having count(cnum)<(select count(cnum) from course));

-- 12、查询至少有一门课与学号为"01"的同学所学相同的同学的信息 

select * from student where snum in (select snum from sc where cnum in
(select cnum from sc where snum='01') and snum!='01'
group by snum);

-- 13、查询和"01"号的同学学习的课程完全相同的其他同学的信息 XX

select * from student where snum in
 (select snum from (select sc.* from 
(select cnum from sc where snum='01') a
left join sc on sc.cnum=a.cnum and sc.snum!='01') b 
group by b.snum having count(b.cnum)=count((select count(sc.Cnum) from sc where snum='01')));

-- 14、查询没学过"张三"老师讲授的任一门课程的学生姓名 

-- 见-- 8

-- 15、查询两门及其以上不及格课程的同学的学号，姓名及其平均成绩 

select  sname,s.snum,avg(score) from sc 
inner join student s  on s.snum = sc.snum
 group by s.snum having s.snum 
in(select snum from sc where score < 60 group by snum
having count(cnum)  >=2);

```

