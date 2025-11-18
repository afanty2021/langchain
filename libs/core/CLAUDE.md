[根目录](../../CLAUDE.md) > [libs](../) > **core**

# LangChain Core 模块

**变更记录 (Changelog):**
- 2025-11-18 12:40:07 - 初始化模块文档

## 模块职责

LangChain Core 是整个LangChain生态系统的核心抽象层，定义了基础接口、抽象类和通用协议。它提供了：

- 基础抽象类（BaseChatModel, BaseLLM, BaseEmbeddings等）
- 通用调用协议（Runnables）
- LangChain表达式语言（LCEL）
- 回调系统
- 文档处理接口
- 缓存和存储抽象

> **设计原则**: 模块化、轻量级、无第三方集成依赖

## 入口与启动

### 主要入口文件
- `langchain_core/__init__.py` - 包初始化和版本管理
- `langchain_core/runnables/` - 通用调用协议
- `langchain_core/chat_models/` - 聊天模型基类
- `langchain_core/language_models/` - LLM基类
- `langchain_core/embeddings/` - 嵌入模型基类

### 核心抽象类
```python
# 基础接口
from langchain_core.chat_models import BaseChatModel
from langchain_core.language_models import BaseLLM
from langchain_core.embeddings import BaseEmbeddings
from langchain_core.vectorstores import BaseVectorStore
from langchain_core.retrievers import BaseRetriever
from langchain_core.tools import BaseTool

# 可运行协议
from langchain_core.runnables import Runnable, RunnableParallel, RunnablePassthrough
```

## 对外接口

### 核心接口类型

#### 1. 模型接口
- **BaseChatModel**: 聊天模型基类，支持消息格式输入输出
- **BaseLLM**: 文本生成模型基类，支持字符串输入输出
- **BaseEmbeddings**: 嵌入模型基类，支持文本向量化

#### 2. 数据处理接口
- **BaseDocumentTransformer**: 文档转换器
- **BaseDocumentLoader**: 文档加载器
- **BaseTextSplitter**: 文本分割器

#### 3. 存储接口
- **BaseStore**: 键值存储
- **BaseVectorStore**: 向量数据库
- **BaseRetriever**: 检索器

#### 4. 可运行协议
- **Runnable**: 统一调用接口
- **RunnableSequence**: 顺序执行
- **RunnableParallel**: 并行执行
- **RunnableBranch**: 条件分支

## 关键依赖与配置

### 核心依赖
```toml
dependencies = [
    "langsmith>=0.3.45,<1.0.0",
    "tenacity!=8.4.0,>=8.1.0,<10.0.0",
    "jsonpatch>=1.33.0,<2.0.0",
    "PyYAML>=5.3.0,<7.0.0",
    "typing-extensions>=4.7.0,<5.0.0",
    "packaging>=23.2.0,<26.0.0",
    "pydantic>=2.7.4,<3.0.0",
]
```

### 开发依赖
- **测试**: pytest, pytest-asyncio, freezegun
- **类型检查**: mypy, types-pyyaml
- **代码质量**: ruff

### 本地开发源
```toml
[tool.uv.sources]
langchain-tests = { path = "../standard-tests" }
langchain-text-splitters = { path = "../text-splitters" }
```

## 数据模型

### 核心数据结构
- **Message**: 消息基类（HumanMessage, AIMessage, SystemMessage等）
- **Document**: 文档结构（page_content, metadata）
- **ChatResult**: 聊天结果
- **LLMResult**: LLM生成结果
- **PromptValue**: 提示词值

### 配置模型
- **RunnableConfig**: 运行时配置
- **CallbackConfig**: 回调配置

## 测试与质量

### 测试结构
```
tests/
├── unit_tests/           # 单元测试
│   ├── runnables/       # 可运行协议测试
│   ├── chat_models/     # 聊天模型测试
│   ├── embeddings/      # 嵌入模型测试
│   ├── callbacks/       # 回调系统测试
│   └── documents/       # 文档处理测试
├── integration_tests/   # 集成测试
└── benchmarks/         # 性能测试
```

### 质量工具
- **代码检查**: ruff（配置严格模式）
- **类型检查**: mypy（strict模式）
- **测试覆盖率**: coverage.py
- **文档**: Google风格docstring

## 常见问题 (FAQ)

### Q: 如何实现新的模型集成？
A: 继承对应的基类（BaseChatModel或BaseLLM），实现必需的抽象方法。

### Q: 什么是Runnable协议？
A: 统一的调用接口，支持流式处理、批处理、异步执行等特性。

### Q: 如何处理配置和回调？
A: 使用RunnableConfig传递运行时配置，包括回调处理器、标签等。

## 相关文件清单

### 核心模块
- `langchain_core/runnables/base.py` - 可运行协议基类
- `langchain_core/chat_models/base.py` - 聊天模型基类
- `langchain_core/language_models/base.py` - LLM基类
- `langchain_core/embeddings/embeddings.py` - 嵌入模型基类
- `langchain_core/callbacks/base.py` - 回调系统基类

### 数据结构
- `langchain_core/messages/base.py` - 消息基类
- `langchain_core/documents/base.py` - 文档基类
- `langchain_core/prompts/prompt.py` - 提示词相关

### 工具类
- `langchain_core/utils/` - 工具函数
- `langchain_core/load/` - 序列化/反序列化
- `langchain_core/_api/` - API版本控制

## 开发指南

### 添加新抽象
1. 在相应目录创建基类
2. 定义清晰的接口方法
3. 编写完整的单元测试
4. 添加类型注解和文档
5. 更新__init__.py导出

### 版本兼容性
- 公共接口变更需要主版本号升级
- 使用@deprecated标记过时API
- 提供迁移指南和示例

---

*此文档由初始化架构师生成于 2025-11-18 12:40:07*