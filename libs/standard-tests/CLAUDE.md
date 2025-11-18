[根目录](../../CLAUDE.md) > [libs](../) > **standard-tests**

**变更记录 (Changelog):**
- 2025-11-18 12:46:56 - 增量更新：生成模块文档

## 模块职责

`langchain-tests` 提供了标准化的测试框架和工具，用于确保 LangChain 生态系统中所有实现的一致性和质量。它定义了通用的测试基类、工具和约定，供各个集成包使用。

## 入口与启动

- **主入口**: `langchain_tests/__init__.py`
- **基础类**: `langchain_tests/base.py`
- **包名**: `langchain-tests`
- **版本**: 1.0.0

### 核心导出
- `BaseStandardTests` - 标准测试基类
- 测试工具和辅助函数
- 集成测试框架

## 对外接口

### 测试基类
```python
from langchain_tests import BaseStandardTests

class MyIntegrationTests(BaseStandardTests):
    """继承标准测试基类"""
    pass
```

### 测试类别
- **单元测试**: `langchain_tests/unit_tests/`
  - `chat_models.py` - 聊天模型测试
  - `embeddings.py` - 嵌入模型测试
  - `tools.py` - 工具测试

- **集成测试**: `langchain_tests/integration_tests/`
  - `chat_models.py` - 聊天模型集成测试
  - `embeddings.py` - 嵌入模型集成测试
  - `vectorstores.py` - 向量存储测试
  - `tools.py` - 工具集成测试
  - `retrievers.py` - 检索器测试
  - `cache.py` - 缓存测试
  - `indexer.py` - 索引器测试
  - `base_store.py` - 基础存储测试

## 关键依赖与配置

### 核心依赖
- `langchain-core>=1.0.0,<2.0.0` - 核心抽象
- `pytest>=7.0.0,<9.0.0` - 测试框架
- `pytest-asyncio>=0.20.0,<2.0.0` - 异步测试支持
- `httpx>=0.28.1,<1.0.0` - HTTP客户端
- `syrupy>=4.0.0,<5.0.0` - 快照测试
- `vcrpy>=7.0.0,<8.0.0` - HTTP交互录制

### 测试工具
- `pytest-socket` - 网络访问控制
- `pytest-benchmark` - 性能测试
- `pytest-codspeed` - 代码速度测试
- `pytest-recording` - 交互录制

### 配置特性
- **严格模式**: 严格的标记和配置
- **异步支持**: 自动异步模式
- **网络控制**: 测试中的网络访问管理
- **快照测试**: 确保输出一致性

## 测试策略

### 标准测试原则
1. **一致性**: 所有实现遵循相同的测试标准
2. **完整性**: 覆盖核心功能和边界情况
3. **可维护性**: 标准化的测试结构和命名
4. **可扩展性**: 支持自定义测试需求

### 测试层次
```
标准测试框架
├── 基础测试标准 (BaseStandardTests)
├── 单元测试标准 (unit_tests/)
└── 集成测试标准 (integration_tests/)
```

### 使用指南
集成包通过继承标准测试基类来确保一致性：

```python
from langchain_tests.unit_tests import ChatModelTests

class MyChatModelTests(ChatModelTests):
    """自定义聊天模型测试，继承标准测试"""

    @property
    def chat_model_class(self):
        return MyChatModel

    @property
    def chat_model_params(self):
        return {"api_key": "test_key"}
```

## 质量保证

### 测试验证
- **标准合规性**: 确保不覆盖标准测试
- **完整性检查**: 验证所有标准测试都被执行
- **标记系统**: 使用 pytest 标记分类测试

### 代码质量工具
- **ruff**: 代码检查和格式化
- **mypy**: 严格类型检查
- **coverage**: 测试覆盖率分析

### CI/CD集成
- **GitHub Actions**: 自动化测试执行
- **矩阵测试**: 多Python版本测试
- **集成验证**: 跨包兼容性测试

## 开发工具

### 测试辅助工具
- `conftest.py` - 测试配置和fixtures
- `utils/pydantic.py` - Pydantic测试工具
- `scripts/check_imports.py` - 导入检查

### 示例和模板
- `tests/unit_tests/` - 单元测试示例
- `tests/integration_tests/` - 集成测试示例
- `custom_chat_model.py` - 自定义模型示例

## 常见问题 (FAQ)

**Q: 如何在集成包中使用标准测试？**
A: 继承相应的测试基类，并实现必需的属性和方法。详见[集成指南](https://docs.langchain.com/oss/python/contributing/standard-tests-langchain)。

**Q: 可以自定义测试吗？**
A: 可以，但不能覆盖标准测试。使用`@pytest.mark.skip`跳过不适用的测试，或添加自定义测试方法。

**Q: 如何处理网络依赖的测试？**
A: 使用VCR.py录制HTTP交互，或使用`pytest-socket`控制网络访问。

**Q: 快照测试如何工作？**
A: 使用syrupy进行快照测试，确保输出结果的一致性。

## 相关文件清单

### 核心框架
- `langchain_tests/__init__.py` - 包入口和文档
- `langchain_tests/base.py` - 基础测试类
- `langchain_tests/conftest.py` - 测试配置

### 单元测试标准
- `langchain_tests/unit_tests/__init__.py`
- `langchain_tests/unit_tests/chat_models.py`
- `langchain_tests/unit_tests/embeddings.py`
- `langchain_tests/unit_tests/tools.py`

### 集成测试标准
- `langchain_tests/integration_tests/__init__.py`
- `langchain_tests/integration_tests/chat_models.py`
- `langchain_tests/integration_tests/embeddings.py`
- `langchain_tests/integration_tests/vectorstores.py`
- `langchain_tests/integration_tests/tools.py`
- `langchain_tests/integration_tests/retrievers.py`
- `langchain_tests/integration_tests/cache.py`
- `langchain_tests/integration_tests/indexer.py`
- `langchain_tests/integration_tests/base_store.py`

### 工具和实用程序
- `langchain_tests/utils/__init__.py`
- `langchain_tests/utils/pydantic.py`

### 示例和文档
- `tests/unit_tests/` - 单元测试示例
- `tests/integration_tests/` - 集成测试示例
- `scripts/` - 开发脚本

### 配置文件
- `pyproject.toml` - 项目配置和依赖
- `README.md` - 使用说明

---

*此文档由增量更新生成于 2025-11-18 12:46:56*