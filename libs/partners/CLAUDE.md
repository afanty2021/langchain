[根目录](../../CLAUDE.md) > [libs](../) > **partners**

# LangChain Partners 模块

**变更记录 (Changelog):**
- 2025-11-18 12:51:23 - 深度优化：增加集成包详细分析、版本策略、实现模式
- 2025-11-18 12:40:07 - 初始化模块文档

## 模块职责

LangChain Partners 模块包含官方维护的第三方服务集成，提供：

- **第一方集成** - 由LangChain团队直接维护的第三方服务集成
- **标准化接口** - 所有集成都实现core模块定义的标准接口
- **统一测试** - 使用standard-tests确保质量一致性
- **文档完整** - 每个集成都有完整的使用文档

> **覆盖范围**: 主要的LLM提供商、向量数据库、工具服务等

## 入口与启动

### 集成列表
当前包含的官方集成（16个）：

| 集成名称 | 版本 | 描述 | 入口模块 | 主要组件 | 维护状态 |
|---------|------|------|----------|----------|----------|
| **anthropic** | 1.0.0 | Anthropic Claude集成 | `langchain_anthropic` | ChatAnthropic, AnthropicLLM | ✅ 活跃 |
| **openai** | 1.0.1 | OpenAI GPT集成 | `langchain_openai` | ChatOpenAI, OpenAI, OpenAIEmbeddings | ✅ 活跃 |
| **ollama** | 1.0.0 | Ollama本地模型集成 | `langchain_ollama` | OllamaLLM, ChatOllama | ✅ 活跃 |
| **huggingface** | - | Hugging Face集成 | `langchain_huggingface` | HuggingFaceHub, HuggingFaceEmbeddings | ✅ 活跃 |
| **groq** | - | Groq快速推理集成 | `langchain_groq` | ChatGroq | ✅ 活跃 |
| **mistralai** | - | Mistral AI集成 | `langchain_mistralai` | ChatMistralAI | ✅ 活跃 |
| **deepseek** | - | DeepSeek集成 | `langchain_deepseek` | ChatDeepSeek | ✅ 活跃 |
| **xai** | - | xAI (Grok)集成 | `langchain_xai` | ChatXAI | ✅ 活跃 |
| **fireworks** | - | Fireworks AI集成 | `langchain_fireworks` | ChatFireworks | ✅ 活跃 |
| **perplexity** | - | Perplexity集成 | `langchain_perplexity` | ChatPerplexity | ✅ 活跃 |
| **nomic** | - | Nomic嵌入集成 | `langchain_nomic` | NomicEmbeddings | ✅ 活跃 |
| **chroma** | - | ChromaDB向量存储 | `langchain_chroma` | Chroma | ✅ 活跃 |
| **qdrant** | - | Qdrant向量存储 | `langchain_qdrant` | Qdrant | ✅ 活跃 |
| **exa** | - | Exa搜索引擎 | `langchain_exa` | ExaSearchRetriever | ✅ 活跃 |
| **prompty** | - | Prompty工具 | `langchain_prompty` | Prompty | ✅ 活跃 |

## 对外接口

### 标准接口实现
所有集成都实现core模块定义的标准接口：

#### 1. 聊天模型接口
```python
# 统一的聊天模型接口
from langchain_core.chat_models import BaseChatModel

# 使用示例
from langchain_openai import ChatOpenAI
from langchain_anthropic import ChatAnthropic

chat_model = ChatOpenAI(model="gpt-4")
response = chat_model.invoke("Hello!")
```

#### 2. 嵌入模型接口
```python
# 统一的嵌入接口
from langchain_core.embeddings import BaseEmbeddings

from langchain_openai import OpenAIEmbeddings
from langchain_nomic import NomicEmbeddings

embeddings = OpenAIEmbeddings()
vector = embeddings.embed_query("Hello world!")
```

#### 3. 向量存储接口
```python
# 统一的向量存储接口
from langchain_core.vectorstores import BaseVectorStore

from langchain_chroma import Chroma
from langchain_qdrant import Qdrant

vectorstore = Chroma(embedding_function=embeddings)
```

## 集成包深度分析

### 🤖 聊天模型集成详细分析

#### 1. OpenAI 集成 (langchain-openai)
**版本**: 1.0.1
**核心特性**:
- 完整的GPT模型支持 (GPT-4, GPT-3.5-turbo等)
- 高质量嵌入模型 (text-embedding-ada-002, text-embedding-3-small/large)
- 工具调用和函数支持
- 流式处理和异步支持
- Azure OpenAI 支持

