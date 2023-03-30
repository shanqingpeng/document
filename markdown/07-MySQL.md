### 一、用户与权限管理

#### 1、用户管理

##### （1）查看用户

```sql
# 1、查看所有用户
SELECT * FROM mysql.`user`;

# 2、根据用户名和主机地址查找用户
SELECT * FROM mysql.`user` WHERE USER = 'shanqingpeng' AND HOST = '%';
```

##### （2）创建用户

```sql
# 1、格式 
CREATE USER '用户名'@'主机地址' IDENTIFIED BY '密码';

# 2、案例
CREATE USER 'shanqingpeng'@'%' IDENTIFIED BY '123456';
```

##### （3）修改用户

```sql
# 修改用户的允许登录地址为localhost
UPDATE USER SET HOST = 'localhost' WHERE `User` = 'shanqingpeng' AND `Host` = '%';

# 修改用户的允许登录地址为任意主机
UPDATE USER SET HOST = '%' WHERE `User` = 'shanqingpeng' AND `Host` = 'localhost';

# 刷新权限
FLUSH PRIVILEGES;
```

##### （4）删除用户

```sql
# 1、推荐使用这种方式
DROP USER 'shanqingpeng'@'%';

# 2、不推荐使用这种方式，有残留信息保存
DELETE FROM mysql.`user` WHERE `user` = 'shanqingpeng' AND `Host` = '%';

# 刷新权限
FLUSH PRIVILEGES;
```

##### （5）修改密码

```sql
# (1) 修改当前用户密码, 方式1
ALTER USER USER() IDENTIFIED BY '123456';

# (2) 修改当前用户密码, 方式2
SET PASSWORD = '456123';

# (3) 修改其他用户密码, 方式1（需要当前用户有改密码的权限）
ALTER USER 'shanqingpeng'@'%' IDENTIFIED BY '456123';

# (4) 修改其他用户密码, 方式2（需要当前用户有改密码的权限）
SET PASSWORD FOR 'shanqingpeng'@'%'='abc123';
```



#### 2、权限管理

##### （1）查看权限

```sql
# 1、查看系统所有权限
SHOW PRIVILEGES;

# 2、查看当前用户的权限，方式1
SHOW GRANTS;

# 3、查看当前用户的权限，方式2
SHOW GRANTS FOR CURRENT_USER;

# 4、查看当前用户的权限，方式3
SHOW GRANTS FOR CURRENT_USER();

# 5、查看指定用户的权限
SHOW GRANTS FOR 'shanqingpeng'@'%';
```

##### （2）赋予权限

```sql
# 1、格式
GRANT 权限名1, 权限名2, ... ON 数据库名.表名 TO 用户名@主机地址;

# 2、案例1: 赋予指定权限
GRANT INSERT, DELETE, UPDATE, SELECT ON mysql_test.* TO 'shanqingpeng'@'%';

# 3、案例2: 赋予所有权限 (不包括grant权限)
GRANT ALL PRIVILEGES ON *.* TO 'shanqingpeng'@'%';

# 4、案例3: 赋予所有权限 (包括grant权限)
GRANT ALL PRIVILEGES ON *.* TO 'shanqingpeng'@'%' WITH GRANT OPTION;

# 刷新权限
FLUSH PRIVILEGES;
```

##### （3）收回权限

```sql
# 1、格式
REVOKE 权限名1, 权限名2, ... ON 数据库名.表名 FROM 用户名@主机地址;

# 2、案例1: 收回指定权限
REVOKE SELECT ON *.* FROM 'shanqingpeng'@'%';

# 3、案例2: 收回指定权限
REVOKE SELECT, INSERT, UPDATE, DELETE ON mysql_test.* FROM 'shanqingpeng'@'%';

# 4、案例3: 收回所有权限
REVOKE ALL PRIVILEGES ON *.* FROM 'shanqingpeng'@'%';

# 刷新权限
FLUSH PRIVILEGES;
```



### 二、角色管理（```MySQL8.0```新特性）

#### 1、创建角色

```sql
# 1、格式
CREATE ROLE 角色名@主机地址;

# 2、案例1: 新创建的角色, 默认没有激活
CREATE ROLE 'manager'@'%';

# 3、案例2: 新创建的角色, 默认没有激活
CREATE ROLE 'developer'@'%';
```

#### 2、给角色赋予权限

```sql
# 1、格式
GRANT 权限名1, 权限名2, ... ON 数据库名.表名 TO 角色名@主机地址;

# 2、案例1
GRANT SELECT ON mysql_test.* TO 'manager'@'%';

# 3、案例2
GRANT SELECT ON mysql_test.* TO 'developer'@'%';
```

#### 3、查看角色的权限

```sql
# 1、格式
SHOW GRANT FOR 角色名@主机地址;

# 2、案例1
SHOW GRANTS FOR 'manager'@'%';

# 3、案例2
SHOW GRANTS FOR 'developer'@'%';
```

#### 4、回收角色的权限

```sql
# 1、格式
REVOKE 权限名1, 权限名2, ... ON 数据库名.表名 FROM 角色名@主机地址;

# 2、案例
REVOKE SELECT ON mysql_test.* FROM 'manager'@'%';
```

#### 5、给用户赋予角色

```sql
# 1、格式
GRANT 角色名@主机地址 TO 用户名@主机地址;

# 2、案例
GRANT 'developer'@'%' TO 'shanqingpeng'@'%';
```

#### 6、激活角色

```sql
# 1、查看当前角色是否激活, NONE表示没有激活
SELECT current_role();

# 2、激活角色, 方式1
SET DEFAULT ROLE 'developer'@'%' TO 'shanqingpeng'@'%';

# 2、激活角色, 方式2
SHOW VARIABLES LIKE 'activate_all_roles_on_login';

SET GLOBAL activate_all_roles_on_login = ON;
```

#### 7、撤销用户的角色

```sql
# 1、格式
REVOKE 角色名@主机地址 FROM 用户名@主机地址;

# 2、案例
REVOKE 'developer'@'%' FROM 'shanqingpeng'@'%';
```

#### 8、设置强制角色

```sql
# 即：给每个创建的用户赋予一个默认的角色，不需要手动设置。强制角色无法revoke或drop
# 1、服务启动前设置, 方式1
[mysqld]
mandatory_roles='role1@localhost, role2@%'

# 2、运行时设置, 方式2
# (1) 重启后仍然有效
SET PERSIST mandatory_roles = 'role1@localhost, role2@%';

# (2) 重启后失效
SET GLOBAL mandatory_roles = 'role1@localhost, role2@%';
```

#### 9、删除角色

```sql
# 1、格式
DROP ROLE 角色名@主机地址;

# 2、案例
DROP ROLE 'manager'@'%';
```



### 三、流程控制

#### 1、分支结构

##### （1）IF

###### 语法格式

```sql
IF 表达式1 THEN 操作1
[ELSEIF 表达式2 THEN 操作2]         # [] 表示可以省略
...
[ELSE 操作n]                       # [] 表示可以省略
END IF
```

- 案例1：输入员工 id，查询员工 last_name

