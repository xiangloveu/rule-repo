# 项目开发规范 - Claude Code

> 适用于 Claude Code CLI 工具。
> 将此文件放在项目根目录，Claude Code 会自动加载。

---

## 规范文档

请加载并严格遵循以下规范文档：

- **Java 代码规范**：https://raw.githubusercontent.com/haixiangZhang/rule-repo/main/rules/JAVA_CODE_GUIDE.md
- **Git 提交规范**：https://raw.githubusercontent.com/haixiangZhang/rule-repo/main/rules/GIT_COMMIT_GUIDE.md
- **MySQL 规范**：https://raw.githubusercontent.com/haixiangZhang/rule-repo/main/rules/MYSQL_GUIDE.md
- **Java 测试规范**：https://raw.githubusercontent.com/haixiangZhang/rule-repo/main/rules/JAVA_TEST_GUIDE.md

---

## 项目结构

标准 Spring Boot 多模块项目结构：

```
project/
├── project-facade/          # 对外接口定义（DTO、RO、VO、接口声明）
├── project-impl/            # 业务实现（Service、Mapper、Controller）
│   └── src/main/
│       ├── controller/      # 接口层
│       ├── service/         # 业务层
│       │   └── impl/
│       ├── mapper/          # 数据访问层
│       ├── entity/          # 数据库实体
│       └── config/          # 配置类
├── project-admin/           # 管理后台
└── pom.xml
```

---

## 常用命令

```bash
# 编译
mvn clean compile

# 运行测试
mvn test

# 打包
mvn clean package -DskipTests

# 运行单个测试类
mvn test -Dtest=UserServiceImplTest

# 查看依赖树
mvn dependency:tree
```

---

## 行为准则

### 修改代码前

1. 使用 `Read` 工具完整阅读目标文件，理解现有实现
2. 使用 `Grep` 搜索相关类、方法的使用场景，避免破坏已有逻辑
3. 对于复杂任务，先用 `Glob` 了解模块结构再动手

### 修改代码时

1. **最小化改动**：只修改与任务直接相关的代码
2. **保持风格一致**：新代码风格与文件现有风格保持一致
3. **不删已有注释**：除非注释明确错误，否则保留
4. **异常规范**：业务异常统一使用 `BizException`
5. **日志规范**：使用 `@Slf4j`，占位符 `{}`，禁止 `System.out`

### 修改代码后

1. 运行受影响模块的单元测试验证正确性
2. 新增方法必须附带单元测试
3. 提交前检查：无 `Select *`、无循环 SQL、无空指针隐患

---

## Git 提交规范

```
<type>(<scope>): <subject>
```

| type | 场景 |
|------|------|
| `feat` | 新功能 |
| `fix` | Bug 修复 |
| `refactor` | 重构 |
| `test` | 测试 |
| `docs` | 文档 |
| `chore` | 构建/依赖 |

**要求：**
- 使用中文描述
- 一次提交只做一件事
- 关联 Issue 写在 footer：`Closes #123`

---

## 代码审查清单

每次生成或修改代码，自动检查以下项目：

**功能正确性**
- [ ] 入参校验完整（`@Validated` + `BizException`）
- [ ] 边界条件处理（null、空集合、越界）
- [ ] 异常路径有日志记录

**性能**
- [ ] 无 N+1 查询（循环内无 SQL）
- [ ] 大结果集有分页（超 1000 条）
- [ ] 有缓存的地方正确使用 Redis

**安全**
- [ ] 无 SQL 注入风险（参数化查询）
- [ ] 敏感数据接口加 `@EncryptDecrypt`
- [ ] 无硬编码密钥/密码

**规范**
- [ ] 禁止 `SELECT *`
- [ ] 禁止 `System.out.println`
- [ ] 禁止直接抛出 `RuntimeException`
- [ ] 禁止 `@Autowired` 字段注入

---

## 禁止事项

- ❌ 未读原文件直接覆盖修改
- ❌ 一次性修改超过 3 个不相关文件
- ❌ 提交包含 `.env`、密钥、测试数据库连接信息
- ❌ 跳过测试直接提交（`-DskipTests` 仅用于打包）
