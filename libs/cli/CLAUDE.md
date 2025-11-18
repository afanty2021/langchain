[根目录](../../CLAUDE.md) > [libs](../) > **cli**

# LangChain CLI 模块

**变更记录 (Changelog):**
- 2025-11-18 12:40:07 - 初始化模块文档

## 模块职责

LangChain CLI 是开发者工具链，提供以下功能：

- **项目脚手架** - 快速创建新的LangChain项目和集成
- **集成模板** - 为第三方服务集成提供标准模板
- **迁移工具** - 帮助代码从旧版本迁移到新版本
- **开发辅助** - 提供开发、测试、部署的辅助命令

> **设计目标**: 简化LangChain生态系统的开发和集成流程

## 入口与启动

### 主入口文件
- `langchain_cli/cli.py` - CLI主入口，使用typer框架
- `langchain_cli/__init__.py` - 包初始化

### 主要命令结构
```bash
langchain-cli [OPTIONS] COMMAND [ARGS]...

Commands:
  template      模板相关操作
  app           应用程序管理
  integration   集成开发工具
  migrate       代码迁移工具
```

## 对外接口

### 1. 模板命令 (`template`)
```bash
# 创建新项目包
langchain-cli template package --name my-package

# 创建新应用
langchain-cli template app --name my-app
```

### 2. 应用命令 (`app`)
```bash
# 应用相关管理操作
langchain-cli app [SUBCOMMAND]
```

### 3. 集成命令 (`integration`)
```bash
# 创建新的第三方集成
langchain-cli integration new --name provider-name

# 生成集成文档
langchain-cli integration create-doc --name provider-name
```

### 4. 迁移命令 (`migrate`)
```bash
# 自动迁移代码
langchain-cli migrate [OPTIONS] SOURCE_FILES...
```

## 关键依赖与配置

### 核心依赖
```toml
dependencies = [
    "typer[all]>=0.12.0,<1.0.0",
    "langchain-core",
    "rich>=13.0.0,<14.0.0",
    "pydantic>=2.0.0,<3.0.0",
]
```

### 开发依赖
- **测试**: pytest, pytest-asyncio
- **类型检查**: mypy
- **代码质量**: ruff

### 工具依赖
- **模板引擎**: Jinja2
- **Git操作**: GitPython
- **文件处理**: pathspec

## 数据模型

### 配置模型
- **ProjectConfig**: 项目配置
- **IntegrationConfig**: 集成配置
- **MigrationConfig**: 迁移配置

### 模板模型
- **PackageTemplate**: 包模板结构
- **AppTemplate**: 应用模板结构
- **IntegrationTemplate**: 集成模板结构

## 测试与质量

### 测试结构
```
tests/
├── unit_tests/              # 单元测试
│   ├── migrate/            # 迁移工具测试
│   │   ├── generate/       # 代码生成测试
│   │   └── cli_runner/     # CLI运行器测试
│   └── utils/              # 工具函数测试
└── integration_tests/      # 集成测试
    └── test_compile.py     # 编译测试
```

### E2E测试
CLI模块包含完整的端到端测试，模拟真实开发流程：
```bash
# 在Makefile中定义的e2e测试
_e2e_test:
    # 创建集成测试项目
    # 运行完整的lint/test流程
    # 验证生成的代码质量
```

### 质量工具
- **代码检查**: ruff
- **类型检查**: mypy
- **测试**: pytest
- **格式化**: ruff format

## 常见问题 (FAQ)

### Q: 如何创建新的第三方集成？
A: 使用 `langchain-cli integration new --name provider-name` 命令，会生成完整的集成模板。

### Q: 迁移工具支持哪些类型的代码迁移？
A: 支持导入路径更改、API变更、废弃功能替换等多种迁移场景。

### Q: 如何自定义模板？
A: 可以修改 `langchain_cli/*/template/` 目录下的模板文件。

## 相关文件清单

### 核心CLI文件
- `langchain_cli/cli.py` - 主入口文件
- `langchain_cli/namespaces/` - 命名空间模块
  - `app.py` - 应用命令
  - `integration.py` - 集成命令
  - `template.py` - 模板命令
  - `migrate/` - 迁移工具

### 模板文件
- `langchain_cli/package_template/` - 包模板
- `langchain_cli/project_template/` - 项目模板
- `langchain_cli/integration_template/` - 集成模板

### 工具模块
- `langchain_cli/utils/` - 工具函数
  - `packages.py` - 包管理
  - `git.py` - Git操作
  - `pyproject.py` - 项目配置
  - `find_replace.py` - 文本处理

### 迁移工具
- `langchain_cli/namespaces/migrate/` - 迁移实现
  - `generate/` - 代码生成
    - `generic.py` - 通用迁移
    - `partner.py` - 伙伴集成迁移
    - `grit.py` - Grit引擎迁移

## 开发指南

### 添加新命令
1. 在 `namespaces/` 目录创建新模块
2. 实现typer命令函数
3. 在主CLI中注册命令
4. 添加对应测试

### 更新模板
1. 修改对应模板目录下的文件
2. 更新模板变量和逻辑
3. 测试生成的代码质量
4. 更新文档

### 扩展迁移功能
1. 在 `migrate/generate/` 目录添加新迁移器
2. 实现迁移逻辑
3. 添加测试用例
4. 更新迁移配置

## 开发工作流

### 本地开发
```bash
# 安装开发依赖
uv sync --group dev

# 运行测试
make test

# 代码检查
make lint format

# 运行E2E测试
make _e2e_test
```

### 发布流程
1. 更新版本号
2. 更新CHANGELOG
3. 运行完整测试套件
4. 提交并创建PR
5. 合并后自动发布

---

*此文档由初始化架构师生成于 2025-11-18 12:40:07*