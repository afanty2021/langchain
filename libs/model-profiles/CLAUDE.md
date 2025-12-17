[根目录](../../CLAUDE.md) > [libs](../) > **model-profiles**

# LangChain Model Profiles 模块

## 模块职责

`langchain-model-profiles` 是 LangChain 生态系统中专门负责提供大语言模型能力信息的模块。它为开发者提供了一个标准化的方式来查询不同模型的特性和限制，如上下文窗口大小、支持的输入/输出模态、结构化输出支持、工具调用能力等。

## 入口与启动

- **主入口**: `langchain_model_profiles/__init__.py`
- **核心类**: `ModelProfile`, `get_model_profile`
- **版本**: 0.0.5

```python
# 模块初始化
from langchain_model_profiles.model_profile import ModelProfile, get_model_profile

__all__ = [
    "ModelProfile",
    "get_model_profile",
]
```

## 对外接口

### ModelProfile (TypedDict)

核心数据结构，定义了模型能力的完整描述：

```python
class ModelProfile(TypedDict, total=False):
    # 输入约束
    max_input_tokens: int  # 最大上下文窗口
    image_inputs: bool     # 是否支持图像输入
    image_url_inputs: bool # 是否支持图像URL输入
    pdf_inputs: bool       # 是否支持PDF输入
    audio_inputs: bool     # 是否支持音频输入
    video_inputs: bool     # 是否支持视频输入

    # 输出约束
    max_output_tokens: int # 最大输出token数

    # 结构化输出支持
    structured_output: bool
    json_mode: bool

    # 工具调用能力
    tool_calling: bool
    parallel_tool_calls: bool

    # 流式支持
    streaming: bool

    # 其他特性
    vision_models: bool    # 视觉模型支持
    function_calling: bool # 函数调用支持
```

### get_model_profile() 函数

获取特定模型的配置文件信息：

```python
def get_model_profile(model_name: str) -> Optional[ModelProfile]:
    """获取指定模型的能力配置文件"""
```

## 关键依赖与配置

### 核心依赖
- **tomli**: TOML文件解析 (Python < 3.11)
- **typing-extensions**: 类型注解扩展

### 开发依赖
- **httpx**: 用于数据刷新脚本的网络请求
- **pytest**: 测试框架
- **langchain**: 用于集成测试

### 配置文件结构
```toml
[tool.uv.sources]
langchain-core = { path = "../core", editable = true }
langchain = { path = "../langchain_v1", editable = true }
```

## 数据模型

### 数据源

基于 [models.dev](https://github.com/sst/models.dev) 项目的开源模型能力数据，并进行了以下扩展：

1. **额外字段**: 在原始数据基础上增加了 LangChain 特定的能力描述
2. **本地化数据**: 通过 `_data_loader.py` 进行数据加载和缓存
3. **增量更新**: 支持通过 `refresh_data.py` 脚本进行数据更新

### 数据加载机制

```python
# _data_loader.py
class _DataLoader:
    """负责加载和管理模型能力数据"""

    def load_data(self) -> dict:
        """加载模型数据"""

    def get_model_profile(self, model_name: str) -> Optional[dict]:
        """获取特定模型的配置文件"""
```

## 测试与质量

### 测试架构

```
测试结构
├── unit_tests/          # 单元测试
│   ├── test_chat_model.py     # 聊天模型集成测试
│   ├── test_data_loader.py    # 数据加载器测试
│   └── test_model_profile.py  # 模型配置文件测试
└── integration_tests/   # 集成测试
    └── test_compile.py        # 编译验证测试
```

### 测试覆盖范围

1. **数据加载准确性**: 验证模型数据的完整性和正确性
2. **类型安全**: TypedDict 的类型验证
3. **集成兼容性**: 与 langchain 核心包的集成测试
4. **性能测试**: 数据加载和查询的性能基准

### 质量工具

- **代码质量**: ruff (严格模式) + mypy (类型检查)
- **测试框架**: pytest + syrupy (快照)
- **覆盖率**: pytest-cov 目标 >80%

## 使用模式

### 基本用法

```python
from langchain.chat_models import init_chat_model

# 初始化模型并获取配置文件
model = init_chat_model("openai:gpt-4")
profile = model.profile

# 检查模型能力
if profile.get("structured_output"):
    print("支持结构化输出")

if profile.get("max_input_tokens"):
    print(f"最大输入tokens: {profile['max_input_tokens']}")
```

### 高级查询

```python
from langchain_model_profiles import get_model_profile

# 直接查询模型配置
profile = get_model_profile("claude-3-opus")

# 检查多模态支持
multimodal_features = [
    feature for feature in ["image_inputs", "audio_inputs", "pdf_inputs"]
    if profile.get(feature)
]

# 工具调用能力检查
tool_capabilities = {
    "tool_calling": profile.get("tool_calling", False),
    "parallel_tools": profile.get("parallel_tool_calls", False),
    "function_calling": profile.get("function_calling", False)
}
```

## 开发工具

### 数据刷新脚本

```bash
# 更新模型数据（需要网络连接）
uv run python scripts/refresh_data.py
```

### 测试命令

```bash
# 运行单元测试
make test

# 运行集成测试
make integration_test

# 检查代码质量
make lint
make format

# 类型检查
uv run mypy .
```

## 常见问题 (FAQ)

### Q: 如何添加新模型的能力数据？
A: 新模型数据通过 `scripts/refresh_data.py` 脚本从上游 models.dev 项目获取。如需添加自定义数据，可手动编辑数据文件。

### Q: 模型数据的更新频率是多少？
A: 目前依赖上游项目更新，建议定期运行刷新脚本获取最新数据。

### Q: 如何报告模型数据不准确的问题？
A: 可以通过 GitHub Issues 报告，或在上游 models.dev 项目中贡献修正。

### Q: 是否支持自定义模型配置文件？
A: 当前版本主要基于开源数据，未来版本可能会增加自定义配置支持。

## 相关文件清单

### 核心文件
- `langchain_model_profiles/__init__.py` - 模块入口
- `langchain_model_profiles/model_profile.py` - 核心数据结构定义
- `langchain_model_profiles/_data_loader.py` - 数据加载器实现

### 数据和脚本
- `scripts/refresh_data.py` - 数据刷新脚本
- `langchain_model_profiles/data/` - 模型数据存储目录

### 测试文件
- `tests/unit_tests/test_model_profile.py` - 核心功能测试
- `tests/unit_tests/test_data_loader.py` - 数据加载测试
- `tests/unit_tests/test_chat_model.py` - 集成测试

### 配置文件
- `pyproject.toml` - 项目配置和依赖
- `Makefile` - 构建和测试命令
- `README.md` - 使用指南

## 变更记录 (Changelog)

- 2025-11-19 14:30:00 - **新模块发现**: 发现并完整分析 model-profiles 模块
  - 完成模块结构分析
  - 创建完整模块文档
  - 更新根级索引文件
  - 添加到总体架构图

- 2025-11-18 12:56:16 - **前期维护**: 在常规维护中确认模块存在但未深度分析

---

*此模块文档生成于 2025-11-19 14:30:00*