```sql
# 创建存储过程
DROP PROCEDURE IF EXISTS test_if_1;
DELIMITER $$
CREATE PROCEDURE IF NOT EXISTS test_if_1(IN emp_id INT(11))
BEGIN
	DECLARE stu_name VARCHAR(15);
	SELECT last_name INTO stu_name FROM employees WHERE employee_id = emp_id;
	IF stu_name IS NULL 
		THEN SELECT 'last_name is null';
	ELSE
		SELECT CONCAT('last_name is ', stu_name);
	END IF;
END $$
DELIMITER ;

# 调用存储过程
CALL test_if_1(101);
```

- 案例2：输入员工 id，查询员工工资级别

```sql
DROP PROCEDURE IF EXISTS test_if_2;
DELIMITER $$
CREATE PROCEDURE IF NOT EXISTS test_if_2(IN emp_id INT(11))
BEGIN
	DECLARE emp_salary DOUBLE(8, 2);
	SELECT salary INTO emp_salary FROM employees WHERE employee_id = emp_id;
	IF emp_salary >= 30000
		THEN SELECT '高薪';
	ELSEIF emp_salary >= 20000
		THEN SELECT '中薪';
	ELSEIF emp_salary >= 10000
		THEN SELECT '低薪';
	ELSE
		SELECT '低保';
	END IF;
END $$
DELIMITER ;

CALL test_if_2(119);
```

- 案例3：输入员工 id，如果工资小于8000且入职满5年，就涨薪1000

```sql
DROP PROCEDURE IF EXISTS test_if_3;
DELIMITER $$
CREATE PROCEDURE IF NOT EXISTS test_if_3(IN emp_id INT(11))
BEGIN
	DECLARE emp_salary DOUBLE(8, 2);
	DECLARE hire_years INT(11);
	
	SELECT salary INTO emp_salary FROM employees WHERE employee_id = emp_id;
	SELECT TIMESTAMPDIFF(YEAR, hire_date, NOW()) INTO hire_years FROM employees WHERE employee_id = emp_id;
	
	IF emp_salary < 8000 AND hire_years >= 5
		THEN UPDATE employees SET salary = salary + 1000 WHERE employee_id = emp_id;
	ELSE
		SELECT 'salary not change';
	END IF;
END $$
DELIMITER ;

CALL test_if_3(100);
```

- 案例4：输入员工 id，如果工资低于9000，直接涨薪到9000，如果工资低于10000且奖金为空，则把奖金设为0.01，其他情况，涨薪100

```sql
DROP PROCEDURE IF EXISTS test_if_3;
DELIMITER $$
CREATE PROCEDURE IF NOT EXISTS test_if_3(IN emp_id INT(11))
BEGIN
	DECLARE emp_salary DOUBLE(8, 2);       # 声明变量: 工资
	DECLARE emp_commission DOUBLE(2, 2);   # 声明变量: 奖金
	
	SELECT salary INTO emp_salary FROM employees WHERE employee_id = emp_id;
	SELECT commission_pct INTO emp_commission FROM employees WHERE employee_id = emp_id;
	
	IF emp_salary < 9000
		THEN UPDATE employees SET salary = 9000 WHERE employee_id = emp_id;
	ELSEIF emp_salary < 10000 AND emp_commission IS NULL
		THEN UPDATE employees SET commission_pct = 0.01 WHERE employee_id = emp_id;
	ELSE
		UPDATE employees SET salary = salary + 100 WHERE employee_id = emp_id;
	END IF;
END $$
DELIMITER ;

CALL test_if_3(101);
```



##### （2）CASE

###### 	语法格式1

```sql
CASE 表达式
WHEN 值1 THEN 结果1或语句1          # (语句需要加分号)
WHEN 值2 THEN 结果2或语句2          # (语句需要加分号)
...
ELSE 结果N或语句N                   # (语句需要加分号)
END [CASE]                        # (放在 BEGIN END 中需要加 CASE, 放在 SELECT 中不需要加 CASE)
```

- 案例：输入员工id，入职年限为1-5年，对应涨薪100-500元，超过5年，统一涨薪1000元

```sql
DROP PROCEDURE IF EXISTS test_case_1;
DELIMITER $$
CREATE PROCEDURE IF NOT EXISTS test_case_1(IN emp_id INT(11))
BEGIN
	DECLARE emp_hire_year INT(11) DEFAULT 0;
	SELECT TIMESTAMPDIFF(YEAR, hire_date, NOW()) INTO emp_hire_year FROM employees WHERE employee_id = emp_id;
	CASE emp_hire_year
	WHEN 0 THEN SELECT 'employee salary not change';
	WHEN 1 THEN UPDATE employees SET salary  = salary + 100 WHERE employee_id = emp_id;
	WHEN 2 THEN UPDATE employees SET salary  = salary + 200 WHERE employee_id = emp_id;
	WHEN 3 THEN UPDATE employees SET salary  = salary + 300 WHERE employee_id = emp_id;
	WHEN 4 THEN UPDATE employees SET salary  = salary + 400 WHERE employee_id = emp_id;
	WHEN 5 THEN UPDATE employees SET salary  = salary + 500 WHERE employee_id = emp_id;
	ELSE UPDATE employees SET salary  = salary + 1000 WHERE employee_id = emp_id;
	END CASE;
END $$
DELIMITER ;

CALL test_case_1(101);
```



###### 语法格式2

  ```sql
  CASE
  WHEN 条件1 THEN 结果1或语句1                 # (语句需要加分号)
  WHEN 条件2 THEN 结果2或语句2                 # (语句需要加分号)
  ...
  ELSE 结果N或语句N                           # (语句需要加分号)
  END [CASE]                                 # (放在 BEGIN END 中需要加 CASE, 放在 SELECT 中不需要加 CASE)
  ```

- 案例1：输入员工id，查看员工信息

```sql
DROP PROCEDURE IF EXISTS test_case_2;
DELIMITER $$
CREATE PROCEDURE IF NOT EXISTS test_case_2(IN emp_id INT(11))
BEGIN
	DECLARE result_count INT(11);
	SELECT COUNT(*) INTO result_count FROM employees WHERE employee_id = emp_id;
	CASE
		WHEN result_count > 0 
			THEN SELECT * FROM employees WHERE employee_id = emp_id;
		ELSE SELECT 'employee not exist';
	END CASE;
END $$
DELIMITER ;

CALL test_case_2(103);
```

- 案例2：输入员工id，如果工资小于10000且入职满5年，则涨薪500

```sql
DROP PROCEDURE IF EXISTS test_case_3;
DELIMITER $$
CREATE PROCEDURE IF NOT EXISTS test_case_3(IN emp_id INT(11))
BEGIN
	DECLARE emp_salary DOUBLE(8, 2);
	DECLARE emp_hire_years INT(11);
	
	SELECT salary INTO emp_salary FROM employees WHERE employee_id = emp_id;
	SELECT TIMESTAMPDIFF(YEAR,hire_date,NOW()) INTO emp_hire_years FROM employees WHERE employee_id = emp_id;
	
	CASE
		WHEN emp_salary < 10000 AND emp_hire_years >= 5
			THEN UPDATE employees SET salary = salary + 500 WHERE employee_id = emp_id;
		ELSE SELECT 'employee salary not change';
	END CASE;
END $$
DELIMITER ;

CALL test_case_3(107);
```

- 案例3：输入员工id，如果员工入职年限超过30则改为10年，超过20则改为5年，超过10年则改为3年

