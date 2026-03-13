# 数据库

数据库是现代应用的核心组件，掌握数据库知识对开发者至关重要。

## MySQL 基础

### 数据库操作

```sql
-- 创建数据库
CREATE DATABASE mydb CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;

-- 选择数据库
USE mydb;

-- 删除数据库
DROP DATABASE mydb;

-- 查看所有数据库
SHOW DATABASES;
```

### 表操作

```sql
-- 创建表
CREATE TABLE users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    username VARCHAR(50) NOT NULL UNIQUE,
    email VARCHAR(100) NOT NULL,
    age INT DEFAULT 0,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

-- 查看表结构
DESCRIBE users;
SHOW COLUMNS FROM users;

-- 修改表
ALTER TABLE users ADD COLUMN phone VARCHAR(20);
ALTER TABLE users DROP COLUMN phone;
ALTER TABLE users MODIFY COLUMN username VARCHAR(100);

-- 删除表
DROP TABLE users;

-- 重命名表
RENAME TABLE old_name TO new_name;
```

### CRUD 操作

```sql
-- 插入数据
INSERT INTO users (username, email, age) 
VALUES ('pdkst', 'pdkst@example.com', 25);

-- 批量插入
INSERT INTO users (username, email, age) VALUES
    ('user1', 'user1@example.com', 20),
    ('user2', 'user2@example.com', 30);

-- 查询数据
SELECT * FROM users;
SELECT id, username, email FROM users WHERE age > 20;
SELECT * FROM users WHERE username LIKE 'p%';
SELECT * FROM users WHERE created_at BETWEEN '2024-01-01' AND '2024-12-31';

-- 排序
SELECT * FROM users ORDER BY age DESC;
SELECT * FROM users ORDER BY age ASC, username DESC;

-- 限制结果
SELECT * FROM users LIMIT 10;
SELECT * FROM users LIMIT 10, 5; -- 跳过前10条，取5条

-- 更新数据
UPDATE users SET age = 26 WHERE username = 'pdkst';
UPDATE users SET email = 'new@example.com' WHERE id = 1;

-- 删除数据
DELETE FROM users WHERE id = 1;
DELETE FROM users WHERE age < 20;

-- 清空表（保留表结构）
TRUNCATE TABLE users;
```

## 查询优化

### 索引

```sql
-- 创建索引
CREATE INDEX idx_username ON users(username);
CREATE UNIQUE INDEX idx_email ON users(email);
CREATE INDEX idx_age_email ON users(age, email);

-- 查看索引
SHOW INDEX FROM users;

-- 删除索引
DROP INDEX idx_username ON users;
```

### JOIN 操作

```sql
-- INNER JOIN
SELECT u.username, o.order_date, o.amount
FROM users u
INNER JOIN orders o ON u.id = o.user_id;

-- LEFT JOIN
SELECT u.username, o.order_date
FROM users u
LEFT JOIN orders o ON u.id = o.user_id;

-- RIGHT JOIN
SELECT u.username, o.order_date
FROM users u
RIGHT JOIN orders o ON u.id = o.user_id;

-- 多表 JOIN
SELECT u.username, p.product_name, o.order_date
FROM users u
JOIN orders o ON u.id = o.user_id
JOIN products p ON o.product_id = p.id;
```

### 聚合函数

```sql
-- COUNT
SELECT COUNT(*) FROM users;
SELECT COUNT(age) FROM users; -- 不统计 NULL 值

-- SUM, AVG, MAX, MIN
SELECT SUM(amount), AVG(amount), MAX(amount), MIN(amount)
FROM orders;

-- GROUP BY
SELECT user_id, COUNT(*) as order_count, SUM(amount) as total
FROM orders
GROUP BY user_id;

-- HAVING
SELECT user_id, COUNT(*) as order_count
FROM orders
GROUP BY user_id
HAVING COUNT(*) > 5;
```

### 子查询

```sql
-- EXISTS
SELECT * FROM users u
WHERE EXISTS (
    SELECT 1 FROM orders o WHERE o.user_id = u.id
);

-- IN
SELECT * FROM users
WHERE id IN (SELECT user_id FROM orders WHERE amount > 100);

-- JOIN 代替子查询（性能更好）
SELECT u.*, o.*
FROM users u
JOIN orders o ON u.id = o.user_id;
```

## 高级特性

### 事务

```sql
-- 开始事务
START TRANSACTION;
-- 或
BEGIN;

-- 执行 SQL
UPDATE account SET balance = balance - 100 WHERE id = 1;
UPDATE account SET balance = balance + 100 WHERE id = 2;

-- 提交事务
COMMIT;

-- 回滚事务
ROLLBACK;

-- 事务隔离级别
SET TRANSACTION ISOLATION LEVEL READ UNCOMMITTED;  -- 读未提交
SET TRANSACTION ISOLATION LEVEL READ COMMITTED;   -- 读已提交
SET TRANSACTION ISOLATION LEVEL REPEATABLE READ;   -- 可重复读
SET TRANSACTION ISOLATION LEVEL SERIALIZABLE;      -- 串行化
```

