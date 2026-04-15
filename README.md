# Rule Repository

这是一个集中管理各种开发规范和配置规则的仓库，方便在不同 AI 编码工具中引用和复用。

## 📁 目录结构

```
rule-repo/
├── rules/                       # 通用规范文档（工具无关，唯一数据源）
│   ├── JAVA_CODE_GUIDE.md       # Java 代码规范
│   ├── GIT_COMMIT_GUIDE.md      # Git 提交规范
│   ├── JAVA_TEST_GUIDE.md       # Java 测试规范
│   └── MYSQL_GUIDE.md           # MySQL 数据库规范
├── cursor/                      # Cursor 专用配置
│   └── .cursorrules             # Cursor 主配置文件（内联规范）
├── copilot/                     # GitHub Copilot 专用配置
│   └── copilot-instructions.md  # 精简版规范（内联，不支持远程引用）
├── codex/                       # OpenAI Codex CLI 专用配置
│   └── AGENTS.md                # 引用 rules/ 远程 URL + 工作流指令
├── claude/                      # Claude Code 专用配置
│   └── CLAUDE.md                # 引用 rules/ 远程 URL + Claude 特有指令
└── README.md
```

---

## 📖 规则文件说明

### 通用规范文档（rules/）

| 文件 | 说明 | 更新时间 |
|------|------|----------|
| `rules/JAVA_CODE_GUIDE.md` | Java 代码规范（命名、接口、分页、异常处理等） | 2026-02-12 ✨ |
| `rules/GIT_COMMIT_GUIDE.md` | Git 提交规范（格式、分支策略、工作流） | - |
| `rules/JAVA_TEST_GUIDE.md` | Java 测试规范（单元测试、集成测试） | - |
| `rules/MYSQL_GUIDE.md` | MySQL 规范（表设计、索引、SQL 优化） | - |

### 工具专用配置

| 文件 | 工具 | 内容策略 |
|------|------|---------|
| `cursor/.cursorrules` | Cursor | 内联完整规范，对话式 AI 指令风格 |
| `copilot/copilot-instructions.md` | GitHub Copilot | 精简内联，适配 VS Code / IDEA |
| `codex/AGENTS.md` | OpenAI Codex CLI | 远程 URL 引用 + 任务执行工作流 |
| `claude/CLAUDE.md` | Claude Code | 远程 URL 引用 + 项目结构 + 审查清单 |

---

## 🚀 各工具使用指南

### 一、Cursor

Cursor 通过项目根目录的 `.cursorrules` 文件加载规则，AI 助手会自动遵循其中的编码规范。

#### 方式一：直接引用 GitHub 文件（推荐）

在项目根目录创建 `.cursorrules` 文件，写入以下内容：

```
@https://raw.githubusercontent.com/haixiangZhang/rule-repo/main/cursor/.cursorrules
```

Cursor 会在每次对话时自动拉取最新规则，无需手动维护。

#### 方式二：下载到本地

```bash
curl -o .cursorrules https://raw.githubusercontent.com/haixiangZhang/rule-repo/main/cursor/.cursorrules
```

#### 方式三：Git Submodule（团队协作推荐）

```bash
# 添加 submodule
git submodule add https://github.com/haixiangZhang/rule-repo.git .rules

# 创建软链接到项目根目录
ln -s .rules/cursor/.cursorrules .cursorrules
```

> **注意**：`.cursorrules` 文件必须放在**项目根目录**才能被 Cursor 识别。

---

### 二、OpenAI Codex CLI

Codex CLI 通过 `AGENTS.md` 文件加载项目规范，支持在项目根目录或 `~/.codex/` 全局目录放置说明文件。

#### 方式一：项目级配置（推荐）

在项目根目录创建 `AGENTS.md` 文件，引入本仓库规范：

```markdown
# 项目开发规范

请严格遵循以下规范文档中的所有要求：

## Java 代码规范
https://raw.githubusercontent.com/haixiangZhang/rule-repo/main/rules/JAVA_CODE_GUIDE.md

## Git 提交规范
https://raw.githubusercontent.com/haixiangZhang/rule-repo/main/rules/GIT_COMMIT_GUIDE.md

## MySQL 规范
https://raw.githubusercontent.com/haixiangZhang/rule-repo/main/rules/MYSQL_GUIDE.md

## 测试规范
https://raw.githubusercontent.com/haixiangZhang/rule-repo/main/rules/JAVA_TEST_GUIDE.md
```

#### 方式二：下载规范到本地并引用

