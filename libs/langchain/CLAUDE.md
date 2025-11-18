[根目录](../../CLAUDE.md) > [libs](../) > **langchain**

# LangChain Classic 模块

**变更记录 (Changelog):**
- 2025-11-18 12:40:07 - 初始化模块文档

## 模块职责

LangChain Classic（原langchain-classic）是LangChain的经典实现包，包含：

- **传统Chain实现** - 原始的链式调用实现
- **社区功能重导出** - langchain-community功能的主要导出
- **索引API** - 文档索引和检索功能
- **废弃功能兼容** - 为向后兼容保留的废弃功能
- **工具包** - 预构建的工具和agent组合

> **使用建议**: 大多数情况下应使用主 `langchain` 包，此包主要用于兼容性

## 入口与启动

### 主要入口文件
- `langchain_classic/__init__.py` - 包初始化，包含导入警告机制
- `langchain_classic/chains/` - 传统链实现
- `langchain_classic/agents/` - Agent实现
- `langchain_classic/document_loaders/` - 文档加载器
- `langchain_classic/vectorstores/` - 向量存储
- `langchain_classic/tools/` - 工具实现

### 废弃导入处理
```python
# 自动废弃导入警告机制
def _warn_on_import(name: str, replacement: str | None = None) -> None:
    """Warn on import of deprecated module."""
    # 在交互式环境中不显示警告
    # 提供清晰的迁移路径
```

## 对外接口

### 1. 传统Chain接口
```python
# 经典链式调用
from langchain_classic.chains import LLMChain, ConversationChain, SequentialChain

# 使用示例
llm = OpenAI(temperature=0.9)
prompt = PromptTemplate(template="Hello {name}!", input_variables=["name"])
chain = LLMChain(llm=llm, prompt=prompt)
```

### 2. Agent接口
```python
# 传统Agent实现
from langchain_classic.agents import AgentExecutor, create_openai_functions_agent

# 工具包
from langchain_classic.agents.agent_toolkits import PythonAstREPLTool
```

### 3. 索引API
```python
# 文档索引功能
from langchain_classic.indexes import VectorstoreIndexCreator

# 创建索引
index = VectorstoreIndexCreator().from_loaders([loader])
```

## 关键依赖与配置

### 核心依赖
```toml
dependencies = [
    "langchain-core>=1.0.0,<2.0.0",
    "langchain-text-splitters>=1.0.0,<2.0.0",
    "langsmith>=0.1.17,<1.0.0",
    "pydantic>=2.7.4,<3.0.0",
    "SQLAlchemy>=1.4.0,<3.0.0",
    "requests>=2.0.0,<3.0.0",
    "PyYAML>=5.3.0,<7.0.0",
]
```

### 可选依赖
```toml
[project.optional-dependencies]
# 各种第三方集成的可选依赖
anthropic = ["langchain-anthropic"]
openai = ["langchain-openai"]
google-vertexai = ["langchain-google-vertexai"]
# ... 更多集成
```

### 开发依赖
- **测试**: pytest, pytest-cov, pytest-asyncio
- **类型检查**: mypy
- **代码质量**: ruff

## 数据模型

### 传统链模型
- **LLMChain**: 基础LLM链
- **ConversationChain**: 对话链
- **SequentialChain**: 顺序执行链
- **TransformChain**: 数据转换链

### Agent模型
- **AgentExecutor**: Agent执行器
- **AgentAction**: Agent动作
- **AgentFinish**: Agent结束状态

### 索引模型
- **Record**: 索引记录
- **Index**: 文档索引

## 测试与质量

### 测试结构
```
tests/
├── unit_tests/              # 单元测试
│   ├── chains/             # 链测试
│   ├── agents/             # Agent测试
│   ├── document_loaders/   # 文档加载器测试
│   └── vectorstores/       # 向量存储测试
├── integration_tests/       # 集成测试
│   └── examples/           # 示例测试
└── conftest.py             # 测试配置
```

### 兼容性测试
- 确保废弃导入的警告机制正常工作
- 验证向后兼容性
- 测试迁移路径的正确性

### 质量工具
- **代码检查**: ruff（配置相对宽松以兼容历史代码）
- **类型检查**: mypy
- **测试**: pytest

## 常见问题 (FAQ)

### Q: 应该使用langchain还是langchain-classic？
A: 新项目推荐使用主langchain包，langchain-classic主要用于现有项目的兼容性。

### Q: 如何从传统Chain迁移到LCEL？
A: 使用管道操作符 `|` 替代Chain类，参考迁移指南。

### Q: 废弃的导入如何处理？
A: 包会自动显示迁移建议，按照警告信息更新导入路径即可。

### Q: 索引API还在维护吗？
A: 索引API仍然支持，但推荐使用新的检索器模式。

## 相关文件清单

### 核心模块
- `langchain_classic/chains/` - 传统链实现
  - `llm_chain.py` - LLM链
  - `conversation_chain.py` - 对话链
  - `sequential_chain.py` - 顺序链
- `langchain_classic/agents/` - Agent实现
  - `agent.py` - Agent基类
  - `agent_executor.py` - 执行器
- `langchain_classic/indexes/` - 索引功能
  - `__init__.py` - 索引API
  - `vectorstore.py` - 向量存储索引

### 工具和集成
- `langchain_classic/tools/` - 工具实现
- `langchain_classic/document_loaders/` - 文档加载器
- `langchain_classic/vectorstores/` - 向量存储
- `langchain_classic/embeddings/` - 嵌入模型

### 兼容性
- `langchain_classic/_api/` - API兼容性处理
- `langchain_classic/imports.py` - 导入处理

## 开发指南

### 维护原则
1. **向后兼容** - 不破坏现有用户代码
2. **渐进迁移** - 提供清晰的迁移路径
3. **废弃警告** - 明确标识过时功能
4. **文档更新** - 及时更新使用建议

### 添加新功能
- 优先考虑在core或新包中实现
- 如需在此包中添加，确保兼容性
- 提供迁移到新模式的示例

### 处理废弃功能
1. 标记为废弃但保留功能
2. 添加明确的迁移警告
3. 在文档中说明替代方案
4. 计划未来版本的移除时间表

## 迁移指南

### 从传统Chain到LCEL
```python
# 旧方式
from langchain_classic.chains import LLMChain
chain = LLMChain(llm=llm, prompt=prompt)

# 新方式 (LCEL)
from langchain_core.prompts import PromptTemplate
from langchain_core.runnables import RunnablePassthrough

chain = prompt | llm
```

### 从Agent到Tools
```python
# 旧方式
from langchain_classic.agents import AgentExecutor
executor = AgentExecutor.from_agent_and_tools(agent, tools)

# 新方式
from langchain_core.agents import AgentExecutor
# 使用新的agent模式
```

---

*此文档由初始化架构师生成于 2025-11-18 12:40:07*