```sql
DROP PROCEDURE IF EXISTS test_case_4;
DELIMITER $$
CREATE PROCEDURE IF NOT EXISTS test_case_4(IN emp_id INT(11))
BEGIN
	DECLARE emp_hire_years INT(11) DEFAULT 0;
	SELECT TIMESTAMPDIFF(YEAR, hire_date, NOW()) INTO emp_hire_years FROM employees WHERE employee_id = emp_id;
	CASE
		WHEN emp_hire_years > 30
			THEN UPDATE employees SET hire_date = DATE_SUB(NOW(), INTERVAL 10 YEAR) WHERE employee_id = emp_id;
		WHEN emp_hire_years > 20
			THEN UPDATE employees SET hire_date = DATE_SUB(NOW(), INTERVAL 5 YEAR) WHERE employee_id = emp_id;
		WHEN emp_hire_years > 10
			THEN UPDATE employees SET hire_date = DATE_SUB(NOW(), INTERVAL 3 YEAR) WHERE employee_id = emp_id;
		ELSE
			SELECT 'employee hire_date not change';
	END CASE;
END $$
DELIMITER ;
```



#### 2、循环结构

##### （1）LOOP

###### 语法格式

```sql
[loop_label:] LOOP         # loop_label为当前LOOP的名字, 可以自定义, 可以省略
# 循环体
END LOOP [loop_label];
```

- 案例1：输入员工id，输入数字n，打印n次员工的姓名

```sql
DROP PROCEDURE IF EXISTS test_loop_1;
DELIMITER $$
CREATE PROCEDURE IF NOT EXISTS test_loop_1(IN emp_id INT(11), IN num INT(11))
BEGIN
	DECLARE emp_name VARCHAR(100);
	SELECT last_name INTO emp_name FROM employees WHERE employee_id = emp_id;
	
	test_loop: LOOP
		IF num <= 0 OR emp_name IS NULL 
			THEN LEAVE test_loop;
		END IF;
		SELECT emp_name;
		SET num = num - 1;
	END LOOP test_loop;
END $$
DELIMITER ;

CALL test_loop_1(101, 5);
```

- 案例2：给所有员工涨薪每次涨薪10%，直到平均工资大于等于12000元，输出总共涨薪次数

```sql
DROP PROCEDURE IF EXISTS test_loop_2;
DELIMITER $$
CREATE PROCEDURE IF NOT EXISTS test_loop_2(OUT loop_times INT(11))
BEGIN
	DECLARE avg_salary DOUBLE(8, 2);
	SET loop_times = 0;
	add_salary: LOOP
		SELECT AVG(salary) INTO avg_salary FROM employees;
		# 使用LEAVE退出LOOP循环
		IF avg_salary >= 12000 THEN LEAVE add_salary;
		END IF;
		UPDATE employees SET salary = (salary + salary * 0.1);
		SET loop_times = loop_times + 1;
	END LOOP add_salary;
END $$
DELIMITER ;

CALL test_loop_2(@loop_times);

SELECT @loop_times;
```

- 案例3：给所有员工每次降薪10%，直到平均工资小于等于5000元，输出总共降薪次数

```sql
DROP PROCEDURE IF EXISTS test_loop_3;
DELIMITER $$
CREATE PROCEDURE IF NOT EXISTS test_loop_3(OUT minus_times INT(11))
BEGIN
	DECLARE emp_avg_salary DOUBLE(8, 2) DEFAULT 0.0;
	SET minus_times = 0;
	
	minus_loop: LOOP
		SELECT AVG(salary) INTO emp_avg_salary FROM employees;
		IF emp_avg_salary <= 5000
			THEN LEAVE minus_loop;
		END IF;
		UPDATE employees SET salary = salary * 0.9;
		SET minus_times = minus_times + 1;
	END LOOP minus_loop;
END $$
DELIMITER ;

CALL test_loop_3(@minus_times);

SELECT @minus_times;
```

- 案例4：输入操作数字operator，输入数字count，随机给N名员工涨薪或降薪，幅度范围：500 - 3000，如果工资低于5000则不降薪，工资高于30000则不涨薪，结束后，打印涨薪员工的id、姓名、操作、幅度

```sql
DROP PROCEDURE IF EXISTS test_loop_4;
DELIMITER $$
# operator：0-降薪, 1-涨薪
CREATE PROCEDURE IF NOT EXISTS test_loop_4(IN operator INT(1), IN count INT(11))
BEGIN
	DECLARE amount INT(11) DEFAULT 0;
	DECLARE emp_id INT(11);
	DECLARE emp_name VARCHAR(100);
	DECLARE max_emp_id INT(11);
	DECLARE min_emp_id INT(11);
	DECLARE emp_salary DOUBLE(8, 0) DEFAULT 0.0;
	
	rand_loop: LOOP
		IF count <= 0
			THEN LEAVE rand_loop;
		END IF;
		
		SET count = count - 1;
		SELECT FLOOR(500 + RAND() * 2501) INTO amount;     # 随机金额: 500 - 3000
		SELECT MAX(employee_id) INTO max_emp_id FROM employees;
		SELECT MIN(employee_id) INTO min_emp_id FROM employees;
		# 随机获取一名员工的工号
		SELECT employee_id INTO emp_id FROM employees 
			WHERE employee_id >= FLOOR((max_emp_id - min_emp_id + 1) * RAND() + min_emp_id) LIMIT 1;
		# 获取员工工资
		SELECT salary INTO emp_salary FROM employees WHERE employee_id = emp_id;
		# 获取员工姓名
		SELECT last_name INTO emp_name FROM employees WHERE employee_id = emp_id;
		
		IF operator = 0 AND emp_salary < 5000
			THEN SET amount = 0.0;
		ELSEIF operator = 1 AND emp_salary > 30000
			THEN SET amount = 0.0;
		ELSEIF operator != 0 AND operator != 1
			THEN SET amount = 0.0;
		ELSE
			SELECT emp_id AS 工号, emp_name AS 姓名, CASE operator WHEN 0 THEN '降薪' WHEN 1 THEN '涨薪' ELSE '无' END AS 操作, amount AS 金额;
		END IF;
		
		IF operator = 0 AND emp_salary < 5000
			THEN ITERATE rand_loop;
		ELSEIF operator = 1 AND emp_salary > 30000
			THEN ITERATE rand_loop;
		END IF;
		
		CASE operator
			WHEN 0 THEN UPDATE employees SET salary = salary - amount WHERE employee_id = emp_id;
			WHEN 1 THEN UPDATE employees SET salary = salary + amount WHERE employee_id = emp_id;
			ELSE SELECT 'operator error';
		END CASE;
	END LOOP rand_loop;
END $$
DELIMITER ;

CALL test_loop_4(1, 1000);
```



##### （2）WHILE

###### 语法格式

```sql
[while_label:] WHILE 循环条件 DO             # [while_label:]是WHILE循环结构的名称, 可以省略
	循环体
END WHILE [while_label];
```

- 案例1：输入工号```emp_id```、打印次数```num```，循环打印```num```次工号