```bash
# 在项目根目录创建规范目录
mkdir -p .codex

# 下载规范文件
curl -o .codex/JAVA_CODE_GUIDE.md https://raw.githubusercontent.com/haixiangZhang/rule-repo/main/rules/JAVA_CODE_GUIDE.md
curl -o .codex/GIT_COMMIT_GUIDE.md https://raw.githubusercontent.com/haixiangZhang/rule-repo/main/rules/GIT_COMMIT_GUIDE.md
curl -o .codex/MYSQL_GUIDE.md https://raw.githubusercontent.com/haixiangZhang/rule-repo/main/rules/MYSQL_GUIDE.md
curl -o .codex/JAVA_TEST_GUIDE.md https://raw.githubusercontent.com/haixiangZhang/rule-repo/main/rules/JAVA_TEST_GUIDE.md
```

然后在 `AGENTS.md` 中本地引用：

```markdown
# 项目开发规范

@.codex/JAVA_CODE_GUIDE.md
@.codex/GIT_COMMIT_GUIDE.md
@.codex/MYSQL_GUIDE.md
@.codex/JAVA_TEST_GUIDE.md
```

#### 方式三：全局配置（适用所有项目）

```bash
# 创建全局配置目录
mkdir -p ~/.codex

# 创建全局说明文件
cat > ~/.codex/instructions.md << 'EOF'
# 全局开发规范

请遵循以下 Java 后端开发规范：
- 命名规范、代码格式、注释规范
- Controller / Service / DAO 分层规范
- 异常处理使用 BizException
- 日志规范：使用 SLF4J，禁止 System.out
- Git 提交格式：feat/fix/docs/refactor/test/chore
- SQL 禁止 SELECT *，必须走索引
EOF
```

> **提示**：Codex CLI 会按照 `~/.codex/instructions.md` → 项目根目录 `AGENTS.md` 的顺序合并加载规则，项目级配置优先级更高。

---

### 三、GitHub Copilot

#### 在 VS Code 中使用

GitHub Copilot 在 VS Code 中通过 `.github/copilot-instructions.md` 文件加载自定义规范（需要 Copilot Chat 功能）。

**步骤：**

1. 在项目根目录创建 `.github/copilot-instructions.md` 文件：

```bash
mkdir -p .github
```

2. 写入规范引导内容（建议将核心规范内联进来，Copilot 不支持远程 URL 引用）：

```bash
# 下载规范内容到 copilot-instructions.md
curl -s https://raw.githubusercontent.com/haixiangZhang/rule-repo/main/rules/JAVA_CODE_GUIDE.md >> .github/copilot-instructions.md
```

或手动创建 `.github/copilot-instructions.md`，内容示例：

```markdown
# 项目开发规范

## 基本原则
- 使用 Java 11+，遵循 Spring Boot 开发规范
- 所有接口必须有 Swagger 注解和入参校验
- 异常统一使用 BizException，禁止直接抛出 RuntimeException
- 日志使用 SLF4J + @Slf4j，禁止 System.out.println
- SQL 禁止 SELECT *，更新必须带 WHERE 条件

## 命名规范
- 类名：UpperCamelCase（如 UserService）
- 方法/变量：lowerCamelCase（如 getUserById）
- 常量：UPPER_SNAKE_CASE（如 MAX_PAGE_SIZE）
- 数据库字段：snake_case（如 user_name）

## Git 提交格式
type(scope): subject
类型：feat / fix / docs / refactor / test / chore
```

3. 在 VS Code 设置中开启自定义指令（`settings.json`）：

```json
{
  "github.copilot.chat.codeGeneration.useInstructionFiles": true
}
```

> **注意**：此功能需要 VS Code 版本 ≥ 1.90 且安装了 GitHub Copilot Chat 扩展。

#### 在 IntelliJ IDEA 中使用

IDEA 插件版 Copilot 暂不支持自动加载 `.github/copilot-instructions.md`，但可以通过以下方式让 Copilot Chat 遵循规范：

**方式一：在 Copilot Chat 对话开头手动引入规范（临时生效）**

在 IDEA 的 Copilot Chat 窗口中，对话开始时粘贴以下 prompt：

```
请在本次对话中严格遵循以下开发规范：
1. Java 命名：类名 UpperCamelCase，方法/变量 lowerCamelCase，常量 UPPER_SNAKE_CASE
2. 异常处理：业务异常使用 BizException，禁止直接抛出 RuntimeException
3. 日志：使用 @Slf4j + SLF4J，禁止 System.out
4. SQL：禁止 SELECT *，UPDATE/DELETE 必须带 WHERE，禁止在循环中执行 SQL
5. 接口：Controller 必须有 @ApiOperation 注解，入参必须有 @Validated 校验
6. Git 提交：遵循 feat/fix/docs/refactor/test/chore 格式
完整规范参见：https://github.com/haixiangZhang/rule-repo
```

**方式二：创建 Live Template 快速注入规范（推荐）**

