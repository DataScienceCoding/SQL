1. ROOT密码修改
1).打开/etc/my.cnf,在[mysqld]小节加入一行"skip-grant-tables";
2).重启mysql服务:service mysqld restart;
3).登录mysql,输入:mysql;
4).选择mysql库,输入:use mysql;
5).修改root密码:update user set password=password('newpassword') where user = 'root';
6).恢复my.cnf之前的配置,重启mysql便可使用新密码登录。
注: 高版本的msyql库中user表没有password字段，与之对应的字段为authentication_string，
同时要将password_expired字段设为'N'。
另外也可试用mysqladmin进行密码修改:mysqladmin -u root password 新密码(mariadb可用)

2. 用户创建、登陆、删除
mysql>insert into mysql.user(Host,User,Password) values('localhost','admin',password('admin'));
mysql>flush privileges;
mysql> grant all privileges on *.* to username@'%' identified by 'password' with grant option;
mysql> grant select,update,delete on *.* to username@'%' identified by 'password';
mysql> flush privileges;
  DOS> mysql -u用户名 -p密码 -h服务器IP;
mysql> delete from user where user=username;
mysql> drop user username@'%';
mysql> drop user username@localhost;

3. 数据库创建与删除，备份与还原
创建:
mysql> CREATE DATABASE dbname DEFAULT CHARACTER SET utf8 COLLATE utf8_general_ci;
mysql> show databases;
mysql> use dbname;
删除:
mysql> DROP DATABASE dbname;
备份:
mysql> mysqldump -u用户名 -p密码 -h服务器IP dbname > 备份文件名.sql
mysql> 输入密码 >>
还原:
mysql> use dbname;
mysql> source 备份文件名.sql;

4. Mysql行聚合函数
group_concat(column_name): 将字段column_name聚合成一行并以逗号分隔
group_concat(column_name SEPARATOR 'separator') :将字段column_name聚合成一行并以separator分隔

5. Mysql系统表
Mysql系统表所在库是information_schema，如访问系统表tables可以用show tables，
也可用select*from information_schema.tables

6. 常用函数

CONCAT(str1,str2,...,strn)
字符连接函数

EXTRACT(date)
用于返回日期/时间的单独部分，比如年、月、日、小时、分钟等等。

concat_ws(separator,str1,str2,...,strn)
表示concat with separator,即有分隔符的字符串连接
与concat不同的是, 执行concat_ws不会因为NULL值而返回NULL

UPPER(str)
将字符串改为大写字母

LOWER(str)
将字符串改为小写字母
 
LENGTH(str)
判定字符串长度

STRCMP(STR1,STR2)
字符串比较函数,该函数和字符集有关系，默认区分大小写
若STR1和STR2相同, 则返回 0,
若STR1小于STR2, 则返回 -1,
若STR1大于STR2, 则返回 1

INSTR(str,substr)    
返回字符串substr在字符串str第一次出现的位数
 
SUBSTRING(str,a,b)
提取字段中的一段，从字符串str的第a位开始提取，提取b个字符

SUBSTRING_INDEX(str,delim,count)
返回从字符串str分隔符delim在计数发生前的子字符串。
如果count是正的，则返回一切到最终定界符(从左边算起)的左侧；
如果count是负数，则返回一切最终定界符(从右边算起)的右侧。
注：SUBSTRING_INDEX()搜寻在delim时进行区分大小写的匹配。

LPAD(str,len,padstr)
用字符串padstr填补str左端直到字串长度为len并返回

RPAD(str,len,padstr)
用字符串padstr填补str右端直到字串长度为len并返回

LTRIM(str)
返回删除了左空格的字符串str

RTRIM(str)
返回删除了右空格的字符串str

SPACE(N)
返回由N个空格字符组成的一个字符串

REPEAT(str,count)
返回由count个字符串str连成的一个字符串

REVERSE(str)
颠倒字符串str的字符顺序并返回

ELT(N,str1,str2,str3,...)
返回第N个字符串(如N=2时返回str2)

FIELD(str,str1,str2,str3,...)
返回str等于其后的第N个字符串的序号(如str=str2时返回2)

