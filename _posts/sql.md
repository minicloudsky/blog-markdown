---
title: 数据库课堂练习
date: 2017-12-27 11:23:35
tags: 
- 数据库
- MySQL
categories:
- 数据库
- MySQL
---

数据库curd,老师讲课挺不错的，难受的竟然用sql server 2008，老古董了，还好我用的mysql   O(∩_∩)O哈哈~
```mysql

/*学生表*/
create table student(
sno Char(4) primary key,
sname Char(8) not null,
ssex Char(2) not null,
sage smallint,
sdept Char(10) not null,
);


/*课程表*/
create table course(
cno Char(4) primary key,
cname Char(10) not null,
);

alter table course drop credit ;
/*选课表*/
create table sc(
sno Char(4) not null,
cno Char(4) not null,
cname Char(10) not null,
grade smallint not null check(grade >=0 and grade <=100),
primary key(sno,cno),
foreign key(sno) references student(sno),
foreign key(cno) references course(cno),
);

/*在课程表中增加一个credit字段*/
alter table course add credit smallint;

/*在选课表中删除cname*/
alter table sc drop column cname;

alter table sc add cname char(10) not null;

/*撤销数据表*/

create table data
(
sname char(10) not null,
snumber char(15) not null primary key,
ssex char(2) not null,
sdept char(10) not null,
scourse char(6) not null,
);
drop table data;

/*实验2*/
insert 
into student(sno,sname,ssex,sage,sdept)
values('1100','小小佳','女','21','外国语'),
('1101','陈晴毅','女','21','医学检验'),
('1102','陈金光','男','22','中医推拿'),
('1103','裴秀雅','女','21','法律'),
('1104','林雅倩','女','20','园艺'),
('1105','裴玉冰','女','21','师范')
;

insert into course(cno,cname,credit)
values('1','数据库概论','8'),
('2','数据库概论','7'),
('3','数据结构','6'),
('4','高数','3');

insert into sc(sno,cno,grade,cname)
values('1100','1','90','英语'),
('1101','1','87','英语'),
('1102','2','45','数据库概论'),
('1102','3','67','数据结构'),
('1102','1','15','英语'),
('1103','1','99','英语'),
('1103','2','76','数据库概论'),
('1103','3','66','数据结构'),
('1104','4','55','高数'),
('1105','2','81','数据库概论');

/*单条记录修改*/

update student set sage = 25 where sno ='1101';
update student set sage  = 34 where sno = '1102';
update student set sdept = 'CS' where sno = '1101';
update student set sdept = 'CS' where sno ='1102';
/*多条记录修改*/
update student 
set sage = sage+1;
/*单条记录增加*/
insert into student(sno,sname,ssex,sage,sdept)
values('1107','张亚鹏','男','20','动物医学');

/*多条记录增加*/
insert into student(sno,sname,ssex,sage,sdept)
values('1108','张三','男','19','经管学院'),
('1109','王翔','男','23','生物工程'),
('1110','赵四','女','21','艺术学院');

/*单条记录删除*/
delete from student where sname = '赵四';

/*带子查询的多条记录删除*/
delete from student  where sdept = '生物工程';

/*给sc表建立索引,先sno按升序，再按cno降序*/

create unique index scno on sc(sno asc,cno desc);
/*删除上述索引*/
drop index scno on sc;

/*在sc表中添加字段sname*/
alter table sc add sname char(8);

drop table sc;

create table sc
(
sno char(4) not null,
cno char(4) not null,
cname char(10) not null,
sname char(10) not null,
grade smallint check(grade >=0 and grade <=100)
primary key(sno,cno),
foreign key(sno) references student(sno),
foreign key(cno) references course(cno),
);

select * from student;

/*在sc表插入10条记录course : 英语数据库数据结构高数*/
insert into sc(sno,cno,grade,cname,sname)
values('1100','1','45','英语','小小佳'),
('1105','2','67','数据库','裴玉冰'),
('1107','3','78','数据结构','张亚鹏'),
('1101','1','67','英语','陈晴毅'),
('1101','2','89','数据库','陈晴毅'),
('1101','4','77','高数','陈晴毅'),
('1102','1','89','英语','陈金光'),
('1108','2','65','数据库','张三'),
('1103','4','87','高数','裴秀雅'),
('1104','3','71','数据结构','高数');

update student 
set sname = '小王'
where sno = '1108';
/*建立视图view1，要求有sno，sname,cname,grade四个字段*/
create view view1
as select sno,sname,cname,grade
from sc;
/*建立视图view2,要求有sno,ssex,sage三个字段*/
create view view2
as
select sno,ssex,sage
from student

/*(1)查询全体学生的学号和姓名。*/
select sno,sname from student;
/*(2)查询全体学生的详细记录。*/
select * from student;
/*(3)查询全体学生的姓名和出生年份。*/
select sname,2017-sage from student;
/*(4)查询选修了课程的学生的学号。*/
select sno from student;
/*(5)查询计算机系的学生的姓名。*/
select sname from student where sdept in ('CS');
/*(6)查询年龄在20岁以下的计算机系的学生的学号和年龄。*/
select sno,sage from student where sage<20 ; 
/*(7)查询年龄在20—22岁的学生的学号。*/
select sno from student where sage>=20 and sage <=22;
/*(8)查询信息系、计算机系和外语系的学生的信息。*/
select * from student where sdept ='CS' or sdept='外国语';

select * from student;
/*(9)查询姓“王”的学生的信息。*/
insert into student(sno,sname,ssex,sage,sdept) 
values('1110','王菲','女','24','音乐');
select * from student where sname like '王%';
/*(10)查询选修了3号课程的学生的学号及成绩，结果按分数的降序排列。*/
select sno,grade 
from sc
where cno='3'
order by grade desc;

/*(11)查询学生总人数。*/
select count(*) from student;

/*(12)查询选修1号课程的学生的最高分。*/
select max(grade)
from sc 
where cno = '1';

/*(13)查询选修了3门以上课程的学生的学号。*/
insert into sc(sno,cno,cname,sname,grade)
values('1101','3','数据结构','陈晴毅','99');
select sno from student where sno in
(select sno
from sc 
group by sc.sno
having count(*) >3);

/*(14)查询每个学生及其选修课程的情况。*/
select sno,sname,cno,cname
from sc;
/*(15)查询选修2号课程且分数在90分以上的所有学生。*/
select * from student,sc
where student.sno = sc.sno and sc.grade>90;
/*(16)查询每个学生的学号、姓名、选课名及成绩。*/
select sno,sname,cname,grade
from sc;
/*(17) 查询与“张三”在同一系的学生。*/
insert into student(sno,sname,ssex,sage,sdept)
values('1111','张三','男','23','CS');
select * from student
where sdept in
(select sdept
from student where sname='张三');

/*(18) 查询选修了课程名为“数据库”的学生学号和姓名。*/
select sno,sname 
from student where sno in
(select sno 
from sc 
where sc.cname='数据库'
);

/*
(19) 查询没有选修1号课程的学生的姓名。*/
select sname from student 
where not exists
(select * from sc where sc.sno=student.sno and sc.cno='1');

/*(20) 查询选修了所有课程的学生的姓名。*/

select sname from student where sno in
(select sno from sc where
sno = '1' and cno ='2' and cno='3' and cno = '4');

insert into sc(sno,cno,grade,cname,sname)
values('1103','2','91','数据库','裴秀雅');

select sname from student 
where not exists
(select * from course
where not exists
( select * from sc 
where sc.sno = student.sno and sc.cno = course.cno));

select sname from student where sno in 
(select sno
from sc
where sc.sno = student.sno
group by sno having count(*) =
(select count(*) from course));

```