**技术实现**:
```python
# 核心依赖
dependencies = [
    "langchain-core>=1.0.0,<2.0.0",
    "openai>=1.109.1,<3.0.0",  # 最新OpenAI SDK
    "tiktoken>=0.7.0,<1.0.0",  # GPT tokenizer
]

# 主要组件导出
__all__ = [
    "AzureChatOpenAI", "ChatOpenAI",
    "AzureOpenAI", "OpenAI",
    "AzureOpenAIEmbeddings", "OpenAIEmbeddings",
    "custom_tool"
]
```

**测试策略**: 完整的单元测试和集成测试，支持VCR录制
**质量保证**: ruff严格模式，mypy类型检查，快照测试

#### 2. Anthropic 集成 (langchain-anthropic)
**版本**: 1.0.0
**核心特性**:
- Claude系列模型支持 (Claude-3-opus, sonnet, haiku)
- 工具转换 (convert_to_anthropic_tool)
- 长上下文支持 (200K tokens)
- 流式处理和异步支持

**技术实现**:
```python
# 核心依赖
dependencies = [
    "anthropic>=0.69.0,<1.0.0",
    "langchain-core>=1.0.0,<2.0.0",
    "pydantic>=2.7.4,<3.0.0",
]

# 组件导出
__all__ = [
    "AnthropicLLM",
    "ChatAnthropic",
    "convert_to_anthropic_tool",
]
```

**特色功能**:
- 智能工具转换，自动将LangChain工具转换为Anthropic格式
- 支持Claude的消息格式转换
- 完善的错误处理和重试机制

#### 3. Ollama 集成 (langchain-ollama)
**版本**: 1.0.0
**核心特性**:
- 本地模型支持
- 模型验证 (validate_model_on_init)
- 轻量级依赖
- 支持多种模型格式

**技术优势**:
```python
# 特色功能示例
chat = ChatOllama(
    model="llama2",
    validate_model_on_init=True,  # 启动时验证模型
    temperature=0.7
)
```

**适用场景**:
- 本地开发环境
- 数据隐私要求高的场景
- 离线部署

### 🗄️ 向量存储集成分析

#### 1. Chroma 集成
**类型**: 向量数据库
**核心特性**:
- 本地和云端部署
- 元数据过滤
- 简单易用的API
- 适合小到中型应用

**实现模式**:
```python
from langchain_chroma import Chroma

# 本地向量存储
vectorstore = Chroma(
    embedding_function=embeddings,
    persist_directory="./chroma_db"
)

# 云端集成 (支持Chroma Cloud)
vectorstore = Chroma(
    embedding_function=embeddings,
    client_settings=chromadb_settings
)
```

#### 2. Qdrant 集成
**类型**: 分布式向量数据库
**核心特性**:
- 高性能向量搜索
- 分布式架构
- 云服务和自托管
- 适合大规模生产环境

**优势对比**:
| 特性 | Chroma | Qdrant |
|------|--------|--------|
| 部署复杂度 | 简单 | 中等 |
| 性能 | 中等 | 高 |
| 扩展性 | 有限 | 优秀 |
| 适用规模 | 小到中型 | 大规模 |

### 🛠️ 工具与服务集成分析

#### 1. Nomic 嵌入集成
**特色**: 高质量文本嵌入模型
**优势**:
- 专门优化的嵌入模型
- 支持多语言
- 性能优异

#### 2. Exa 搜索集成
**特色**: 实时网络搜索检索器
**应用场景**:
- 实时信息检索
- 知识库更新
- 问答系统增强

#### 3. Prompty 工具集成
**特色**: Prompt管理和模板化
**功能**:
- 提示词版本管理
- 模板化支持
- A/B测试友好

## 关键依赖与配置

### 共同依赖模式
所有集成包的共同依赖模式：
```toml
dependencies = [
    "langchain-core>=1.0.0,<2.0.0",
    "pydantic>=2.7.4,<3.0.0",
]
```

### 开发依赖标准
```toml
[dependency-groups]
dev = [
    "langchain-core",
    "langchain-tests",
]

test = [
    "pytest>=8.0.0,<9.0.0",
    "pytest-asyncio>=0.21.1,<1.0.0",
    "langchain-tests",
    "vcrpy>=7.0.0,<8.0.0",  # HTTP交互录制
]

lint = ["ruff>=0.13.1,<0.14.0"]
typing = ["mypy>=1.17.1,<2.0.0"]
```

### 本地开发源配置
```toml
[tool.uv.sources]
langchain-core = { path = "../core", editable = true }
langchain-tests = { path = "../standard-tests", editable = true }
langchain = { path = "../../langchain_v1", editable = true }
```

## 数据模型与配置模式

### 通用配置模式
所有集成包遵循统一的配置模式：

#### 1. API密钥配置
```python
# 优先级：直接传入 > 环境变量 > 默认值
chat_model = ChatOpenAI(
    api_key="your_key_here",  # 直接传入
    # 或使用环境变量 OPENAI_API_KEY
    model="gpt-4"
)
```

