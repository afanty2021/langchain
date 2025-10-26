# Agent类型

<cite>
**本文档中引用的文件**  
- [__init__.py](file://libs/langchain/langchain_classic/agents/__init__.py)
- [agent_types.py](file://libs/langchain/langchain_classic/agents/agent_types.py)
- [mrkl/base.py](file://libs/langchain/langchain_classic/agents/mrkl/base.py)
- [react/base.py](file://libs/langchain/langchain_classic/agents/react/base.py)
- [structured_chat/base.py](file://libs/langchain/langchain_classic/agents/structured_chat/base.py)
- [openai_functions_agent/base.py](file://libs/langchain/langchain_classic/agents/openai_functions_agent/base.py)
- [initialize.py](file://libs/langchain/langchain_classic/agents/initialize.py)
- [types.py](file://libs/langchain/langchain_classic/agents/types.py)
- [mrkl/prompt.py](file://libs/langchain/langchain_classic/agents/mrkl/prompt.py)
- [react/wiki_prompt.py](file://libs/langchain/langchain_classic/agents/react/wiki_prompt.py)
- [structured_chat/prompt.py](file://libs/langchain/langchain_classic/agents/structured_chat/prompt.py)
</cite>

## 目录
1. [引言](#引言)
2. [MRKL Agent](#mrkl-agent)
3. [ReAct Agent](#react-agent)
4. [Structured Chat Agent](#structured-chat-agent)
5. [OpenAI Functions Agent](#openai-functions-agent)
6. [Agent类型选择建议](#agent类型选择建议)
7. [总结](#总结)

## 引言
LangChain中的Agent是利用大语言模型（LLM）来决定行动序列的智能体。与在链（Chains）中硬编码行动序列不同，Agent使用LLM作为推理引擎，动态选择和执行工具（Tools）。本文档深入分析四种核心Agent类型：MRKL、ReAct、Structured Chat和OpenAI Functions Agent，详细阐述其设计原理、适用场景、Prompt模板结构、决策逻辑和性能特点。

## MRKL Agent

MRKL（Model-Reasoning-Knowledge-Loop）Agent是一种零样本（zero-shot）Agent，其设计灵感来源于arXiv:2205.00445论文。它通过一个“思考-行动-观察”的循环来解决复杂问题，特别擅长处理数学和符号推理任务。

### 核心设计原理
MRKL Agent的核心是`ZeroShotAgent`类，它继承自`Agent`基类。其工作流程如下：
1.  **思考（Thought）**：Agent根据当前状态和历史记录，生成一个内部思考。
2.  **行动（Action）**：基于思考，决定调用哪个工具（Tool）以及传递什么参数。
3.  **观察（Observation）**：执行工具后，将结果作为观察返回给Agent。
4.  **循环**：将观察结果纳入上下文，重复上述过程，直到得出最终答案。

该Agent通过`MRKLOutputParser`解析LLM的输出，确保其遵循预定义的格式指令。

### Prompt模板结构
MRKL Agent的Prompt由三部分组成：
- **前缀（PREFIX）**：设定Agent的角色和基本行为准则。
- **工具描述**：将可用工具的名称和描述渲染成文本列表。
- **后缀（SUFFIX）**：包含格式化指令（FORMAT_INSTRUCTIONS），明确指导Agent如何输出。

```python
PREFIX = """Answer the following questions as best you can. You have access to the following tools:"""
FORMAT_INSTRUCTIONS = """Use the following format:

Question: the input question you must answer
Thought: you should always think about what to do
Action: the action to take, should be one of [{tool_names}]
Action Input: the input to the action
Observation: the result of the action
... (this Thought/Action/Action Input/Observation can repeat N times)
Thought: I now know the final answer
Final Answer: the final answer to the original input question"""
SUFFIX = "Begin! {input}"
```

### 代码实现示例
```python
from langchain.agents import initialize_agent, AgentType
from langchain_community.tools import Tool
from langchain_community.llms import OpenAI

# 定义工具
def calculator_tool(expression: str) -> str:
    # 执行数学计算
    return str(eval(expression))

tools = [
    Tool(
        name="Calculator",
        func=calculator_tool,
        description="用于执行数学计算"
    )
]

llm = OpenAI(temperature=0)
# 初始化MRKL Agent
agent = initialize_agent(
    tools, 
    llm, 
    agent=AgentType.ZERO_SHOT_REACT_DESCRIPTION, 
    verbose=True
)

# 执行任务
agent.run("3的平方根乘以5等于多少？")
```

**Section sources**
- [mrkl/base.py](file://libs/langchain/langchain_classic/agents/mrkl/base.py#L1-L217)
- [mrkl/prompt.py](file://libs/langchain/langchain_classic/agents/mrkl/prompt.py#L1-L10)

## ReAct Agent

ReAct Agent实现了arXiv:2210.03629论文中的“推理-行动”（Reasoning-Acting）框架。它通过显式的推理步骤来指导行动，特别适用于需要从外部知识库（如维基百科）中检索信息的问答任务。

### 核心设计原理
ReAct Agent的核心是`ReActDocstoreAgent`类。它与MRKL Agent类似，但对工具的使用有更严格的约束。它通常只配备两个特定工具：
- **Search**：用于在文档库中搜索相关主题。
- **Lookup**：用于在已检索到的文档中查找特定关键词。

这种设计强制Agent先通过`Search`获取上下文，再通过`Lookup`提取精确信息，从而减少幻觉并提高答案的准确性。

### Prompt模板结构
ReAct Agent使用一个更具体的Prompt，例如`WIKI_PROMPT`，它直接针对维基百科风格的文档库进行了优化。

```python
WIKI_PROMPT = PromptTemplate.from_template(
    """Answer the following questions as best you can. You have access to a search engine about Wikipedia. Use the search engine to look up information.

Question: {input}
Thought: {agent_scratchpad}
"""
)
```

### 代码实现示例
```python
from langchain.agents import create_react_agent, AgentExecutor
from langchain_community.tools import DuckDuckGoSearchRun
from langchain_community.llms import OpenAI

# 定义工具
search_tool = DuckDuckGoSearchRun()

tools = [search_tool]
llm = OpenAI(temperature=0)

# 创建ReAct Agent
agent = create_react_agent(llm, tools, prompt=WIKI_PROMPT)
agent_executor = AgentExecutor(agent=agent, tools=tools, verbose=True)

# 执行任务
agent_executor.invoke({"input": "爱因斯坦在哪所大学获得了博士学位？"})
```

**Section sources**
- [react/base.py](file://libs/langchain/langchain_classic/agents/react/base.py#L1-L190)
- [react/wiki_prompt.py](file://libs/langchain/langchain_classic/agents/react/wiki_prompt.py#L1-L5)

## Structured Chat Agent

Structured Chat Agent旨在优化与聊天模型的交互，能够处理具有多个输入参数的复杂工具。它使用结构化的JSON格式来解析Agent的决策，从而支持更复杂的工具调用。

### 核心设计原理
该Agent的核心是`StructuredChatAgent`类。它与传统的文本解析不同，要求LLM输出一个JSON对象，其中包含`action`（工具名）和`action_input`（工具参数）两个键。这使得它可以轻松处理需要多个参数的工具，而无需复杂的文本解析。

### Prompt模板结构
其Prompt使用`ChatPromptTemplate`，并明确要求LLM以JSON格式响应。

```python
FORMAT_INSTRUCTIONS = """Respond to the human as helpfully and accurately as possible. You have access to the following tools:

{tools}

Use a json blob to specify a tool by providing an action key (tool name) and an action_input key (tool input).

Valid "action" values: "Final Answer" or {tool_names}

Provide only ONE action per $JSON_BLOB, as shown:

```txt
{{
    "action": $TOOL_NAME,
    "action_input": $INPUT
}}
```

Follow this format:

Question: input question to answer
Thought: consider previous and subsequent steps
Action:
```
$JSON_BLOB
```
Observation: action result
... (repeat Thought/Action/Observation N times)
Thought: I know what to respond
Action:
```txt
{{
    "action": "Final Answer",
    "action_input": "Final response to human"
}}
"""
```

### 代码实现示例
```python
from langchain.agents import create_structured_chat_agent
from langchain_community.tools import Tool
from langchain_community.chat_models import ChatOpenAI
from langchain_core.prompts import ChatPromptTemplate, MessagesPlaceholder

# 定义一个需要多个参数的工具
def create_event_tool(title: str, date: str, location: str) -> str:
    return f"已创建事件: {title}，时间: {date}，地点: {location}"

tools = [
    Tool(
        name="CreateEvent",
        func=create_event_tool,
        args_schema={"title": "str", "date": "str", "location": "str"},
        description="用于创建一个新事件"
    )
]

llm = ChatOpenAI(model="gpt-3.5-turbo", temperature=0)
prompt = ChatPromptTemplate.from_messages([
    ("system", "你是一个日程安排助手。{tools}"),
    MessagesPlaceholder("chat_history", optional=True),
    ("human", "{input}"),
    MessagesPlaceholder("agent_scratchpad")
])

# 创建Structured Chat Agent
agent = create_structured_chat_agent(llm, tools, prompt)
agent_executor = AgentExecutor(agent=agent, tools=tools, verbose=True)

# 执行任务
agent_executor.invoke({
    "input": "请帮我安排一个明天下午3点在会议室A的项目会议。"
})
```

**Section sources**
- [structured_chat/base.py](file://libs/langchain/langchain_classic/agents/structured_chat/base.py#L1-L318)
- [structured_chat/prompt.py](file://libs/langchain/langchain_classic/agents/structured_chat/prompt.py#L1-L15)

## OpenAI Functions Agent

OpenAI Functions Agent是专为利用OpenAI API的函数调用（function calling）能力而设计的。它不依赖于文本解析，而是直接让LLM生成符合函数签名的参数，从而实现更可靠、更高效的工具调用。

### 核心设计原理
该Agent的核心是`OpenAIFunctionsAgent`类。它利用了OpenAI模型原生的`functions`参数。当调用LLM时，会将工具列表以OpenAI函数的格式传递。LLM会直接返回一个`function_call`对象，其中包含函数名和参数，无需任何文本解析，大大降低了出错率。

### Prompt模板结构
其Prompt相对简单，因为它依赖于LLM的原生功能。关键在于Prompt中必须包含一个名为`agent_scratchpad`的`MessagesPlaceholder`，用于存放Agent和工具之间的交互历史。

```python
from langchain_core.prompts import ChatPromptTemplate, MessagesPlaceholder

prompt = ChatPromptTemplate.from_messages([
    ("system", "You are a helpful assistant"),
    MessagesPlaceholder("chat_history", optional=True),
    ("human", "{input}"),
    MessagesPlaceholder("agent_scratchpad") # 这个占位符至关重要
])
```

### 代码实现示例
```python
from langchain.agents import create_openai_functions_agent, AgentExecutor
from langchain_community.tools import Tool
from langchain_community.chat_models import ChatOpenAI

# 定义工具
def get_weather(location: str) -> str:
    # 模拟获取天气
    return f"{location}今天天气晴朗，气温25度。"

tools = [
    Tool(
        name="GetWeather",
        func=get_weather,
        description="用于查询指定地点的天气"
    )
]

llm = ChatOpenAI(model="gpt-3.5-turbo-0613", temperature=0) # 需要支持函数调用的模型
prompt = ChatPromptTemplate.from_messages([
    ("system", "你是一个天气查询助手。"),
    ("human", "{input}"),
    MessagesPlaceholder("agent_scratchpad")
])

# 创建OpenAI Functions Agent
agent = create_openai_functions_agent(llm, tools, prompt)
agent_executor = AgentExecutor(agent=agent, tools=tools, verbose=True)

# 执行任务
agent_executor.invoke({"input": "上海现在的天气怎么样？"})
```

**Section sources**
- [openai_functions_agent/base.py](file://libs/langchain/langchain_classic/agents/openai_functions_agent/base.py#L1-L383)

## Agent类型选择建议

选择合适的Agent类型对应用的性能和可靠性至关重要。以下是选择建议：

| Agent类型 | 适用场景 | 优点 | 缺点 |
| :--- | :--- | :--- | :--- |
| **MRKL Agent** | 简单的问答、数学计算 | 实现简单，通用性强 | 依赖文本解析，容易出错；不支持复杂工具 |
| **ReAct Agent** | 需要从外部知识库检索信息的任务 | 推理过程清晰，适合文档检索 | 工具使用受限，灵活性较低 |
| **Structured Chat Agent** | 需要调用多参数复杂工具的聊天应用 | 支持复杂工具调用，结构清晰 | 仍依赖JSON文本解析，有一定出错风险 |
| **OpenAI Functions Agent** | 与OpenAI模型集成，需要高可靠性的工具调用 | 原生支持，最可靠，性能最好 | 依赖特定LLM（如OpenAI），可移植性差 |

**Section sources**
- [__init__.py](file://libs/langchain/langchain_classic/agents/__init__.py#L1-L165)
- [agent_types.py](file://libs/langchain/langchain_classic/agents/agent_types.py#L1-L58)
- [types.py](file://libs/langchain/langchain_classic/agents/types.py#L1-L28)

## 总结
LangChain提供了多种Agent类型来满足不同的应用需求。MRKL和ReAct Agent基于文本解析，历史悠久且通用；Structured Chat Agent通过JSON格式提升了对复杂工具的支持；而OpenAI Functions Agent则利用了现代LLM的原生功能，提供了最高级别的可靠性和性能。开发者应根据具体的LLM后端、工具复杂度和对可靠性的要求来选择最合适的Agent类型。