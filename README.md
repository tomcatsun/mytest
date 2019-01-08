# mytest
批量插入数据测试

#1.首先创建一张student表
create table student(id int(20) NOT NULL AUTO_INCREMENT,sex char(1),name varchar(20), primary key(id));

--2.创建两个随机函数，用来生成用户名和性别
#随机生成name
delimiter 
CREATE FUNCTION rand_name(n INT) 
RETURNS VARCHAR(20) 
BEGIN 
DECLARE chars_str varchar(100) DEFAULT  
'abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ'; 
DECLARE return_str varchar(255) DEFAULT ''; 
DECLARE i INT DEFAULT 0; 
WHILE i < n DO 
SET return_str = concat(return_str,substring(chars_str ,  
FLOOR(1 + RAND()*52 ),1));#52代表在52个字母中随即找出一个 
SET i = i +1; 
END WHILE; 
RETURN return_str; 
END


#随机生成性别
delimiter $$
CREATE FUNCTION rand_sex(n INT)
RETURNS VARCHAR(20)
BEGIN
DECLARE chars_str varchar(100) DEFAULT '01';
DECLARE return_str varchar(255) DEFAULT '';
DECLARE i INT DEFAULT 0;
WHILE i < n DO
SET return_str = concat(return_str,
substring(chars_str , FLOOR(1 + RAND()*2 ),1));
SET i = i +1;
END WHILE;
RETURN return_str;
END
$$
 
# 3.写存储过程，思路是首先生成数据，然后一起commit
delimiter $$
CREATE PROCEDURE insertData()
begin
set @a=1;     #学号
SET autocommit=0;
while @a<1000000 do     #如果@a<2000010001则返回true，继续执行
 #如果@a<2000010001则返回true，继续执行
set @b=rand_name(5);     #姓名，随即赋值，值为5位a-zA-Z的任意组合
set @c=rand_sex(1);           #性别，随即赋值，值为1位，0或者1
insert into student(name,sex) values(@b,@c);
set @a=@a+1;
end while;
COMMIT;
End
$$

# 4调用函数 
call insertData

select count(*)from student