```sql
DROP PROCEDURE IF EXISTS test_while_1;
DELIMITER $$
CREATE PROCEDURE IF NOT EXISTS test_while_1(IN emp_id INT(11), IN num INT(11))
BEGIN
	IF num <= 0
		THEN SELECT 'num 必须大于 0' AS message;
	END IF;
	
	while_label: WHILE num > 0 DO
		SELECT emp_id AS 工号;
		SET num = num - 1;
	END WHILE while_label;
END $$
DELIMITER ;

CALL test_while_1(102, 10);
```

- 案例2：持续涨薪10%，直到平均工资达到指定金额, 工资```2W```以上的员工不涨薪, 输出涨薪次数

```sql
DROP PROCEDURE IF EXISTS test_while_2;
DELIMITER $$
CREATE PROCEDURE IF NOT EXISTS test_while_2(IN amount INT(11), OUT num INT(11))
BEGIN
	DECLARE emp_avg_salary DOUBLE(8, 2) DEFAULT 0.0;
	
	SET num = 0;
	
	SELECT AVG(salary) INTO emp_avg_salary FROM employees;
	
	add_salary: WHILE emp_avg_salary < amount DO
		UPDATE employees SET salary = salary * 1.1 WHERE salary < 20000;
		SELECT AVG(salary) INTO emp_avg_salary FROM employees;
		SET num = num + 1;
	END WHILE add_salary;
END $$
DELIMITER ;

CALL test_while_2(15000, @add_salary_num);

SELECT @add_salary_num AS 涨薪次数;

SELECT AVG(salary) FROM employees;
```

- 案例3：持续降薪10%，直到平均工资达到指定金额, 工资6000以下的员工不降薪, 输出降薪次数

```sql
DROP PROCEDURE IF EXISTS test_while_3;
DELIMITER $$
CREATE PROCEDURE IF NOT EXISTS test_while_3(IN amount INT(11), OUT num INT(11))
BEGIN
	DECLARE emp_avg_salary DOUBLE(8, 2) DEFAULT 0.0;
	
	SET num = 0;
	
	SELECT AVG(salary) INTO emp_avg_salary FROM employees;
	
	down_salary: WHILE emp_avg_salary > amount DO
		UPDATE employees SET salary = salary * 0.9 WHERE salary > 6000;
		SELECT AVG(salary) INTO emp_avg_salary FROM employees;
		SET num = num + 1;
	END WHILE down_salary;
END $$
DELIMITER ;

CALL test_while_3(7000, @down_salary_num);

SELECT @down_salary_num AS 降薪次数;

SELECT AVG(salary) FROM employees;
```



##### （3）REPEAT

###### 语法格式

```sql
[repeat_label:] REPEAT
	循环体
UNTIL 循环结束的条件表达式
END REPEAT [repeat_label]
```

- 案例1：输入工号```emp_id```、打印次数```num```，循环打印```num```次姓名

```sql
DROP PROCEDURE IF EXISTS test_repeat_1;
DELIMITER $$
CREATE PROCEDURE IF NOT EXISTS test_repeat_1(IN emp_id INT(11), IN num INT(11))
BEGIN
	DECLARE emp_name VARCHAR(25);
	
	SELECT last_name INTO emp_name FROM employees WHERE employee_id = emp_id;
	
	repeat_label: REPEAT
		SELECT emp_name AS 姓名;
		SET num = num - 1;
	UNTIL num <= 0
	END REPEAT repeat_label;
END $$
DELIMITER ;

CALL test_repeat_1(100, 3);
```

- 案例2：输入目标金额，涨薪比例```rate```，持续涨薪直到目标金额，工资```2W```以上的员工不涨薪，输出涨薪次数

```sql
DROP PROCEDURE IF EXISTS test_repeat_2;
DELIMITER $$
CREATE PROCEDURE IF NOT EXISTS test_repeat_2(IN amount INT(11), IN rate DOUBLE(8, 2), OUT num INT(11))
BEGIN
	DECLARE emp_avg_salary DOUBLE(8, 2) DEFAULT 0.0;
	
	SET num = 0;
	
	SELECT AVG(salary) INTO emp_avg_salary FROM employees;
	
	add_salary: REPEAT
		IF emp_avg_salary >= amount
			THEN LEAVE add_salary;
		END IF;
		
		UPDATE employees SET salary = salary + salary * rate WHERE salary < 20000;
		SELECT AVG(salary) INTO emp_avg_salary FROM employees;
		SET num = num + 1;
	UNTIL emp_avg_salary >= amount
	END REPEAT add_salary;	
END $$
DELIMITER ;

CALL test_repeat_2(12000, 0.15, @repeat_add_salary_num);

SELECT @repeat_add_salary_num AS 涨薪次数;

SELECT AVG(salary) FROM employees;
```

- 案例3：输入目标金额```amount```，降薪比例```rate```，持续降薪直到目标金额，工资```5K```以下的员工不降薪，输出降薪次数

```sql
DROP PROCEDURE IF EXISTS test_repeat_3;
DELIMITER $$
CREATE PROCEDURE IF NOT EXISTS test_repeat_3(IN amount INT(11), IN rate DOUBLE(8, 2), OUT num INT(11))
BEGIN
	DECLARE emp_avg_salary DOUBLE(8, 2) DEFAULT 0.0;
	
	SET num = 0;
	
	SELECT AVG(salary) INTO emp_avg_salary FROM employees;
	
	down_salary: REPEAT
		IF emp_avg_salary <= amount
			THEN LEAVE down_salary;
		END IF;
		
		UPDATE employees SET salary = salary - salary * rate WHERE salary > 5000;
		SELECT AVG(salary) INTO emp_avg_salary FROM employees;
		SET num = num + 1;
	UNTIL emp_avg_salary <= amount
	END REPEAT down_salary;	
END $$
DELIMITER ;

CALL test_repeat_3(6000, 0.2, @repeat_down_salary_num);

SELECT @repeat_down_salary_num AS 降薪次数;

SELECT AVG(salary) FROM employees;
```

##### （4）总结

- LOOP：一般用于实现简单的 "死" 循环。

- WHILE：先判断条件，满足条件才执行循环体。

- REPEAT：先执行循环体，再判断（满足条件就结束循环）。

  

#### 3、跳转语句

##### （1）LEAVE

###### 语法格式

```sql
LEAVE 标记名;           # 用于跳出循环结构或```BEGIN...END```结构
```

- 案例：输入数字```end_num```， 打印从1到```end_num```，如果```end_num```>10，则最多只打印1到10

```sql
DROP PROCEDURE IF EXISTS test_leave;
DELIMITER $$
CREATE PROCEDURE IF NOT EXISTS test_leave(IN end_num INT(11))
begin_label: BEGIN
	DECLARE begin_num INT(11) DEFAULT 1;
	while_label: WHILE end_num >= begin_num DO
		SELECT begin_num;
		SET begin_num = begin_num + 1;
		IF(begin_num > 10) 
			THEN LEAVE begin_label;
		END IF;
	END WHILE while_label;
END $$
DELIMITER ;

CALL test_leave(100);
```

##### （2）ITERATE

###### 语法格式

```sql
ITERATE 标记名;           # 只能在循环结构中使用，表示跳过本次循环，继续执行下一次循环
```

- 案例：输入数字```num```，循环执行```num = num + 1```，如果```num < 10```，则继续执行循环，如果```num > 15```，则跳出循环