#### 2. 模型参数标准化
```python
# 标准参数名称
chat_model = ChatOpenAI(
    model="gpt-4",
    temperature=0.7,
    max_tokens=1000,
    top_p=1.0,
    frequency_penalty=0.0,
    presence_penalty=0.0
)
```

#### 3. 运行时配置
```python
# 超时和重试配置
chat_model = ChatOpenAI(
    timeout=30.0,
    max_retries=3,
    # 其他运行时配置...
)
```

## 测试策略与质量保证

### 统一测试标准
所有集成包都使用 `langchain-tests` 提供的标准测试：

#### 1. 测试基类继承
```python
# 标准测试基类
from langchain_tests.unit_tests import ChatModelUnitTests
from langchain_tests.integration_tests import ChatModelIntegrationTests

class TestOpenAIChat(ChatModelUnitTests):
    @property
    def chat_model_class(self):
        return ChatOpenAI

    @property
    def chat_model_params(self):
        return {"model": "gpt-3.5-turbo"}
```

#### 2. 测试分类和覆盖
- **单元测试**: 模拟API响应，无网络调用
- **集成测试**: 真实API调用，需要API密钥
- **性能测试**: 响应时间和吞吐量测试
- **快照测试**: 确保API响应格式一致性

#### 3. VCR.py 集成
```python
# HTTP交互录制，避免真实API调用
@vcr.use_cassette("tests/fixtures/chat_openai.yaml")
def test_chat_openai_integration():
    chat = ChatOpenAI()
    response = chat.invoke("Hello")
    assert response.content is not None
```

### CI/CD集成模式
```yaml
# GitHub Actions中的标准测试流程
- name: Run unit tests
  run: make test

- name: Run integration tests (if secrets available)
  if: env.OPENAI_API_KEY != ''
  run: make integration_test

- name: Run performance benchmarks
  run: make benchmark
```

## 版本管理与发布策略

### 语义化版本控制
所有集成包遵循严格的语义化版本：
- **主版本号**: 不兼容的API变更
- **次版本号**: 向后兼容的功能新增
- **修订版本号**: 向后兼容的问题修正

### 版本协调策略
```toml
# 确保与core模块兼容
dependencies = [
    "langchain-core>=1.0.0,<2.0.0",
]

# 可选依赖与主版本兼容
[project.optional-dependencies]
test = ["langchain-tests>=1.0.0,<2.0.0"]
```

### 发布流程
1. **开发完成**: 所有测试通过，文档更新
2. **版本标记**: 更新pyproject.toml中的版本号
3. **CHANGELOG更新**: 记录所有变更
4. **自动发布**: GitHub Actions自动构建和发布

## 开发指南与最佳实践

### 新集成开发流程
1. **创建模板**: 使用CLI生成基础代码
   ```bash
   langchain integration new --name my-provider
   ```

2. **实现接口**: 继承core模块的基类
   ```python
   from langchain_core.chat_models import BaseChatModel

   class ChatMyProvider(BaseChatModel):
       # 实现必需的抽象方法
       pass
   ```

3. **添加测试**: 实现标准测试用例
   ```python
   from langchain_tests.unit_tests import ChatModelUnitTests

   class TestMyProviderChat(ChatModelUnitTests):
       @property
       def chat_model_class(self):
           return ChatMyProvider
   ```

4. **编写文档**: 包括安装、配置、使用示例

5. **质量检查**: 运行lint, format, test
   ```bash
   make lint
   make format
   make test
   ```

### 代码规范要求
- **类型注解**: 强制要求完整的类型注解
- **文档字符串**: Google风格docstring
- **异常处理**: 完善的错误处理和重试机制
- **异步支持**: 支持异步操作和流式处理
- **配置管理**: 标准化的配置参数管理

### 性能优化原则
- **懒加载**: 延迟初始化大型资源
- **连接池**: 复用HTTP连接
- **缓存策略**: 合理的缓存机制
- **并发控制**: 适当的并发限制

## 常见问题与解决方案

### Q: 如何添加新的集成？
A: 使用CLI工具创建模板：
```bash
langchain integration new --name provider-name
cd partners/provider-name
# 实现核心接口和测试
```

### Q: API密钥如何安全配置？
A: 推荐使用环境变量，避免硬编码：
```bash
export OPENAI_API_KEY=your_key_here
export ANTHROPIC_API_KEY=your_key_here
```

### Q: 如何处理API限制和错误？
A: 所有集成都内置重试机制和错误处理：
```python
chat_model = ChatOpenAI(
    max_retries=3,
    timeout=30.0,
    # 其他错误处理配置...
)
```

