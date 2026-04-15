## MySQL 开发规范
适用范围：MySQL 表设计 / DDL / DML / SQL Review

---
### 一、命名规范（Naming Rules）
R-DB-01 数据库对象命名
- 必须使用英文
- 禁止中文、空格、特殊符号、SQL 关键字
- 长度 ≤ 32 字符
- 要求语义明确（见名知意）

---
R-DB-02 库名 / 字段名
- 必须全小写
- 必须使用下划线分隔
- 示例：
- pupu_main
  buyer_id

---
R-DB-03 表名
- 必须全小写
- 多个单词使用下划线、连接符
- 建议使用英文复数
- 必须在全库范围内唯一
- 跨库同义表：必须加业务前缀
- hr_users
  admin_users

---
R-DB-04 索引命名
- 唯一索引：uk_xxx
- 普通索引：idx_xxx
- 联合索引：idx_col1_col2_col3
- 必须全小写
- 禁止随意缩写、无语义命名

---
### 二、表与字段设计规范（Schema Rules）
R-DB-10 存储引擎与字符集
- 必须使用 InnoDB
- 必须显式指定字符集 utf8
- 校对规则：utf8_general_ci

---
R-DB-11 注释规范
- 所有表、字段必须有 COMMENT
- 禁止使用英文分号 ;
- 枚举字段：
- 10-待支付，20-已支付，30-已取消

---
R-DB-12 时间字段（强制）
- 每个表必须包含
    - time_create
    - time_update
- 类型：BIGINT
- 精度：毫秒
- time_create 创建后禁止更新

---
R-DB-13 数据类型规范
- 非负数：UNSIGNED
- 时间：BIGINT（毫秒）
- 金额：INT（分）
- 枚举：TINYINT / SMALLINT
- 精确浮点：必须使用 DECIMAL
- 禁止 FLOAT / DOUBLE

---
R-DB-14 NULL 与默认值
- 强烈建议字段使用 NOT NULL
- TEXT / BLOB 可例外
- 建议合理设置 DEFAULT
- 原因：
    - NULL 占额外空间
    - 聚合结果不稳定
    - 数据迁移风险高

---
R-DB-15 索引设计
- 禁止冗余索引、重复索引
- 联合索引：
    - 字段数 > 3 必须评估合理性
    - time_create 禁止作为第一个索引字段
- 单索引记录长度 ≤ 64KB
- 分区表 SQL 必须包含分区键

---
R-DB-16 外键与数据库能力
- 禁止
    - 外键
    - 存储过程
    - 视图
    - 触发器

---
R-DB-17 DDL 风险操作
- 生产环境禁止
- DROP TABLE
- 正确流程：
- RENAME TABLE A TO A_move;
  -- DBA 定时清理

---
### 三、SQL 编写规范（DML / Query Rules）
R-SQL-01 SELECT / INSERT
- 禁止 SELECT *
- INSERT / SELECT 必须显式字段

---
R-SQL-02 ALTER TABLE
- 多个字段变更：
    - 必须合并为一条 ALTER
- 禁止频繁小 ALTER

---
R-SQL-03 WHERE 条件
- 条件字段 必须与字段类型一致
- 必须命中索引（Explain）

---
R-SQL-04 UPDATE / DELETE（生产环境）
- 必须分批执行
- 单批：
    - ≤ 500 行
    - ≤ 表总行数 10%
- 禁止一次性大事务
