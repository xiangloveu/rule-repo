# 项目开发规范 - OpenAI Codex

> 本文件供 Codex CLI 自动加载。所有任务执行必须严格遵循以下规范和工作流程。

---

## 规范文档

请加载并严格遵循以下规范文档中的所有要求：

- **Java 代码规范**：https://raw.githubusercontent.com/haixiangZhang/rule-repo/main/rules/JAVA_CODE_GUIDE.md
- **Git 提交规范**：https://raw.githubusercontent.com/haixiangZhang/rule-repo/main/rules/GIT_COMMIT_GUIDE.md
- **MySQL 规范**：https://raw.githubusercontent.com/haixiangZhang/rule-repo/main/rules/MYSQL_GUIDE.md
- **Java 测试规范**：https://raw.githubusercontent.com/haixiangZhang/rule-repo/main/rules/JAVA_TEST_GUIDE.md

---

## 技术栈

- Java 11+、Spring Boot、Spring Cloud、Dubbo
- MyBatis / MyBatis-Plus
- MySQL、Redis
- Maven / Gradle
- JUnit4 + Mockito

---

## 任务执行工作流

### Dubbo 接口参数变更规范

**修改 Dubbo 对外接口参数前，必须暂停操作，告知用户影响，等待确认后再执行。**

**高风险变更（必须用户确认）：**
- 泛型参数类型替换：`List<Long>` → `List<String>`
- 基础类型替换：`Long` → `String`、`Integer` → `Long`
- 删除已有参数 / 修改返回值类型

**如何判断是否为 Dubbo 对外接口：**
- 接口位于 `facade` / `api` 模块，或包路径含 `facade`、`api`、`provider`
- 接口或实现类有 `@DubboService` 注解

**安全变更（可直接修改）：**
- `@RestController` HTTP 接口（JSON 序列化，容错性强）
- Service / Manager / Mapper 等本服务内部类

**推荐兼容方案：**
```java
// 保留旧方法 + 新增方法，或升级 Dubbo 版本号
@Deprecated
List<Long> getUserIds();

List<String> getUserStringIds(); // 新增
```

---

### 编写代码前

1. **先读文件**：修改已有文件前，必须先完整阅读该文件
2. **理解上下文**：了解相关类的结构、依赖关系、已有方法
3. **确认接口契约**：查看 Service 接口再实现，不要凭空假设方法签名

### 编写代码时

1. **遵循分层架构**：Controller → Service → Mapper，禁止跨层调用
2. **使用项目已有工具类**：优先复用 `BizException`、`JsonResponse`、`PageResponse` 等
3. **入参必须校验**：Controller 方法参数加 `@Validated`，业务逻辑加 `BizException` 兜底
4. **异常统一**：业务异常只用 `BizException`，禁止 `RuntimeException`
5. **禁止魔法值**：数字、字符串常量必须定义为 `static final` 常量

### 编写代码后

1. **自检清单**：
   - [ ] 有无 `SELECT *`
   - [ ] 有无循环内执行 SQL（N+1）
   - [ ] 有无空指针风险
   - [ ] 日志是否使用占位符而非字符串拼接
   - [ ] 新增方法是否有单元测试
2. **生成单元测试**：新增 Service 方法必须附带 JUnit4 单元测试
3. **提交信息**：按 `<type>(<scope>): <subject>` 格式，使用中文描述

---

## 代码生成规范

### 生成 Controller

```java
@Slf4j
@Validated
@Api(tags = "模块名称")
@RestController
@RequestMapping("/api/v1")
public class XxxController {

    @Autowired
    private XxxService xxxService;

    @ApiOperation("接口说明")
    @GetMapping("/xxx/{id}")
    public JsonResponse<XxxVO> getXxx(@PathVariable Long id) {
        return JsonResponse.getInstance(xxxService.getById(id));
    }
}
```

### 生成 Service

```java
public interface XxxService {
    XxxVO getById(Long id);
}

@Slf4j
@Service
public class XxxServiceImpl implements XxxService {

    private final XxxMapper xxxMapper;

    public XxxServiceImpl(XxxMapper xxxMapper) {
        this.xxxMapper = xxxMapper;
    }

    @Override
    public XxxVO getById(Long id) {
        if (id == null) {
            throw new BizException("id.not.null");
        }
        Xxx xxx = xxxMapper.selectById(id);
        if (xxx == null) {
            throw new BizException("xxx.not.found");
        }
        return convert(xxx);
    }
}
```

### 生成单元测试

```java
public class XxxServiceImplTest {

    private XxxMapper xxxMapper = Mockito.mock(XxxMapper.class);
    private XxxServiceImpl xxxService = new XxxServiceImpl(xxxMapper);

    /**
     * 测试 getById 方法 - 正常获取
     * 功能说明：当 id 存在时，返回对应的 VO
     * 输入：id = 1L
     * 输出：非空的 XxxVO
     */
    @Test
    public void testGetById_Success() {
        // Given
        Long id = 1L;
        Xxx xxx = new Xxx();
        xxx.setId(id);
        Mockito.when(xxxMapper.selectById(id)).thenReturn(xxx);

        // When
        XxxVO result = xxxService.getById(id);

        // Then
        Assert.assertNotNull(result);
    }

    /**
     * 测试 getById 方法 - id 为 null
     * 功能说明：当 id 为 null 时，抛出 BizException
     * 输入：id = null
     * 输出：抛出 BizException
     */
    @Test(expected = BizException.class)
    public void testGetById_NullId() {
        xxxService.getById(null);
    }
}
```

---

## 禁止事项

- ❌ 禁止 `SELECT *`
- ❌ 禁止 `System.out.println`，使用 `log.info/warn/error`
- ❌ 禁止直接抛出 `RuntimeException`，使用 `BizException`
- ❌ 禁止在循环中执行数据库查询
- ❌ 禁止字段注入 `@Autowired`，使用构造器注入
- ❌ 禁止硬编码数字/字符串，使用常量
- ❌ 禁止修改文件前不读取原文件内容
- ❌ 禁止一次提交混合多个不相关功能