### Q: 如何确保测试质量？
A: 继承标准测试基类，确保一致性：
```python
class MyProviderTests(ChatModelUnitTests):
    # 必须实现的属性
    @property
    def chat_model_class(self):
        return MyProvider
```

### Q: 版本兼容性如何处理？
A: 使用明确的版本约束：
```toml
dependencies = [
    "langchain-core>=1.0.0,<2.0.0",
    "provider-sdk>=1.0.0,<2.0.0",
]
```

## 相关文件清单

### 每个集成的标准结构
```
partners/provider_name/
├── langchain_provider_name/
│   ├── __init__.py          # 主要导出
│   ├── chat_models.py       # 聊天模型实现
│   ├── llms.py             # LLM实现（如适用）
│   ├── embeddings.py       # 嵌入模型实现（如适用）
│   ├── vectorstores.py     # 向量存储实现（如适用）
│   ├── retrievers.py       # 检索器实现（如适用）
│   ├── tools.py            # 工具实现（如适用）
│   ├── utils.py            # 工具函数
│   └── _version.py         # 版本信息
├── tests/
│   ├── unit_tests/         # 单元测试
│   │   ├── __init__.py
│   │   ├── test_chat_models.py
│   │   └── conftest.py
│   └── integration_tests/  # 集成测试
│       ├── __init__.py
│       └── test_chat_models.py
├── README.md               # 使用文档
├── pyproject.toml          # 项目配置
├── Makefile               # 构建脚本
└── CHANGELOG.md           # 变更日志
```

### 典型实现示例
```python
# langchain_openai/chat_models/base.py
from typing import Any, Dict, Optional, List
from langchain_core.chat_models import BaseChatModel
from langchain_core.messages import BaseMessage, HumanMessage, AIMessage
from langchain_core.callbacks import CallbackManagerForLLMRun

class ChatOpenAI(BaseChatModel):
    """OpenAI聊天模型实现"""

    def __init__(
        self,
        model: str = "gpt-3.5-turbo",
        api_key: Optional[str] = None,
        temperature: float = 0.7,
        max_tokens: Optional[int] = None,
        **kwargs: Any,
    ):
        super().__init__(**kwargs)
        self.model = model
        self.api_key = api_key
        self.temperature = temperature
        self.max_tokens = max_tokens

    def _generate(
        self,
        messages: List[BaseMessage],
        stop: Optional[List[str]] = None,
        run_manager: Optional[CallbackManagerForLLMRun] = None,
        **kwargs: Any,
    ) -> ChatResult:
        # 实现API调用逻辑
        pass

    async def _agenerate(
        self,
        messages: List[BaseMessage],
        stop: Optional[List[str]] = None,
        run_manager: Optional[CallbackManagerForLLMRun] = None,
        **kwargs: Any,
    ) -> ChatResult:
        # 实现异步API调用逻辑
        pass
```

### 测试实现示例
```python
# tests/unit_tests/test_chat_models.py
from langchain_tests.unit_tests import ChatModelUnitTests
from langchain_openai import ChatOpenAI

class TestOpenAIChat(ChatModelUnitTests):
    """OpenAI聊天模型标准测试"""

    @property
    def chat_model_class(self):
        return ChatOpenAI

    @property
    def chat_model_params(self):
        return {
            "model": "gpt-3.5-turbo",
            "api_key": "test-key",
            "max_tokens": 10,
        }

    @property
    def supported_features(self):
        """声明支持的功能特性"""
        return {
            "tool-calling": True,
            "streaming": True,
            "images": True,
        }
```

## 质量指标与监控

### 质量标准
- **测试覆盖率**: >80%
- **类型注解覆盖率**: >95%
- **文档覆盖度**: 100% (所有公共接口)
- **性能基准**: 响应时间<1000ms (P95)

### 监控指标
- API响应时间
- 错误率和重试率
- 内存使用情况
- 并发处理能力

## 未来发展方向

### 短期目标 (3-6个月)
1. **新集成添加**:
   - 更多LLM提供商集成
   - 新的向量数据库支持
   - 工具生态扩展

2. **性能优化**:
   - 连接池优化
   - 缓存策略改进
   - 并发性能提升

### 中期目标 (6-12个月)
1. **功能增强**:
   - 多模态支持
   - 流式处理优化
   - 批处理能力

2. **开发者体验**:
   - 更好的错误提示
   - 调试工具集成
   - 性能分析工具

### 长期愿景
1. **生态建设**:
   - 社区贡献集成支持
   - 插件化架构
   - 标准化推广

2. **技术创新**:
   - AI辅助集成开发
   - 自动化测试生成
   - 智能性能调优

---

*此文档由深度优化生成于 2025-11-18 12:51:23*