[根目录](../../CLAUDE.md) > [libs](../) > **text-splitters**

**变更记录 (Changelog):**
- 2025-11-18 12:46:56 - 增量更新：生成模块文档

## 模块职责

`langchain-text-splitters` 提供了丰富的文本分割工具，用于将长文本分割成适合处理的块。这是 LangChain 生态系统中处理文档的核心组件，支持多种分割策略和格式。

## 入口与启动

- **主入口**: `langchain_text_splitters/__init__.py`
- **基础类**: `langchain_text_splitters/base.py`
- **包名**: `langchain-text-splitters`
- **版本**: 1.0.0

### 核心导出
- `TextSplitter` - 基础分割器抽象类
- `CharacterTextSplitter` - 字符级分割
- `RecursiveCharacterTextSplitter` - 递归字符分割
- `TokenTextSplitter` - Token级分割

## 对外接口

### 基础接口
```python
from langchain_text_splitters import (
    TextSplitter,           # 基础抽象类
    CharacterTextSplitter,  # 字符分割器
    RecursiveCharacterTextSplitter,  # 递归字符分割器
    TokenTextSplitter,      # Token分割器
    Language,              # 编程语言枚举
    split_text_on_tokens   # Token分割函数
)
```

### 专用分割器
- **HTML**: `HTMLHeaderTextSplitter`, `HTMLSectionSplitter`, `HTMLSemanticPreservingSplitter`
- **Markdown**: `MarkdownHeaderTextSplitter`, `MarkdownTextSplitter`
- **JSON**: `RecursiveJsonSplitter`
- **代码**: `PythonCodeTextSplitter`, `JSFrameworkTextSplitter`
- **NLP**: `NLTKTextSplitter`, `SpacyTextSplitter`, `KonlpyTextSplitter`
- **ML**: `SentenceTransformersTokenTextSplitter`
- **文档**: `LatexTextSplitter`

## 关键依赖与配置

### 核心依赖
- `langchain-core>=1.0.0,<2.0.0` - 核心抽象和文档类
- Python >=3.10.0

### 可选依赖（按需）
- `tiktoken>=0.8.0` - OpenAI Tokenizer
- `transformers>=4.51.3` - HuggingFace Tokenizer
- `nltk>=3.9.1` - 自然语言处理
- `spacy>=3.8.7` - 高级NLP
- `sentence-transformers>=3.0.1` - 句子嵌入

### 配置特性
- 支持 ruff 代码检查
- mypy 严格类型检查
- pytest 测试框架
- 分层测试策略（单元测试 + 集成测试）

## 测试与质量

### 测试组织
```
tests/
├── unit_tests/           # 单元测试（无网络）
│   ├── test_text_splitters.py
│   └── test_html_security.py
├── integration_tests/    # 集成测试（允许网络）
│   ├── test_text_splitter.py
│   ├── test_nlp_text_splitters.py
│   └── test_compile.py
└── test_data/           # 测试数据
    └── test_splitter.xslt
```

### 质量工具
- **代码检查**: ruff（严格模式）
- **类型检查**: mypy（strict模式）
- **测试覆盖**: pytest + coverage
- **文档**: Google-style docstrings

## 设计模式

### 分割器层次结构
```python
TextSplitter (ABC)
├── CharacterTextSplitter
├── RecursiveCharacterTextSplitter
├── TokenTextSplitter
└── 专用分割器（HTML, Markdown, JSON等）
```

### 配置策略
- `chunk_size`: 块大小（默认4000）
- `chunk_overlap`: 块重叠（默认200）
- `length_function`: 长度计算函数
- `separators`: 分隔符列表

## 常见问题 (FAQ)

**Q: 如何选择合适的分割器？**
A:
- 通用文本：`RecursiveCharacterTextSplitter`
- 代码：使用语言特定的分割器（`PythonCodeTextSplitter`等）
- 结构化文档：使用`HTMLHeaderTextSplitter`或`MarkdownHeaderTextSplitter`
- Token敏感：使用`TokenTextSplitter`

**Q: 如何处理中文文本？**
A: 使用`RecursiveCharacterTextSplitter`配合合适的分隔符，或使用NLTK/Spacy分割器

**Q: 如何优化分割性能？**
A:
- 合理设置`chunk_size`和`chunk_overlap`
- 选择合适的`length_function`
- 对于大文档，考虑流式处理

## 相关文件清单

### 核心实现
- `langchain_text_splitters/base.py` - 基础抽象类和接口
- `langchain_text_splitters/character.py` - 字符级分割器
- `langchain_text_splitters/html.py` - HTML分割器
- `langchain_text_splitters/markdown.py` - Markdown分割器
- `langchain_text_splitters/json.py` - JSON分割器
- `langchain_text_splitters/python.py` - Python代码分割器

### NLP分割器
- `langchain_text_splitters/nltk.py` - NLTK分割器
- `langchain_text_splitters/spacy.py` - Spacy分割器
- `langchain_text_splitters/konlpy.py` - 韩语分割器
- `langchain_text_splitters/sentence_transformers.py` - 句子嵌入分割器

### 专用格式
- `langchain_text_splitters/latex.py` - LaTeX分割器
- `langchain_text_splitters/jsx.py` - JSX/React分割器

### 配置文件
- `pyproject.toml` - 项目配置和依赖
- `README.md` - 使用说明
- `tests/` - 测试套件

---

*此文档由增量更新生成于 2025-11-18 12:46:56*