在 IDEA 中配置代码模板，快速插入规范 prompt：

1. 打开 `Settings` → `Editor` → `Live Templates`
2. 新建模板，缩写设为 `copilot-rule`
3. 内容填入规范 prompt（同方式一）
4. 在 Copilot Chat 输入框中输入 `copilot-rule` + Tab 即可快速展开

**方式三：通过 `.editorconfig` 约束代码风格（被动生效）**

在项目根目录创建 `.editorconfig`，Copilot 会参考此文件生成风格一致的代码：

```ini
root = true

[*]
charset = utf-8
indent_style = space
indent_size = 4
end_of_line = lf
trim_trailing_whitespace = true
insert_final_newline = true

[*.java]
indent_size = 4

[*.{yml,yaml}]
indent_size = 2
```

---

### 四、Claude Code

Claude Code 通过项目根目录的 `CLAUDE.md` 文件加载规范。

#### 方式一：直接复制配置文件（推荐）

```bash
curl -o CLAUDE.md https://raw.githubusercontent.com/haixiangZhang/rule-repo/main/claude/CLAUDE.md
```

#### 方式二：Git Submodule

```bash
git submodule add https://github.com/haixiangZhang/rule-repo.git .rules
ln -s .rules/claude/CLAUDE.md CLAUDE.md
```

> **提示**：`CLAUDE.md` 放在项目根目录会被 Claude Code 自动识别加载。

---

## 🎯 适用场景

- Spring Boot / Spring Cloud 项目
- MyBatis / MyBatis-Plus 项目
- 微服务架构项目
- RESTful API 项目
- Dubbo 分布式服务项目

**技术栈**: Java 11+, Spring Boot, MyBatis, MySQL, Redis, Dubbo, Maven/Gradle

---

## 📊 规范亮点

### 接口层面规范
从实际项目提取的最佳实践：
- 完整的 Controller 注解配置
- RESTful URL 设计规范（下划线命名）
- 参数验证和业务校验结合
- 字段权限控制和数据加密

### 分页规范
明确的分页场景使用规则：
- 人员查询：最大 1000 条
- 组织/岗位查询：最大 2000 条
- 批量 GET 接口：最大 200 条

### 异常处理规范
统一的异常处理机制：
- BizException 业务异常
- 错误码国际化配置
- 敏感信息保护
- 统一异常拦截

---

## 💡 使用建议

| 场景 | 推荐方式 |
|------|--------|
| 个人 Cursor 项目 | 方式一：直接引用 GitHub URL |
| 团队 Cursor 项目 | 方式三：Git Submodule |
| Codex CLI 项目 | 项目根目录创建 `AGENTS.md` |
| VS Code + Copilot | `.github/copilot-instructions.md` |
| IDEA + Copilot | Live Template 快速注入 prompt |
| Claude Code 项目 | 项目根目录创建 `CLAUDE.md` |

---

## 🔄 更新日志

### 2026-04-15
- 🏗️ 重构目录结构：规范文档移至 `rules/`，`cursor/` 仅保留 Cursor 专用配置
- 🆕 新增 `copilot/copilot-instructions.md`：GitHub Copilot 专用精简规范
- 🆕 新增 `codex/AGENTS.md`：OpenAI Codex CLI 专用配置 + 工作流指令
- 🆕 新增 `claude/CLAUDE.md`：Claude Code 专用配置 + 项目结构说明

### 2026-02-12
- ✨ 重构 JAVA_CODE_GUIDE.md，优化章节结构
- ✨ 新增接口层面规范（Controller、URL、参数验证、RO/VO）
- ✨ 新增分页规范（分页参数、常量定义、场景使用）
- ✨ 新增异常处理规范（BizException、错误码、统一处理）
- ✨ 新增 Dubbo 接口规范
- ✨ 新增 Controller 日志规范

---

## ♻️ 更新规则

### 使用 Submodule 时

```bash
cd .rules
git pull origin main
cd ..
git add .rules
git commit -m "chore: 更新开发规范"
```

### 直接引用时

Cursor 和 Codex 直接引用远程 URL 的方式会自动获取最新规则，无需手动更新。

---

## 📝 贡献指南

欢迎提交 Issue 或 Pull Request 来改进规则！

1. Fork 本仓库
2. 创建特性分支 (`git checkout -b feature/improve-rules`)
3. 提交更改 (`git commit -m 'docs: 改进 Java 命名规范'`)
4. 推送到分支 (`git push origin feature/improve-rules`)
5. 创建 Pull Request

---

## 📄 许可证

MIT License

## 🙋 问题反馈

如有问题或建议，请提交 [Issue](https://github.com/haixiangZhang/rule-repo/issues)