MAKE_SET(bits,str1,str2,...)
返回一个集合 (包含由“,”字符分隔的子串组成的一个字符串)，由相应的位在bits集合中的的字符串组成。
str1对应于位0，str2对应位1，等等。在str1, str2, ...中的NULL串不添加到结果中。
mysql> SELECT MAKE_SET(1,'a','b','c');
    -> 'a'
mysql> SELECT MAKE_SET(1 | 4,'hello','nice','world');
    -> 'hello,world'
mysql> SELECT MAKE_SET(0,'a','b','c');
    -> ''

EXPORT_SET(bits,on,off,[separator,[number_of_bits]])
返回一个字符串，在这里对于在“bits”中设定每一位，你得到一个“on”字符串，并且对于每个复位(reset)的位，你得到一个“off”字符串。
每个字符串用“separator”分隔(缺省“,”)，并且只有“bits”的“number_of_bits” (缺省64)位被使用。
mysql> select EXPORT_SET(5,'Y','N',',',4)
    -> Y,N,Y,N 

LOAD_FILE(file_name)
读入文件并且作为一个字符串返回文件内容

MID(str,a,b)
提取字段中的一段，从字符串str的第a位开始提取，提取b个字符
 
LEFT(str,n)
提取字符串最左边的n个字符
 
RIGHT(str,n)
提取字符串最右边的n个字符

   CONV(N,from_base,to_base) 
   对数字N进制转换,并转换为字串返回

   BIN(N)  
   把N转为二进制值并以字串返回

   OCT(N)   
   把N转为八进制值并以字串返回

   HEX(N)  
   把N转为十六进制并以字串返回

   CHAR(N,...)    
   返回由参数N,...对应的ASCII代码字符组成的一个字串

   POSITION(substr IN str)    
   返回字符串substr在字符串str第一次出现的位置

   LOCATE(substr,str,pos)   
   返回字符串substr在字符串str中的第pos个位置起第一次出现的位置

   FIND_IN_SET(str,strlist)函数
   查询字符串strlist(以","分隔，形如1,2,a,b)中包含字符串str的记录，返回str在strlist中的排列序号

7. 查看datadir目录位置
show variables like 'datadir';

8. 存储过程
示例:
# 判断过程CountProc是否存在，若存在就drop
DROP PROCEDURE IF EXISTS Proc; 
# 创建过程CountProc
DELIMITER //
# OUT型参数指定了过程的返回结果集,过程有IN、OUT、INOUT三种参数类型
CREATE PROCEDURE CountProc(OUT param1 INT)
BEGIN
SELECT    COUNT(*) INTO  param1 FROM t3;
END//
DELIMITER ;
# 调用执行过程CountProc
CALL CountProc(); 
注意：“DELIMITER //”语句的作用是将MYSQL的结束符设置为//，因为MYSQL默认的语句结束符为分号;，为了避免与存储过程中SQL语句结束符相冲突，需要使用DELIMITER 改变存储过程的结束符，并“END//”结束存储过程。存储过程定义完毕之后再使用DELIMITER ;恢复默认结束符。

9. 自定义函数
示例:
# 创建函数NameByT
DELIMITER //
CREATE FUNCTION NameByT()
RETURNS CHAR(50)
RETURN (SELECT NAME FROM t3 WHERE id=2);
//
DELIMITER ;
# 调用函数NameByT
select NameByT(); 

10. 使用group_concat出现字符串丢失问题处理
方法: 使用group_concat_max_len系统变量来设置允许的最大长度
查看: SELECT @@group_concat_max_len;
设置: SET [SESSION | GLOBAL] group_concat_max_len = val;  (其中val为无符号整数)

11. Mysql按指定顺序排序
SELECT group_no 组号, GROUP_CONCAT(name
ORDER BY FIELD(utype,'O','P','N')) 成员
FROM define_party
GROUP BY group_no
ORDER BY CAST(group_no AS UNSIGNED);

12. 生成系统唯一随机数
select upper(replace(uuid(),'-',''));

