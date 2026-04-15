# Java 后端开发规范 - GitHub Copilot

> 适用于 VS Code + GitHub Copilot Chat / IntelliJ IDEA + GitHub Copilot
> 完整规范文档：https://github.com/haixiangZhang/rule-repo

---

## 技术栈

- **语言**：Java 11+
- **框架**：Spring Boot、Spring Cloud、Dubbo
- **持久层**：MyBatis / MyBatis-Plus
- **数据库**：MySQL、Redis
- **构建**：Maven / Gradle
- **日志**：SLF4J + @Slf4j（Lombok）

---

## 命名规范

| 类型 | 规则 | 示例 |
|------|------|------|
| 类名 | UpperCamelCase | `UserServiceImpl` |
| 方法/变量 | lowerCamelCase | `getUserById` |
| 常量 | UPPER_SNAKE_CASE | `MAX_PAGE_SIZE` |
| 包名 | 全小写，点分隔 | `com.company.project` |
| 数据库字段 | snake_case | `user_name`, `time_create` |

**特殊类命名：**

| 类型 | 命名 | 示例 |
|------|------|------|
| Controller | XxxController | `UserController` |
| Service 接口 | XxxService | `UserService` |
| Service 实现 | XxxServiceImpl | `UserServiceImpl` |
| Mapper | XxxMapper | `UserMapper` |
| 请求对象 | XxxRO | `UserQueryRO` |
| 返回对象 | XxxVO | `UserVO` |
| 数据传输 | XxxDTO | `UserDTO` |

---

## Controller 规范

```java
@Slf4j
@Validated
@ApiCode(value = ApiCodeConstants.User.code)
@Api(tags = "用户接口")
@RestController
@RequestMapping("/open/basic/" + VERSION)
public class UserController {

    @ApiOperation("获取用户信息")
    @ApiCode(value = ApiCodeConstants.User.getUser,
             authMode = {ApiAuthEnum.SERVER, ApiAuthEnum.USER_TOB})
    @GetMapping("/users/nums/{num}")
    public JsonResponse<UserVO> getUser(@PathVariable("num") String userNum) {
        return JsonResponse.getInstance(userService.get(userNum));
    }
}
```

**URL 命名规则：**
- 使用下划线：`/users/nums/{num}`、`/org_nodes/{node_id}`
- 批量操作加 `/batch` 后缀
- 搜索接口加 `/search` 后缀
- 查询接口加 `/query` 后缀

---

## 异常处理规范

- 业务异常统一使用 `BizException`，**禁止**直接抛出 `RuntimeException`
- 错误码配置在 `resources/message.properties`

```java
// 正确
if (CollectionUtils.isEmpty(nums)) {
    throw new BizException("nums.length.invalid");
}
throw new BizException("approval_role.invalid", approvalRole);

// 禁止
throw new RuntimeException("参数错误");
```

---

## 分页规范

```java
// 人员查询：最大 1000 条
@PageableDefault(page = 1, size = 20)
@MaxPageSize(value = Constants.PAGE_SIZE_1K) Pageable pageable

// 组织/岗位查询：最大 2000 条
@PageableDefault(page = 1, size = 20)
@MaxPageSize(value = Constants.PAGE_SIZE_2K) Pageable pageable

// 批量 GET 接口：最大 200 条
Constants.PAGE_SIZE_BATCH_GET
```

返回值使用 `PageResponse<T>` 包装。

---

## 日志规范

```java
// 使用 @Slf4j，禁止 System.out.println
@Slf4j
public class UserService {
    public void createUser(UserDTO dto) {
        log.info("创建用户, num: {}", dto.getNum());       // 关键业务节点
        log.warn("用户已存在, num: {}", dto.getNum());     // 警告
        log.error("创建用户失败, num: {}", dto.getNum(), e); // 异常带堆栈
    }
}
```

**禁止：**
- `System.out.println`
- 在循环中打印日志
- 日志字符串拼接（用占位符 `{}`）

---

## 数据库规范

```java
// 禁止 SELECT *，必须指定字段
// 禁止在循环中执行 SQL（N+1 问题）
// 查询结果超 1000 条必须分页
// UPDATE/DELETE 必须带 WHERE 条件
```

- 金额使用 `INT`（单位：分）
- 时间使用 `BIGINT`（单位：毫秒）
- 禁止使用 `FLOAT`/`DOUBLE`，精确浮点用 `DECIMAL`
- 字符集使用 `utf8mb4`

---

## 代码风格

```java
// 使用 Lombok 简化代码
@Data
@Builder
@Slf4j
@NoArgsConstructor
@AllArgsConstructor

// 使用构造器注入（禁止 @Autowired 字段注入）
private final UserService userService;

// 方法不超过 50 行，单一职责
// 使用 Optional 避免空指针
// 使用 String.format 或 StrUtil，禁止 + 拼接
```

---

## Git 提交规范

```
<type>(<scope>): <subject>

类型：feat / fix / docs / style / refactor / perf / test / chore
```

示例：
```
feat(user): 添加用户批量查询接口
fix(order): 修复订单金额精度问题
```

---

## 代码审查清单

生成或修改代码时，自动检查：

- [ ] 无空指针风险（使用 Optional 或判空）
- [ ] 无 SQL 注入风险（参数化查询）
- [ ] 无线程安全问题
- [ ] 无 N+1 查询问题
- [ ] 异常使用 `BizException`
- [ ] 日志使用 `@Slf4j` + 占位符
- [ ] 接口有 `@ApiOperation` 注解
- [ ] 入参有 `@Validated` 校验
- [ ] 禁止 `SELECT *`
