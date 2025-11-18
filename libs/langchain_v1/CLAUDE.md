[根目录](../../CLAUDE.md) > [libs](../) > **langchain_v1**

**变更记录 (Changelog):**
- 2025-11-18 12:46:56 - 增量更新：生成模块文档

## 模块职责

`langchain` (v1) 是 LangChain 的主要用户入口包，提供了完整的 LLM 应用开发工具链。它整合了核心抽象、智能体框架、工具系统等，同时保持向后兼容性。该模块专注于生产就绪的智能体开发，特别是与 LangGraph 的集成。

## 入口与启动

- **主入口**: `langchain/__init__.py`
- **包名**: `langchain`
- **版本**: 1.0.2
- **Python要求**: >=3.10.0

### 核心组件
- **智能体系统**: `langchain.agents.*`
- **聊天模型**: `langchain.chat_models.*`
- **嵌入模型**: `langchain.embeddings.*`
- **工具系统**: `langchain.tools.*`
- **消息系统**: `langchain.messages.*`
- **限流器**: `langchain.rate_limiters.*`

## 对外接口

### 智能体系统
```python
from langchain.agents import (
    create_agent,           # 智能体创建函数
    AgentExecutor,         # 智能体执行器
    tool,                  # 工具装饰器
)
from langchain.agents.middleware import (
    # 各种中间件：重试、限流、总结等
)
```

### 兼容层
```python
from langchain.chat_models import BaseChatModel  # 兼容性别名
from langchain.embeddings import BaseEmbeddings  # 兼容性别名
```

### 智能体中间件系统
- **执行控制**: `tool_call_limit`, `model_call_limit`
- **错误处理**: `tool_retry`, `model_fallback`
- **安全**: `pii`, `shell_tool`
- **优化**: `summarization`, `todo`
- **交互**: `human_in_the_loop`
- **文件操作**: `file_search`

## 关键依赖与配置

### 核心依赖
- `langchain-core>=1.0.0,<2.0.0` - 核心抽象
- `langgraph>=1.0.0,<1.1.0` - 智能体编排框架
- `pydantic>=2.7.4,<3.0.0` - 数据验证

### 可选集成（按需）
```toml
[project.optional-dependencies]
community = ["langchain-community"]
anthropic = ["langchain-anthropic"]
openai = ["langchain-openai"]
# ... 其他15+个集成
```

### 测试依赖
- `langchain-tests` - 标准测试框架
- `langchain-text-splitters` - 文本分割工具
- `pytest-*` - 测试工具集

## 架构设计

### 智能体中间件架构
```python
中间件系统
├── 执行控制中间件
│   ├── tool_call_limit     # 工具调用限制
│   ├── model_call_limit    # 模型调用限制
│   └── tool_emulator       # 工具模拟
├── 错误处理中间件
│   ├── tool_retry          # 工具重试
│   └── model_fallback      # 模型回退
├── 安全中间件
│   ├── pii                 # PII数据脱敏
│   └── shell_tool          # Shell工具安全
├── 优化中间件
│   ├── summarization       # 对话总结
│   └── todo                # 任务管理
└── 交互中间件
    ├── human_in_the_loop   # 人工干预
    └── file_search         # 文件搜索
```

### 兼容性策略
- **接口保持**: 保持与旧版本的API兼容
- **渐进迁移**: 支持逐步迁移到新架构
- **弃用警告**: 明确标识过时功能
- **向后兼容**: 确保现有代码继续工作

## 测试策略

### 测试组织
```
tests/
├── unit_tests/                    # 单元测试
│   ├── agents/                   # 智能体测试（大量）
│   │   └── middleware/           # 中间件测试
│   ├── chat_models/              # 聊天模型测试
│   ├── embeddings/               # 嵌入模型测试
│   └── tools/                    # 工具测试
└── integration_tests/            # 集成测试
    ├── agents/                   # 智能体集成测试
    ├── chat_models/              # 聊天模型集成测试
    └── embeddings/               # 嵌入模型集成测试
```

### 测试重点
- **中间件测试**: 15+种中间件的独立和组合测试
- **智能体测试**: 复杂智能体行为的端到端测试
- **兼容性测试**: 确保向后兼容性
- **集成测试**: 与各种LLM提供商的集成

## 质量保证

### 代码质量
- **ruff**: 代码检查和格式化
- **mypy**: 类型检查（部分模块宽松模式）
- **测试覆盖**: 全面的单元和集成测试

### 特殊处理
- **代理模块**: `langchain/agents/*` 使用宽松的检查规则
- **测试模块**: 测试代码使用专门的规则集
- **兼容代码**: 允许部分类型推断的放宽

## 使用模式

### 智能体创建
```python
from langchain.agents import create_agent
from langchain.agents.middleware import tool_retry, human_in_the_loop

# 创建带中间件的智能体
agent = create_agent(
    model,
    tools,
    middleware=[tool_retry(), human_in_the_loop()]
)
```

### 工具定义
```python
from langchain.tools import tool

@tool
def my_tool(input: str) -> str:
    """定义一个工具"""
    return f"处理: {input}"
```

## 常见问题 (FAQ)

**Q: langchain v1 与 langchain-core 的关系？**
A: v1是用户层接口，整合了core和其他组件，提供完整的开发体验。

**Q: 如何使用中间件系统？**
A: 通过`create_agent`函数的`middleware`参数，或使用装饰器语法。

**Q: 迁移到新版本需要注意什么？**
A: 查看弃用警告，逐步替换过时API，利用新的中间件系统。

**Q: 如何开发自定义中间件？**
A: 继承适当的中间件基类，实现必需的方法，参考现有中间件实现。

## 相关文件清单

### 核心模块
- `langchain/__init__.py` - 包入口和版本信息
- `langchain/agents/` - 智能体系统
  - `agents/factory.py` - 智能体工厂
  - `agents/middleware/` - 中间件系统（13个文件）
  - `agents/structured_output.py` - 结构化输出
- `langchain/chat_models/` - 聊天模型兼容层
- `langchain/embeddings/` - 嵌入模型兼容层
- `langchain/tools/` - 工具系统
- `langchain/messages/` - 消息系统
- `langchain/rate_limiters/` - 限流器

### 测试套件
- `tests/unit_tests/agents/` - 智能体单元测试（大量文件）
- `tests/integration_tests/` - 集成测试
- `tests/unit_tests/chat_models/` - 聊天模型测试
- `tests/unit_tests/embeddings/` - 嵌入模型测试

### 开发工具
- `scripts/check_imports.py` - 导入检查脚本
- `pyproject.toml` - 项目配置（详细的规则配置）

### 配置文件
- `pyproject.toml` - 项目配置（包含详细的ruff、mypy规则）
- `README.md` - 使用说明

---

*此文档由增量更新生成于 2025-11-18 12:46:56*