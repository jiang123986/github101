# 贡献指南

感谢你对 SuperAgent 项目的关注！我们欢迎任何形式的贡献。

## 🌟 贡献方式

### 1. 报告 Bug 🐛

发现 bug？请通过 [GitHub Issues](https://github.com/your-org/super-agent/issues) 报告。

**报告模板**:

```markdown
## Bug 描述
简明扼要地描述问题

## 复现步骤
1. 执行命令 '...'
2. 输入 '...'
3. 观察到错误

## 期望行为
描述你期望发生的事情

## 实际行为
描述实际发生的事情

## 环境信息
- OS: [e.g. macOS 14.0]
- Python: [e.g. 3.11]
- SuperAgent: [e.g. 0.1.0]

## 附加信息
错误日志、截图等
```

### 2. 功能建议 💡

有新想法？欢迎在 [Discussions](https://github.com/your-org/super-agent/discussions) 提出。

**建议模板**:

```markdown
## 功能描述
清晰描述你想要的功能

## 使用场景
描述这个功能解决什么问题

## 建议实现方案
如果有想法，描述可能的实现方式

## 替代方案
描述你考虑过的其他方案
```

### 3. 提交代码 🔧

#### 开发环境设置

```bash
# 1. Fork 项目
# 在 GitHub 上点击 Fork 按钮

# 2. 克隆你的 Fork
git clone https://github.com/YOUR_NAME/super-agent.git
cd super-agent

# 3. 添加上游仓库
git remote add upstream https://github.com/your-org/super-agent.git

# 4. 创建虚拟环境
python -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate

# 5. 安装开发依赖
pip install -e ".[dev]"

# 6. 安装 pre-commit hooks
pre-commit install
```

#### 开发流程

```bash
# 1. 同步最新代码
git fetch upstream
git checkout main
git merge upstream/main

# 2. 创建功能分支
git checkout -b feature/your-feature-name

# 3. 开发和测试
# ... 编写代码 ...

# 运行测试
pytest

# 运行代码检查
ruff check .
mypy .

# 4. 提交代码
git add .
git commit -m "feat: add your feature description"

# 5. 推送到你的 Fork
git push origin feature/your-feature-name

# 6. 创建 Pull Request
# 在 GitHub 上创建 PR
```

#### Commit 规范

我们使用 [Conventional Commits](https://www.conventionalcommits.org/) 规范：

```
<type>(<scope>): <description>

[optional body]

[optional footer]
```

**类型 (type)**:
- `feat`: 新功能
- `fix`: Bug 修复
- `docs`: 文档更新
- `style`: 代码格式（不影响功能）
- `refactor`: 代码重构
- `test`: 测试相关
- `chore`: 构建/工具相关

**示例**:
```
feat(agent): add code review capability
fix(cli): resolve config loading issue
docs(readme): update installation guide
```

#### Pull Request 规范

**PR 标题**: 遵循 Commit 规范

**PR 描述模板**:

```markdown
## 变更说明
描述这个 PR 做了什么

## 变更类型
- [ ] Bug 修复
- [ ] 新功能
- [ ] 破坏性变更
- [ ] 文档更新

## 测试
描述如何测试这些变更

## 检查清单
- [ ] 代码符合项目规范
- [ ] 已添加必要的测试
- [ ] 所有测试通过
- [ ] 已更新相关文档
```

### 4. 完善文档 📖

文档是项目成功的关键！欢迎帮助我们：

- 修复文档错误
- 改进文档清晰度
- 添加使用示例
- 翻译文档

### 5. 帮助他人 🤝

- 在 Issues 中回答问题
- 在 Discussions 中参与讨论
- 编写教程和博客
- 制作视频教程

---

## 📁 项目结构

```
super-agent/
├── super_agent/          # 主包
│   ├── __init__.py
│   ├── core/             # 核心模块
│   │   ├── orchestrator.py
│   │   ├── scheduler.py
│   │   └── state.py
│   ├── agents/           # 智能体
│   │   ├── base.py
│   │   ├── planner.py
│   │   ├── coder.py
│   │   └── reviewer.py
│   ├── memory/           # 记忆系统
│   ├── tools/            # 工具集
│   └── cli/              # CLI 入口
├── tests/                # 测试
│   ├── unit/
│   ├── integration/
│   └── e2e/
├── docs/                 # 文档
├── examples/             # 示例代码
└── scripts/              # 脚本工具
```

---

## 🎨 代码规范

### Python 代码规范

- 使用 [Black](https://github.com/psf/black) 格式化
- 使用 [Ruff](https://github.com/astral-sh/ruff) 检查
- 使用 [mypy](https://mypy-lang.org/) 类型检查

**配置示例** (pyproject.toml):

```toml
[tool.black]
line-length = 88
target-version = ['py311']

[tool.ruff]
select = ["E", "F", "I", "N", "W"]
ignore = ["E501"]
line-length = 88

[tool.mypy]
python_version = "3.11"
strict = true
```

### 代码风格指南

```python
# ✅ 好的代码风格
from typing import Optional

class Agent:
    """智能体基类。
    
    Attributes:
        name: 智能体名称
        tools: 可用工具列表
    """
    
    def __init__(self, name: str, tools: list[Tool] | None = None) -> None:
        self.name = name
        self.tools = tools or []
    
    async def execute(self, task: Task) -> Result:
        """执行任务。
        
        Args:
            task: 要执行的任务
            
        Returns:
            执行结果
            
        Raises:
            ExecutionError: 执行失败时抛出
        """
        ...
```

---

## 🧪 测试规范

### 测试结构

```python
# tests/unit/agents/test_coder.py

import pytest
from super_agent.agents import CoderAgent

class TestCoderAgent:
    """CoderAgent 测试类"""
    
    @pytest.fixture
    def agent(self) -> CoderAgent:
        """创建测试用智能体"""
        return CoderAgent()
    
    async def test_generate_code_success(self, agent: CoderAgent) -> None:
        """测试代码生成成功场景"""
        spec = CodeSpec(language="python", description="Hello World")
        result = await agent.generate_code(spec)
        
        assert result.success
        assert "print" in result.code
    
    async def test_generate_code_invalid_language(self, agent: CoderAgent) -> None:
        """测试无效语言场景"""
        spec = CodeSpec(language="invalid", description="test")
        
        with pytest.raises(UnsupportedLanguageError):
            await agent.generate_code(spec)
```

### 运行测试

```bash
# 运行所有测试
pytest

# 运行特定测试
pytest tests/unit/agents/test_coder.py

# 运行并生成覆盖率报告
pytest --cov=super_agent --cov-report=html

# 运行特定标记的测试
pytest -m "not slow"
```

---

## 📋 审查标准

PR 会根据以下标准进行审查：

### 代码质量
- [ ] 代码清晰易读
- [ ] 遵循项目代码规范
- [ ] 没有重复代码
- [ ] 适当的错误处理

### 测试
- [ ] 新功能有对应测试
- [ ] Bug 修复有回归测试
- [ ] 所有测试通过

### 文档
- [ ] 公共 API 有文档字符串
- [ ] 复杂逻辑有注释
- [ ] 用户文档已更新（如需要）

### 设计
- [ ] 符合项目架构设计
- [ ] 接口设计合理
- [ ] 考虑了边界情况

---

## 🏆 贡献者认可

### 贡献者榜单

所有贡献者都会被列入项目的贡献者榜单。

### 贡献者等级

| 等级 | 徽章 | 条件 |
|------|------|------|
| 新手 | 🌱 | 首次 PR 合并 |
| 活跃 | 🌿 | 5+ PR 合并 |
| 核心 | 🌳 | 20+ PR 合并 |
| 维护者 | 🌟 | 获得维护者权限 |

### 特别贡献

突出贡献者将获得：
- 项目 README 致谢
- 年度贡献者表彰
- 开源活动演讲机会

---

## ❓ 常见问题

### Q: 我是新手，从哪里开始？

查看标记为 `good first issue` 的 Issue，这些适合新贡献者上手。

### Q: 如何获取帮助？

- 查阅文档
- 在 Discussions 提问
- 加入 Discord 社区

### Q: PR 审查需要多长时间？

我们尽量在 3-5 个工作日内完成首次审查。

### Q: 可以用中文提交 Issue/PR 吗？

可以！我们支持中英文交流。

---

## 📞 联系我们

- **GitHub Discussions**: 技术讨论
- **Discord**: 实时交流
- **Email**: contact@super-agent.dev

---

感谢你的贡献！让我们一起打造最好的超级个体智能体！ 🚀