```sql
DROP PROCEDURE IF EXISTS test_iterate;
DELIMITER $$
CREATE PROCEDURE IF NOT EXISTS test_iterate(IN num INT(11))
BEGIN
	loop_label: LOOP
		SET num = num + 1;
		IF num < 10
			THEN ITERATE loop_label;
		ELSEIF num > 15
			THEN LEAVE loop_label;
		END IF;
		SELECT 'test iterate' AS message;
	END LOOP loop_label;
END $$
DELIMITER ;

CALL test_iterate(1);
```





### 四、游标和触发器

#### 1、游标

##### （1）游标是什么

- 游标可以对结果集中每一条记录进行定位，并对记录中的数据进行操作。
- 游标让```SQL```这种面向集合的语言有了面向过程开发的能力。
- ```MySQL```中游标可以在```存储过程```和```函数```中使用。
- 在使用游标的过程中，会对数据进行加锁，这样会影响并发量比较大的业务，还会消耗系统资源，造成内存不足。

##### （2）游标的使用

- 声明游标

  ```sql
  DECLARE 游标名 CURSOR FOR 查询语句;             # MySQL、SQL Server、DB2、MariaDB语法
  DECLARE 游标名 CURSOR IS 查询语句;              # Oracle、PostgreSQL语法
  ```

- 打开游标

  ```sql
  OPEN 游标名;                          # 打开游标时，查询语句的结果集就会送到游标工作区，为游标逐条读取结果集中的记录做准备
  ```

- 使用游标（从游标中获取数据）

  ```sql
  # 使用游标来读取当前行，并将这一行的数据保存到变量中，游标指针指向下一行。
  # 注意：
  # (1) INTO 后面的变量必须提前声明好 
  # (2) INTO 后面变量的数量和顺序必须与查询语句的结果集一致, 否则在执行存储过程或函数时会报错
  FETCH 游标名 INTO 变量1, [变量2, 变量3]... 
  ```

- 关闭游标

  ```sql
  CLOSE 游标名;     # 游标会占用系统资源，如果不及时关闭，游标会一直保持到存储过程结束，影响系统运行效率。
  ```

- 案例：累加薪资最高的员工工资，直到达到指定金额，最后输出累加员工的数量。

  ```sql
  DROP PROCEDURE IF EXISTS test_cursor_1;
  DELIMITER $$
  CREATE PROCEDURE IF NOT EXISTS test_cursor_1(IN amount DOUBLE(8, 2), OUT emp_count INT(11))
  BEGIN
  	DECLARE emp_salary DOUBLE(8, 2) DEFAULT 0.0;
  	DECLARE total_salary DOUBLE(8, 2) DEFAULT 0.0;
  	# 声明游标
  	DECLARE add_salary_cursor CURSOR FOR SELECT salary FROM employees ORDER BY salary DESC;
  	
  	SET emp_count = 0;
  	# 打开游标
  	OPEN add_salary_cursor;
  	
  	add_salary: WHILE total_salary < amount DO
  		# 从游标中读取数据
  		FETCH add_salary_cursor INTO emp_salary;
  		SET total_salary = total_salary + emp_salary;
  		SET emp_count = emp_count + 1;
  	END WHILE add_salary;
  	
  	# 关闭游标
  	CLOSE add_salary_cursor;
  END $$
  DELIMITER ;
  
  CALL test_cursor_1(100000, @cursor_emp_count);
  
  SELECT @cursor_emp_count AS 员工数量;
  ```

  

#### 2、触发器

##### （1）触发器是什么

- 触发器是由事件来触发某个操作，这些事件包括```INSERT、UPDATE、DELETE```事件。
- 只有表支持触发器，视图和临时表不支持触发器。

##### （2）创建触发器

- 语法结构

  ```sql
  # 1、触发器名: 建议数据库唯一
  # 2、BEFORE | AFTER: 表示触发器执行的时间
  # 3、INSERT | UPDATE | DELETE: 表示触发的事件 (即当出现什么事件时, 才执行触发器)
  # 4、表名: 触发器作用的表
  # 5、触发器执行的语句块: 可以是单条SQL语句, 也可以是由BEGIN...END组成的复合语句块.
  CREATE TRIGGER 触发器名
  {BEFORE | AFTER} {INSERT | UPDATE | DELETE} ON 表名
  FOR EACH ROW
  触发器执行的语句块;
  ```

- 案例1：```employees```表插入数据时，插入一条数据到```employees_log```表

  ```sql
  DROP TRIGGER IF EXISTS employees_trigger_1;
  CREATE TRIGGER IF NOT EXISTS employees_trigger_1
  BEFORE INSERT ON employees
  FOR EACH ROW 
  INSERT INTO employees_log(message, log_time) VALUES('新增了一个员工', NOW());
  
  INSERT INTO employees(employee_id, first_name, last_name, email, phone_number, hire_date, job_id, salary, commission_pct, manager_id, department_id)
  VALUES(207, 'Takaki', 'Shan', 'takaki@outlook.com', '13800138000', DATE_SUB(NOW(), INTERVAL 3 YEAR), 'PR_REP', 20000.00, 0.15, 100, 90);
  
  SELECT * FROM employees;
  
  SELECT * FROM employees_log;
  ```

- 案例2：```employees```表插入数据之前，如果员工的工资高于领导的工资，则报错，不允许插入

  ```sql
  DROP TRIGGER IF EXISTS employees_trigger_2;
  DELIMITER $$
  CREATE TRIGGER IF NOT EXISTS employees_trigger_2
  BEFORE INSERT ON employees
  FOR EACH ROW
  BEGIN
  	DECLARE manager_salary DOUBLE(8, 2);
  	SELECT salary INTO manager_salary FROM employees WHERE employee_id = NEW.manager_id;
  	
  	IF NEW.salary > manager_salary
  		THEN SIGNAL SQLSTATE 'HY000' SET MESSAGE_TEXT = '员工工资不能高于领导的工资';
  	END IF;
  END $$
  DELIMITER ;
  
  INSERT INTO employees(employee_id, first_name, last_name, email, phone_number, hire_date, job_id, salary, commission_pct, manager_id, department_id)
  VALUES(208, 'Takaki', 'Lee', 'takakiLee@outlook.com', '13900139000', DATE_SUB(NOW(), INTERVAL 5 YEAR), 'PR_REP', 20000.00, 0.15, 100, 90);
  
  SELECT * FROM employees;
  ```


##### （3）查看和删除触发器

- 查看触发器

  ```sql
  SHOW TRIGGERS;                                     # 查看当前数据库所有触发器
  
  SHOW CREATE TRIGGER test_trigger_1;                # 查看触发器的创建语句
  
  SELECT * FROM information_schema.`TRIGGERS`;       # 从information_schema中查看
  ```

- 删除触发器

  ```sql
  DROP TRIGGER IF EXISTS 触发器名;
  ```


##### （4）触发器的优缺点

- 优点：

  - 可以确保数据的完整性

  - 可以帮助我们记录操作日志

  - 可以在操作数据前，进行数据合法性校验

- 缺点：
  - 最大的缺点就是可读性差
  - 相关数据的变更，可能会导致触发器出错



### 五、变量

#### 1、简介

- 在```MySQL```数据库中，变量分为```系统变量```和```用户变量```。
- 在存储过程和函数中，可以使用变量来存储或计算中间结果数据，或者输出最终结果。

#### 2、系统变量