13. Mysql同时Update多个字段
Oracle下可使用以下语句进行多个字段同时Update，如:
update opera o
set (o.instrument,o.music_path)=(
select instrument,path from opera_music m where m.opera_id=o.id)
where o.id in (select opera_id from opera_music);
但Mysql中无此语法形式，需用以下方式实现：
UPDATE opera o
LEFT JOIN opera_music m
ON o.id = m.opera_id
SET o.instrument=m.instrument,
o.music_path=m.path
WHERE o.id IN (
SELECT opera_id
FROM opera_music);
UPDATE refer_param p
LEFT JOIN (
SELECT t.id,
@rownum := @rownum + 1 AS rank
FROM (
SELECT id,kind
FROM refer_param
WHERE CODE='ADMIN_AREA') t,
(
SELECT @rownum := 0) r
ORDER BY kind) r ON r.id = p.id SET p.k = r.rank
WHERE p.CODE='ADMIN_AREA'

UPDATE AREA o
LEFT JOIN AREA m
ON o.pid = m.id
SET o.pid = CONCAT(m.pid, '/', m.id)
WHERE o.pid IS NOT NULL AND o.typ=3;

14. where与having的区别
where针对表中的列发挥作用，查询数据；having针对查询结果中的列发挥作用，筛选数据。

15. 启用/禁用外键约束
#查看FOREIGN_KEY_CHECKS的值
SELECT  @@FOREIGN_KEY_CHECKS;

#禁用外键约束
SET FOREIGN_KEY_CHECKS=0;

#启用外键约束
SET FOREIGN_KEY_CHECKS=1;

16. 全局日志设置
#查看全局日志设置
show global variables like '%general%';
#开启全局日志
set global general_log = on;
#关闭全局日志
set global general_log = off;
#查看日志
SELECT * from mysql.general_log ORDER BY event_time DESC;
#清空日志表
truncate table mysql.general_log;

17. 设置数据库的远程访问
mysql> grant all on guitar.* to 'root'@'%' IDENTIFIED BY 'guitar2016' WITH GRANT OPTION;
mysql> flush privileges;

18. 修改数据库字符集
以Mysql 5.7版Linux上配置为例，修改my.cnf文件，
在[mysqld]下加上下面这行后重启服务:
character-set-server=utf8

19. 生成json字符串
SELECT CONCAT('select group_concat(concat( ''{'',',
GROUP_CONCAT(CONCAT('''"'',''',column_name,''',''"'','':'',''"'',',column_name,',''"'','','''))
,'''}'') separator '',\n'') from auth_realm')
FROM information_schema.COLUMNS WHERE table_schema ='train' and table_name ='auth_realm';

20. Mysql解释执行
explain select * From e_file where id = 4;

21. 修改Mysql允许接收的最大包容量
set global max_allowed_packet = 2*1024*1024*10;

22. Mysql命令行导入导出
#SQL文件导入
> mysql -u user_name  -p
mysql>  use db_name;
mysql>  source /home/db.sql;
#导出一个库
> mysqldump -u user_name -p db_name > /home/db.sql
#导出一张表
> mysqldump -u user_name -p db_name table_name> /home/db.sql
#导出一个库的结构
> mysqldump -u user_name -p -d --add-drop-table db_name > /home/db.sql

22. 更新数据库、表与字段的字符集
[mysqld]
# 服务端默认字符集
character-set-server=utf8mb4
# 连接层默认字符集
collation-server=utf8mb4_unicode_ci
[client]
# 客户端来源数据的默认字符集
default-character-set = utf8mb4
ALTER TABLE we_user convert to CHARACTER SET utf8mb4 collate utf8mb4_bin;
ALTER TABLE we_user MODIFY COLUMN nickname varchar(160) CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;

23. 查看、设置最大连接数
# 查看最大连接数
$ show variables like '%max_connections%';
# 通过命令设置最大连接数
$ set GLOBAL max_connections = 200;
# 通过配置设置最大连接数
找到配置文件my.ini或my.cnf添加或修改:max_connections=1000

24.查看当前运行的进程并杀掉阻塞中的进程
select*from information_schema.processlist where user='root';
show processlist;
select concat('kill ', id, ';') from information_schema.processlist where user='root';
