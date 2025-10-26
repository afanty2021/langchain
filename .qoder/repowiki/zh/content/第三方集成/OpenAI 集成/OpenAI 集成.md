# OpenAI 集成

<cite>
**本文档中引用的文件**  
- [__init__.py](file://libs/partners/openai/langchain_openai/__init__.py)
- [base.py](file://libs/partners/openai/langchain_openai/chat_models/base.py)
- [base.py](file://libs/partners/openai/langchain_openai/llms/base.py)
- [base.py](file://libs/partners/openai/langchain_openai/embeddings/base.py)
- [azure.py](file://libs/partners/openai/langchain_openai/chat_models/azure.py)
- [azure.py](file://libs/partners/openai/langchain_openai/embeddings/azure.py)
- [custom_tool.py](file://libs/partners/openai/langchain_openai/tools/custom_tool.py)
- [pyproject.toml](file://libs/partners/openai/pyproject.toml)
</cite>

## 目录
1. [简介](#简介)
2. [核心组件](#核心组件)
3. [功能详解](#功能详解)
4. [高级功能](#高级功能)
5. [最佳实践](#最佳实践)
6. [错误处理与速率限制](#错误处理与速率限制)

## 简介
LangChain与OpenAI的集成是LangChain生态系统中最核心和最常用的集成之一。该集成提供了对OpenAI各种模型的全面支持，包括GPT系列的聊天模型、基础LLM模型以及text-embedding模型。通过`langchain-openai`包，开发者可以轻松地将OpenAI的强大功能集成到LangChain的应用程序中，实现复杂的语言处理任务。

**Section sources**
- [__init__.py](file://libs/partners/openai/langchain_openai/__init__.py)

## 核心组件
OpenAI集成主要包含三个核心组件：聊天模型（ChatOpenAI）、基础LLM模型（OpenAI）和嵌入模型（OpenAIEmbeddings）。这些组件都严格遵循`langchain_core`的接口规范，确保了与其他LangChain组件的无缝集成。

### 聊天模型 (ChatOpenAI)
`ChatOpenAI`是OpenAI聊天模型的主要接口，支持GPT-3.5、GPT-4等系列模型。它提供了丰富的初始化参数，包括模型名称、温度、最大生成令牌数等。

**Section sources**
- [base.py](file://libs/partners/openai/langchain_openai/chat_models/base.py)

### 基础LLM模型 (OpenAI)
`OpenAI`类提供了对基础LLM模型的访问，适用于传统的文本生成任务。它支持多种参数配置，如采样温度、top_p、频率惩罚等。

**Section sources**
- [base.py](file://libs/partners/openai/langchain_openai/llms/base.py)

### 嵌入模型 (OpenAIEmbeddings)
`OpenAIEmbeddings`类用于生成文本的嵌入向量，支持`text-embedding-ada-002`和`text-embedding-3`系列模型。它允许指定嵌入的维度，以适应不同的应用场景。

**Section sources**
- [base.py](file://libs/partners/openai/langchain_openai/embeddings/base.py)

## 功能详解
### 配置与实例化
要使用OpenAI集成，首先需要安装`langchain-openai`包并设置API密钥：

```bash
pip install -U langchain-openai
export OPENAI_API_KEY="your-api-key"
```

然后可以实例化不同的模型：

```python
from langchain_openai import ChatOpenAI, OpenAI, OpenAIEmbeddings

# 实例化聊天模型
chat_model = ChatOpenAI(model="gpt-4", temperature=0.7)

# 实例化基础LLM模型
llm_model = OpenAI(model_name="gpt-3.5-turbo-instruct", temperature=0.7)

# 实例化嵌入模型
embeddings = OpenAIEmbeddings(model="text-embedding-3-large")
```

### 集成到LangChain组件
这些模型可以无缝集成到LangChain的链、代理和检索器中。例如，可以将`ChatOpenAI`模型与提示模板结合使用：

```python
from langchain.prompts import ChatPromptTemplate
from langchain_core.runnables import RunnablePassthrough

prompt = ChatPromptTemplate.from_messages([
    ("system", "你是一个有帮助的助手"),
    ("human", "{input}")
])

chain = prompt | chat_model
response = chain.invoke({"input": "你好，世界！"})
```

**Section sources**
- [base.py](file://libs/partners/openai/langchain_openai/chat_models/base.py)
- [base.py](file://libs/partners/openai/langchain_openai/llms/base.py)
- [base.py](file://libs/partners/openai/langchain_openai/embeddings/base.py)

## 高级功能
### 函数调用 (Function Calling)
`ChatOpenAI`支持函数调用功能，允许模型调用预定义的工具。这可以通过`bind_tools`方法实现：

```python
from pydantic import BaseModel, Field

class GetWeather(BaseModel):
    """获取指定位置的当前天气"""
    location: str = Field(..., description="城市和州，例如：旧金山，加利福尼亚")

model_with_tools = chat_model.bind_tools([GetWeather])
ai_msg = model_with_tools.invoke("洛杉矶今天的天气如何？")
```

### 流式传输 (Streaming)
所有模型都支持流式传输，可以实时获取生成的文本：

```python
for chunk in chat_model.stream("你好"):
    print(chunk.content, end="")
```

### 备用模型 (Fallbacks)
可以配置备用模型，当主模型失败时自动切换到备用模型：

```python
from langchain_core.runnables import RunnableWithFallbacks

fallback_model = ChatAnthropic(model="claude-3-sonnet")
model_with_fallbacks = chat_model.with_fallbacks([fallback_model])
```

### Azure集成
对于Azure OpenAI服务，提供了专门的`AzureChatOpenAI`和`AzureOpenAIEmbeddings`类，支持Azure特有的配置参数：

```python
from langchain_openai import AzureChatOpenAI

azure_model = AzureChatOpenAI(
    azure_deployment="your-deployment",
    api_version="2024-05-01-preview"
)
```

**Section sources**
- [base.py](file://libs/partners/openai/langchain_openai/chat_models/base.py)
- [azure.py](file://libs/partners/openai/langchain_openai/chat_models/azure.py)
- [azure.py](file://libs/partners/openai/langchain_openai/embeddings/azure.py)

## 最佳实践
### 成本管理
- 使用`max_tokens`参数限制生成的令牌数
- 对于简单任务，选择成本较低的模型如`gpt-3.5-turbo`
- 使用`text-embedding-3-small`而不是`text-embedding-3-large`进行嵌入

### 配置管理
- 通过环境变量管理API密钥和基础URL
- 使用`model_kwargs`传递OpenAI API的额外参数
- 对于OpenAI兼容的API，使用`extra_body`参数

```python
model = ChatOpenAI(
    base_url="http://localhost:1234/v1",
    api_key="lm-studio",
    model="mlx-community/QwQ-32B-4bit",
    extra_body={"ttl": 300}
)
```

**Section sources**
- [base.py](file://libs/partners/openai/langchain_openai/chat_models/base.py)
- [pyproject.toml](file://libs/partners/openai/pyproject.toml)

## 错误处理与速率限制
### 错误处理
OpenAI集成提供了完善的错误处理机制，包括：

- 自动重试机制（通过`max_retries`参数配置）
- 详细的错误信息和响应元数据
- 对API限制的优雅处理

### 速率限制
可以使用`InMemoryRateLimiter`来控制请求速率：

```python
from langchain_core.rate_limiters import InMemoryRateLimiter

rate_limiter = InMemoryRateLimiter(
    requests_per_second=10,
    check_every_n_seconds=0.1,
    max_bucket_size=10
)

model = ChatOpenAI(rate_limiter=rate_limiter)
```

此外，还可以使用`ModelCallLimitMiddleware`和`ToolCallLimitMiddleware`来限制模型调用和工具调用的次数。

**Section sources**
- [base.py](file://libs/partners/openai/langchain_openai/chat_models/base.py)
- [base.py](file://libs/partners/openai/langchain_openai/llms/base.py)