##### （1）简介

- 由系统定义，不是用户定义，属于服务器层面。
- 启动```MySQL```服务，生成```MySQL```实例期间，```MySQL```将为系统变量赋值，这些变量定义了当前```MySQL```实例的属性、特征。

##### （2）分类

- 全局系统变量：需要加```GLOBAL```关键字。
- 会话系统变量：需要加```SESSION```关键字，如果不写关键字，默认是会话系统变量。

##### （3）区别

- 全局系统变量针对所有会话都有效，但是不能夸重启，即修改全局系统变量之后，重启```MySQL```服务后失效。
- 会话系统变量仅针对当前会话有效。当前会话期间，修改会话系统变量，不会影响其他会话。

##### （4）注意

- 有些系统变量，只能是全局系统变量（GLOBAL），例如```max_connections``` 服务器最大连接数

  ```sql
  SHOW GLOBAL VARIABLES LIKE '%max_connections%';

- 有些系统变量，只能是会话系统变量（SESSION），例如```pseudo_thread_id``` 标记当前会话的```MySQL```连接ID

  ```sql
  SHOW SESSION VARIABLES LIKE '%pseudo_thread_id%';
  ```

- 有些系统变量，既是全局的，又是会话的，例如```character_set_client ``` 客户端字符集

  ```sql
  SHOW GLOBAL VARIABLES LIKE '%character_set_client%';
  
  SHOW SESSION VARIABLES LIKE '%character_set_client%';
  ```

##### （5）全局系统变量

```sql
# 1、查看所有全局系统变量
SHOW GLOBAL VARIABLES;

# 2、查看指定全局系统变量, 方式1
SHOW GLOBAL VARIABLES LIKE '%max_connections%';

# 3、查看指定全局系统变量, 方式2
SELECT @@global.max_connections;

# 4、修改全局系统变量, 方式1
SET GLOBAL max_connections = 171;

# 5、修改全局系统变量, 方式2
SET @@global.max_connections = 181;
```

##### （6）会话系统变量

```sql
# 1、查看所有会话系统变量, 方式1
SHOW VARIABLES;

# 2、查看所有会话系统变量, 方式2
SHOW SESSION VARIABLES;

# 3、查看指定会话系统变量, 方式1
SHOW SESSION VARIABLES LIKE '%pseudo_thread_id%';

# 4、查看指定会话系统变量, 方式2
SELECT @@session.pseudo_thread_id;

# 5、修改系统变量, 方式1
SET @@session.pseudo_thread_id = 37;

# 6、修改系统变量, 方式2
SET SESSION pseudo_thread_id = 47;
```

#### 3、用户变量

##### （1）简介

- 用户变量是由用户自己定义的变量，作为```MySQL```编码的规范，用户变量需要以一个```@```开头。

##### （2）分类

- 会话用户变量：作用域和会话变量一样，只对当前会话有效。
- 局部变量：只在```BEGIN...END```语句块中有效，只能在存储过程和函数中使用。

##### （3）会话用户变量

```sql
# 1、定义会话用户变量, 格式1
SET @变量名 = 值;
SET @变量名 := 值;

# 2、案例1
SET @user_name_1 = 'sqp1';
SET @user_name_2 := 'sqp2';

# 3、定义会话用户变量, 格式2
SELECT @变量名 := 字段或结果 FROM employees;
SELECT 字段或结果 INTO @变量名 FROM employees;

# 4、案例2
SELECT @max_salary_1 := MAX(salary) FROM employees;
SELECT MAX(salary) INTO @max_salary_2 FROM employees;

# 5、查看用户变量, 格式
SELECT @变量名;

# 6、案例3
SELECT @user_name_1;
SELECT @user_name_2;
SELECT @max_salary_1;
SELECT @max_salary_2;
```

##### （4）局部变量

```sql
# 1、声明局部变量, 格式
DECLARE 变量名 类型 [DEFAULT 默认值];

# 2、案例1
DECLARE emp_name VARCHAR(50);

# 3、修改局部变量, 格式
SET 变量名 = 值;
SET 变量名 := 值;
SELECT 字段名 INTO 变量名 FROM 表名 [WHERE 条件];

# 4、案例2
SET emp_name = 'Tom1';
SET emp_name := 'Tom2';
SELECT last_name INTO emp_name FROM employees WHERE employee_id = 100;
```



### 六、存储过程

#### 1、存储过程简介

- 存储过程一种数据库对象，由一系列的预先编译好的```SQL```语句封装而成。
- 存储在服务器端，当需要执行存储过程的时候，客户端向服务器端发送调用请求，服务器端就会执行存储过程中的全部```SQL```

#### 2、创建存储过程

##### （1）格式

```sql
CREATE PROCEDURE [IF NOT EXISTS] 存储过程名(IN | OUT | INOUT 参数名 参数类型, ...)
[characteristics...]
BEGIN
 # 存储过程体: 可以有多条SQL语句, 如果仅有一条SQL语句, 则可以省略BEGIN和END
END ;

# 1、IN: 输入型参数, 存储过程体只是读取这个参数, 如果参数没有声明种类, 则默认是IN
# 2、OUT: 输出型参数, 存储过程体中可以修改这个参数的值, 存储过程执行完成之后, 客户端或应用程序可以读取这个参数的值
# 3、INOUT: 既是输入型参数, 又是输出型参数
# 4、参数类型: 可以是MySQL数据库中的任意类型
# 5、characteristics: 指定存储过程的特征, 其取值信息如下:
LANGUAGE SQL
| [NOT] DETERMINISTIC
| {CONTAINS SQL | NO SQL | READS SQL DATA | MODIFIES SQL DATA }
| SQL SECURITY {DEFINER | INVOKER}
| COMMENT '注释信息'

# (1) LANGUAGE SQL: 表示存储过程体是由SQL语句组成的, 当前系统支持的语言为SQL
# (2) [NOT] DETERMINISTIC: 表示存储过程执行的结果是否确定 (即相同输入是否得到相同结果)
# (3) {CONTAINS SQL | NO SQL | READS SQL DATA | MODIFIES SQL DATA }: 
#     表示子程序使用SQL语句的限制, 包含SQL | 不包含SQL | 包含读取数据的SQL | 包含修改数据的SQL
# (4) SQL SECURITY {DEFINER | INVOKER}: 指定哪些用户可以执行当前存储过程, DEFINER: 创建者, INVOKER: 有权限用户
# (5) COMMENT: 注释信息, 可以用于描述当前存储过程
```

##### （2）设置新的结束标记

```sql
# 1、原因: MySQL默认的语句结束符号为分号(;), 避免因为存储过程体中SQL语句的分号(;)导致创建存储过程出错。

# 2、格式
DELIMITER 新的结束标记

# 3、案例
DELIMITER $$
CREATE PROCEDURE IF NOT EXISTS test_proc_1()
BEGIN
	SELECT * FROM employees;
END $$
DELIMITER ;   # 存储过程创建完成后, 再恢复默认结束符
```

##### （3）存储过程体

```sql
# 1、存储过程体中可以有多条SQL语句, 也可以有复杂的SQL语句

# 2、存储过程体, 以BEGIN开始, END结束, 中间包含一条或多条语句, 每条语句都以分号结束(;)