### 视图

```sql
-- 创建视图
CREATE VIEW user_orders AS
SELECT u.id, u.username, COUNT(o.id) as order_count, SUM(o.amount) as total
FROM users u
LEFT JOIN orders o ON u.id = o.user_id
GROUP BY u.id, u.username;

-- 查询视图
SELECT * FROM user_orders WHERE order_count > 5;

-- 删除视图
DROP VIEW user_orders;
```

### 存储过程

```sql
DELIMITER //

CREATE PROCEDURE GetUserOrders(IN userId INT)
BEGIN
    SELECT u.username, o.order_date, o.amount
    FROM users u
    JOIN orders o ON u.id = o.user_id
    WHERE u.id = userId;
END //

DELIMITER ;

-- 调用存储过程
CALL GetUserOrders(1);

-- 删除存储过程
DROP PROCEDURE GetUserOrders;
```

### 触发器

```sql
-- 创建触发器
DELIMITER //

CREATE TRIGGER before_user_insert
BEFORE INSERT ON users
FOR EACH ROW
BEGIN
    SET NEW.created_at = NOW();
END //

DELIMITER ;

-- 删除触发器
DROP TRIGGER before_user_insert;
```

## 性能优化

### EXPLAIN 分析

```sql
-- 分析查询执行计划
EXPLAIN SELECT * FROM users WHERE username = 'pdkst';

-- 查看详细信息
EXPLAIN FORMAT=JSON SELECT * FROM users;
```

### 慢查询日志

```sql
-- 查看慢查询配置
SHOW VARIABLES LIKE 'slow_query%';

-- 开启慢查询
SET GLOBAL slow_query_log = 'ON';
SET GLOBAL long_query_time = 2; -- 超过2秒的查询
```

### 批量操作优化

```sql
-- 批量插入（比单条插入快）
INSERT INTO users (username, email, age) VALUES
    ('user1', 'user1@example.com', 20),
    ('user2', 'user2@example.com', 30),
    ('user3', 'user3@example.com', 25);

-- 使用 LOAD DATA 导入大量数据
LOAD DATA INFILE '/path/to/file.csv'
INTO TABLE users
FIELDS TERMINATED BY ','
LINES TERMINATED BY '\n'
(username, email, age);
```

## 数据备份与恢复

### 备份

```bash
# 备份单个数据库
mysqldump -u root -p mydb > mydb_backup.sql

# 备份所有数据库
mysqldump -u root -p --all-databases > all_databases_backup.sql

# 备份特定表
mysqldump -u root -p mydb users orders > tables_backup.sql

# 压缩备份
mysqldump -u root -p mydb | gzip > mydb_backup.sql.gz
```

### 恢复

```bash
# 恢复数据库
mysql -u root -p mydb < mydb_backup.sql

# 恢复压缩的备份
gunzip < mydb_backup.sql.gz | mysql -u root -p mydb
```

## 安全配置

```sql
-- 创建用户
CREATE USER 'appuser'@'localhost' IDENTIFIED BY 'password123';

-- 授予权限
GRANT ALL PRIVILEGES ON mydb.* TO 'appuser'@'localhost';

-- 授予特定权限
GRANT SELECT, INSERT, UPDATE, DELETE ON mydb.* TO 'appuser'@'localhost';

-- 刷新权限
FLUSH PRIVILEGES;

-- 查看权限
SHOW GRANTS FOR 'appuser'@'localhost';

-- 删除用户
DROP USER 'appuser'@'localhost';

-- 修改密码
ALTER USER 'appuser'@'localhost' IDENTIFIED BY 'newpassword';
```

## 常见问题

### 连接问题

```sql
-- 查看最大连接数
SHOW VARIABLES LIKE 'max_connections';

-- 查看当前连接数
SHOW STATUS LIKE 'Threads_connected';

-- 查看连接信息
SHOW PROCESSLIST;

-- 杀死连接
KILL connection_id;
```

### 锁问题

```sql
-- 查看锁等待
SELECT * FROM information_schema.INNODB_LOCKS;
SELECT * FROM information_schema.INNODB_LOCK_WAITS;

-- 查看当前事务
SELECT * FROM information_schema.INNODB_TRX;
```

## 参考资料

- [MySQL 官方文档](https://dev.mysql.com/doc/)
- [MySQL 性能优化](https://dev.mysql.com/doc/refman/8.0/en/optimization.html)
- [数据库系统概论](https://book.douban.com/subject/10546109/)

---

*最后更新时间: {docsify-updated}*