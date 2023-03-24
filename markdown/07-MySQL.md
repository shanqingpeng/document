### 一、用户与权限管理

#### 1、用户管理

#### 2、权限管理



### 二、角色管理

#### 1、创建角色

#### 2、给角色赋予权限

#### 3、查看角色的权限

#### 4、回收角色的权限

#### 5、给用户赋予角色

#### 6、激活角色

#### 7、撤销用户的角色

#### 8、设置强制角色（默认角色）

#### 9、删除角色



### 三、流程控制

#### 1、分支结构

##### （1）IF

###### 语法格式

```sql
IF 表达式1 THEN 操作1
[ELSEIF 表达式2 THEN 操作2]
...
[ELSE 操作n]
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

#### 3、跳转语句

##### （1）LEAVE

##### （2）ITERATE



### 四、游标和触发器

#### 1、游标

#### 2、触发器



### 五、窗口函数（```MySQL``` 8.0新特性）