# 3、使用DECALRE声明变量, 需要在使用之前声明
DECLARE emp_name VARCHAR(50);

# 4、使用SET为变量赋值
SET emp_name = 'Tom';

# 5、把从表中查询的结果, 赋值给变量
SELECT last_name INTO emp_name FROM employees WHERE employee_id = 100;
```

##### （4）案例

- 1、无参数：查询所有员工信息

```sql
DROP PROCEDURE IF EXISTS test_proc_1;

DELIMITER $$
CREATE PROCEDURE test_proc_1()
BEGIN
	SELECT * FROM employees;
END $$
DELIMITER ;
```

- 2、带OUT参数：查询员工最高工资是多少

```sql
DROP PROCEDURE IF EXISTS test_proc_2;

DELIMITER $$
CREATE PROCEDURE IF NOT EXISTS test_proc_2(OUT max_salary DOUBLE(8, 2))
BEGIN
	SELECT MAX(salary) INTO max_salary FROM employees;
END $$
DELIMITER ;
```

- 3、带IN、OUT参数：输入员工id，输出员工工资

```sql
DROP PROCEDURE IF EXISTS test_proc_3;

DELIMITER $$
CREATE PROCEDURE IF NOT EXISTS test_proc_3(IN emp_id INT, OUT emp_salary DOUBLE(8, 2))
BEGIN
	SELECT salary INTO emp_salary FROM employees WHERE employee_id = emp_id;
END $$
DELIMITER ;
```

- 4、带```INOUT```参数：输入员工姓名，输出员工领导姓名

```sql
DROP PROCEDURE IF EXISTS test_proc_4;

DELIMITER $$
CREATE PROCEDURE IF NOT EXISTS test_proc_4(INOUT emp_name VARCHAR(25))
BEGIN
	SELECT last_name INTO emp_name
	FROM employees WHERE employee_id = (SELECT manager_id FROM employees WHERE last_name = emp_name);
END $$
DELIMITER ;
```

#### 3、调用存储过程

```sql
# 1、格式
CALL 存储过程名(参数, ...);

# 2、案例1
CALL test_proc_1();

# 3、案例2
CALL test_proc_2(@max_salary);
SELECT @max_salary AS 最高工资;

# 4、案例3
CALL test_proc_3(100, @emp_salary);
SELECT @emp_salary AS 员工工资;

# 5、案例4
SET @emp_name = 'Pataballa';
CALL test_proc_4(@emp_name);
SELECT @emp_name AS 领导姓名;
```

#### 4、查看和修改存储过程

##### （1）查看

```sql
# 1、查看存储过程的创建信息, 格式
SHOW CREATE PROCEDURE 存储过程名;

# 2、案例
SHOW CREATE PROCEDURE test_proc_1;

# 3、查看所有数据库下的存储过程的状态信息
SHOW PROCEDURE STATUS;

# 4、查看指定存储过程的状态信息
SHOW PROCEDURE STATUS LIKE 'test_proc_1';

# 5、查看某个数据库下所有的存储过程状态信息
SHOW PROCEDURE STATUS WHERE db = 'mysql_test';

# 6、从information_schema.Routines中查看存储过程的信息, 格式
SELECT * FROM information_schema.Routines WHERE ROUTINE_NAME = 存储过程名 [AND ROUTINE_TYPE = 'PROCEDURE'];

# 7、从information_schema.Routines中查看存储过程的信息, 案例
SELECT * FROM information_schema.Routines WHERE ROUTINE_NAME = 'test_proc_1' AND ROUTINE_TYPE = 'PROCEDURE';
```

##### （2）修改

```sql
# 1、修改存储过程, 不影响存储过程的功能, 只是修改相关特性, 格式
ALTER PROCEDURE 存储过程名 [characteristics...];

# 2、修改存储过程, 案例
ALTER PROCEDURE test_proc_1
SQL SECURITY INVOKER                      # 存储过程调用权限修改为有权限者
COMMENT '查询所有员工信息';                  # 修改存储过程的注释信息

# 3、查看存储过程状态
SHOW PROCEDURE STATUS LIKE 'test_proc_1';

# 4、存储过程的删除, 格式
DROP PROCEDURE [IF EXISTS] 存储过程名;

# 5、存储过程的删除, 案例
DROP PROCEDURE IF EXISTS test_proc_1;
```

#### 5、存储过程的优缺点

##### （1）优点

- 提高```SQL```复用性，存储过程可以一次编译多次调用。
- 安全性强，可以设置用户对存储过程的调用权限。
- 减少网络传输。

##### （2）缺点

- 可移植性差，不能夸数据库移植，例如在```MySQL```、```Oracle```、```SQL Server```编写的存储过程，换成其他数据库时需要重新编写。
- 调试困难，只有少数DBMS支持存储过程的调试。对于复杂的存储过程来说，开发和维护都不容易。
- 版本控制很困难， 存储过程本身没有版本控制，迭代更新时比较麻烦。
- 不适合高并发场景，存储过程会增加数据库压力，而高并发场景需要减少数据库压力，有时候还会采用分库分表的方式。



### 七、函数

#### 1、函数简介

- ```MySQL```支持自定义函数，定义好之后，调用方式与```MySQL```系统函数一样。

#### 2、创建函数

##### （1）格式

```sql
CREATE FUNCTION [IF NOT EXISTS] 函数名(参数名 参数类型, ...)
RETURNS 返回值类型
[characteristics...]
BEGIN
	# 函数体, 必须要有RETURN语句
END ;

# 1、函数的参数种类总是默认为IN, 不能为参数指定IN、OUT、INOUT修饰符
# 2、RETURNS: 指明当前函数返回值的类型
# 3、[characteristics...]: 指明当前函数的一些特征, 与存储过程的特征一致
# 4、函数体中必须要有RETURN语句
# 5、设置新的结束标记: 与存储过程一致
# 6、注意: 如果创建函数出错, 开启设置: 允许创建函数
SHOW GLOBAL VARIABLES LIKE '%log_bin_trust_function_creators%';
SET GLOBAL log_bin_trust_function_creators = 1;
```

##### （2）函数体

```sql
# 1、如果函数体仅有一个语句, 则BEGIN和END可以省略

# 2、函数中可以使用DECLARE声明变量, 格式
DECLARE 变量名 变量类型 [DEFAULT 默认值];

# 3、使用DECLARE声明变量, 案例
DECLARE emp_sex INT(2) DEFAULT 0;

# 4、为变量赋值, 格式
SET 变量名 = 值;

# 5、为变量赋值, 案例
SET emp_sex = 1;

# 6、查询结果赋值给变量, 格式
SELECT 字段或结果 INTO 变量名 FROM 表名 [WHERE 条件];

# 7、查询结果赋值给变量, 案例
SELECT sex INTO emp_sex FROM employees WHERE employee_id = 100;

# 8、函数体中还可以使用流程控制语句, 如IF、CASE、LOOP、WHILE、REPEAT等

# 9、函数体中必须要有RETURN语句
```

##### （3）案例

- 1、输入一个数字```n```，返回长度为```n```的随机字符串

```sql
DROP FUNCTION IF EXISTS rand_string;

DELIMITER $$
CREATE FUNCTION IF NOT EXISTS rand_string(n INT)
	RETURNS VARCHAR(255)    # 该函数返回一个随机字符串
