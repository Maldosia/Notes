粒度分为：

- 表锁
- 行锁

```MYSQL
-- 显式加锁
-- 设置读锁锁表
LOCK TABLE t_user READ; -- 阻塞其它事务修改表数据
-- 设置写锁锁表
LOCK TABLE t_user WRITE; -- 阻塞其它事务读和写

-- 隐式加锁
-- Innodb 自动加意向锁
-- 对于UPDATE、DELETE和INSERT语句，Innodb会自动给涉及的数据集加排它锁
-- 对于普通的SELETE语句，Innodb不会加任何锁
```

锁级别分为：

- 共享锁(Share Lock，S锁)，读锁。可以并发读，但其它事务无法加排它锁。
- 排它锁(Exclusive Lock，X锁)，写锁。其它事务无法加任何类型的锁。
- 意向锁(Intention Lock)。表级锁，为了在一个事务中揭示下一行将要被请求锁的类型。

```MYSQL
-- ------------------共享锁--------------------
-- SESSION A
BEGIN; -- 1
SELECT * FROM t_user A WHERE A.Sid = '1' LOCK IN SHARE MODE; -- 3,设置共享锁
UPDATE t_user A SET A.Name = '张三三' WHERE A.Id = '1'; -- 5 处于执行中状态，等待6 报错后成功

-- SESSION B
BEGIN; -- 2
SELECT * FROM t_user A WHERE A.Sid = '1' LOCK IN SHARE MODE; -- 4，设置共享锁
UPDATE t_user A SET A.Name = '张三三' WHERE A.Id = '1'; -- 6 报错
```

```MYSQL
-- ------------------排它锁，表级别--------------------
-- SESSION A
BEGIN;
SELECT * FROM t_user A WHERE A.Sid = '1' FOR UPDATE; -- Sid不是索引列
-- COMMIT;

-- SESSION B
SELECT * FROM t_user A WHERE A.Sid = '1' FOR UPDATE; -- 处于执行中状态
SELECT * FROM t_user A WHERE A.Sid = '2' FOR UPDATE; -- 处于执行中状态
```

```mysql
-- ------------------排它锁，行级别--------------------
-- SESSION A
BEGIN;
SELECT * FROM t_user A WHERE A.id = '1' FOR UPDATE; -- id为主键，因为行级别建立在索引上
-- COMMIT;

-- SESSION B
SELECT * FROM t_user A WHERE A.id = '1' FOR UPDATE; -- 处于执行中状态

-- SESSION C
SELECT * FROM t_user A WHERE A.id = '2' FOR UPDATE; -- 正常完成
```

意向锁：

- 意向共享锁(IS)，表示事务准备给数据行加入共享锁，也就是说一个数据行加共享锁前必须先取得该表的IS锁。
- 意向排它锁(IX)，表示事务准备给数据行加入排它锁，也就是说一个数据行加共享锁前必须先取得该表的XS锁。

|                | 共享锁 | 排它锁 | 意向共享锁 | 意向排它锁 |
| -------------- | ------ | ------ | ---------- | ---------- |
| **共享锁**     | 兼容   | 冲突   | 兼容       | 冲突       |
| **排它锁**     | 冲突   | 冲突   | 冲突       | 冲突       |
| **意向共享锁** | 兼容   | 冲突   | 兼容       | 兼容       |
| **意向排它锁** | 冲突   | 冲突   | 兼容       | 兼容       |