BEGIN
	DECLARE chars_str VARCHAR(100) DEFAULT 'abcdefghijklmopgrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ';
	DECLARE return_str VARCHAR(255) DEFAULT '';
	DECLARE i INT DEFAULT 0;
	WHILE i <= n DO
		SET return_str = CONCAT(return_str, SUBSTRING(chars_str, FLOOR(1 + RAND() * 52), 1));
		SET i = i + 1;
	END WHILE;
	RETURN return_str;
END $$
DELIMITER ;
```

- 2、输入一个开始数字```from_num```，一个结束数字```to_num```，随机返回一个```from_num```与```to_num```之间的数字

```sql
DROP FUNCTION IF EXISTS rand_num;

DELIMITER $$
CREATE FUNCTION IF NOT EXISTS rand_num(from_num INT, to_num INT)
	RETURNS INT(11)				# 该函数返回一个随机数字
BEGIN
	DECLARE i INT DEFAULT 0;
	SET i = FLOOR(from_num + RAND() * (to_num - from_num + 1));
	RETURN i;
END $$
DELIMITER ;
```

#### 3、调用函数

```sql
# 1、格式
SELECT 函数名(参数1, ...);

# 2、案例1
SELECT rand_string(10) AS 随机字符串;

# 3、案例2
SELECT rand_num(1, 100) AS 随机数;
```

#### 4、查看和修改函数

##### （1）查看

```sql
# 1、查看函数的创建信息, 格式
SHOW CREATE FUNCTION 函数名;

# 2、查看函数的创建信息, 案例
SHOW CREATE FUNCTION rand_string;

# 3、查看所有数据库中函数状态信息
SHOW FUNCTION STATUS;

# 4、查看指定数据库中函数状态信息
SHOW FUNCTION STATUS WHERE db = 'mysql_test';

# 5、查看指定函数的状态信息
SHOW FUNCTION STATUS LIKE 'rand_string';

# 6、从information_schema.Routines中查看函数信息, 格式
SELECT * FROM information_schema.Routines WHERE ROUTINE_NAME = 函数名 [AND ROUTINE_TYPE = 'FUNCTION'];

# 7、从information_schema.Routines中查看函数信息, 案例
SELECT * FROM information_schema.Routines WHERE ROUTINE_NAME = 'rand_string' AND ROUTINE_TYPE = 'FUNCTION';
```

##### （2）修改

```sql
# 1、只能修改函数的特征, 不影响函数的功能, 格式
ALTER FUNCTION 函数名 [characteristics...];

# 2、案例
ALTER FUNCTION rand_string
SQL SECURITY INVOKER
COMMENT '返回随机字符串';

# 3、查看函数状态信息
SHOW CREATE FUNCTION rand_string;

# 4、删除函数, 格式
DROP FUNCTION [IF EXISTS] 函数名;

# 5、删除函数, 案例
DROP FUNCTION IF EXISTS rand_string;
```

#### 5、存储过程和函数的区别

|          | 关键字          | 调用语法 | 返回值          | 应用场景               |
| -------- | --------------- | -------- | --------------- | ---------------------- |
| 存储过程 | ```PROCEDURE``` | CALL     | 理解为0个或多个 | 一般用于更新           |
| 函数     | ```FUNCTION```  | SELECT   | 只能是1个       | 一般用于查询结果并返回 |

- 函数可以放在查询语句中，而存储过程不行。

- 存储过程功能更加强大，可以执行对表的操作（如新建表、删除表等）和事务操作，而函数不具备这样的功能。

  

### 八、窗口函数（```MySQL``` 8.0新特性）

### 九、字符集与```SQL```规范

#### 1、字符集

##### （1）字符集和比较规则

```sql
# 1、查看MySQL支持的字符集
SHOW CHARSET;
SHOW CHARACTER SET;
# (1) Default collation: 默认比较规则
# (2) Maxlen: 最多用几个字节表示一个字符

# 2、utf8 与 utf8mb4 的区别
# (1) utf8: 即utf8mb3字符集, 使用1~3个字节表示一个字符
# (2) utf8mb4: 使用1~4个字节表示一个字符, MySQL8.0及之后版本的默认字符集.

# 3、查看MySQL支持的比较规则
SHOW COLLATION;

# 4、查看某个字符集对应的比较规则
SHOW COLLATION LIKE 'gbk%';
SHOW COLLATION LIKE 'utf8mb4%';

# 5、比较规则后缀的含义
# (1) _ai: accent insensitive    不区分重音
# (2) _as: accent sensitive      区分重音
# (3) _ci: case insensitive      不区分大小写
# (4) _cs: case sensitive        区分大小写
# (5) _bin: binary               以二进制方式比较
```

##### （2）查看和修改字符集

```sql
# 1、查看系统字符集
SHOW VARIABLES LIKE '%character%';

# 2、查看数据库字符集
SHOW CREATE DATABASE mysql_test;

# 3、查看数据表字符集
SHOW CREATE TABLE employees;

# 4、修改MySQL服务器字符集
# (1) MySQL8.0及之后的版本, 默认字符集为utf8mb4, 一般情况下不需要修改
# (2) MySQL5.7及之前的版本: 在my.cnf文件的 [mysqld] 下增加: character_set_server=utf8

# 5、修改数据库字符集, 格式
ALTER DATABASE 数据库名 CHARACTER SET 字符集名 [COLLATION 比较规则];

# 6、修改数据库字符集, 案例
ALTER DATABASE mysql_test CHARACTER SET 'utf8mb4' COLLATION 'utf8mb4_0900_ai_ci';

# 7、修改数据表的字符集, 格式
ALTER TABLE 表名 CONVERT TO CHARACTER SET 字符集名 [COLLATION 比较规则];

# 8、修改数据表的字符集, 案例
ALTER TABLE employees CONVERT TO CHARACTER SET 'utf8mb4' COLLATION 'utf8mb4_0900_ai_ci';
```

#### 2、```SQL```规范

##### （1）Windows和Linux大小写区别

- Windows环境默认情况下```SQL```语句不区分大小写的

  ```sql
  # 数据库名、表名、变量名、字段名、关键字、函数名: 都不区分大小写
  ```

- Linux环境默认```SQL```语句是区分大小写的

  ```sql
  # 1、数据库名、表名、变量名: 严格区分大小写
  
  # 2、字段名、关键字、函数名: 不区分大小写
  ```

- 通过查看系统参数，确定是否区分大小写

  ```sql
  SHOW VARIABLES LIKE '%lower_case_table_name%';
  # (1) 变量值为 0, 表示区分大小写
  
  # (2) 变量值为 1, 表示不区分大小写, 创建的表、数据库都是以小写形式保存在磁盘上, 对于SQL语句都是转化为小写对表和数据库进行查找
  
  # (3) 变量值为 2, 创建的表和数据依据语句上格式存放, 凡是查找都是转化为小写进行
  ```

##### （2）```SQL```编写建议

```sql
# 1、关键字、函数名全部大写

# 2、数据库名、表名、表别名、字段名、字段别名全部小写, SQL语句以分号结束

# 3、案例：
SELECT department_id AS 部门编号, AVG(salary) AS 平均工资 FROM mysql_test.employees GROUP BY department_id ORDER BY AVG(salary);
```

#### 3、```sql_mode```设置









