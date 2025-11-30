# SuperAgent 快速入门指南

## 🚀 5 分钟快速开始

### 前提条件

- Python 3.11+
- OpenAI API Key 或其他 LLM API Key
- Git

### 步骤 1: 安装

```bash
# 使用 pip 安装
pip install super-agent

# 或从源码安装（推荐开发者）
git clone https://github.com/your-org/super-agent.git
cd super-agent
pip install -e .
```

### 步骤 2: 配置

```bash
# 初始化配置文件
super-agent init

# 配置 LLM (选择一个)
super-agent config set provider openai
super-agent config set api_key sk-your-openai-key

# 或使用 Claude
super-agent config set provider anthropic
super-agent config set api_key sk-ant-your-claude-key

# 或使用本地模型 (Ollama)
super-agent config set provider ollama
super-agent config set model llama3
```

### 步骤 3: 第一个任务

```bash
# 创建一个简单的 Python 脚本
super-agent do "创建一个 Python 脚本，读取 CSV 文件并生成柱状图"

# 查看生成的文件
ls -la
```

**🎉 恭喜！你已经完成了第一次 AI 辅助编程！**

---

## 📖 核心概念

### 1. 工作模式

SuperAgent 支持三种工作模式：

#### 命令模式 (Command Mode)
```bash
# 单次执行任务
super-agent do "实现用户登录功能"
```

#### 交互模式 (Chat Mode)
```bash
# 持续对话
super-agent chat

> 你好，请帮我分析一下这个项目的架构
AI: 正在分析项目结构...

> 接下来，帮我添加一个新的 API 端点
AI: 好的，请告诉我这个端点的功能...
```

#### 自动模式 (Auto Mode)
```bash
# 给定目标，自动完成
super-agent auto "将这个 Flask 应用迁移到 FastAPI"
```

### 2. 智能体类型

| 智能体 | 职责 | 触发场景 |
|--------|------|----------|
| **Planner** | 任务规划 | 复杂任务分解 |
| **Coder** | 代码编写 | 生成/修改代码 |
| **Reviewer** | 代码审查 | 质量检查 |
| **Tester** | 测试验证 | 测试生成/运行 |
| **Deployer** | 部署运维 | CI/CD 配置 |

### 3. 工具集

SuperAgent 内置多种工具：

```bash
# 查看可用工具
super-agent tools list

# 工具列表
- file_read     # 读取文件
- file_write    # 写入文件
- file_search   # 搜索文件
- code_search   # 语义搜索代码
- terminal      # 执行命令
- web_search    # 网络搜索
- git           # Git 操作
```

---

## 🎯 常用场景

### 场景 1: 快速创建项目

```bash
# 创建 React + TypeScript 项目
super-agent create "一个任务管理应用，支持增删改查和拖拽排序"

# 创建后端 API
super-agent create "RESTful API 后端，使用 FastAPI 和 PostgreSQL"
```

### 场景 2: 代码重构

```bash
# 分析现有代码
super-agent analyze ./src

# 执行重构
super-agent refactor "将所有类组件转换为函数式组件"
```

### 场景 3: 添加功能

```bash
# 添加新功能
super-agent do "在用户模块添加密码重置功能，包括邮件发送"
```

### 场景 4: Bug 修复

```bash
# 描述问题
super-agent fix "登录时偶尔报 500 错误，可能和并发有关"
```

### 场景 5: 代码审查

```bash
# 审查最近的改动
super-agent review ./src/auth/

# 审查 PR
super-agent review --pr 123
```

### 场景 6: 测试生成

```bash
# 为模块生成测试
super-agent test generate ./src/services/user.py

# 运行测试并修复失败
super-agent test fix
```

---

## 💡 使用技巧

### 1. 提供清晰的上下文

```bash
# ❌ 模糊的请求
super-agent do "加个按钮"

# ✅ 清晰的请求
super-agent do "在用户列表页面顶部添加一个'导出 CSV'按钮，点击后下载当前筛选结果"
```

### 2. 分步骤执行复杂任务

```bash
# 复杂功能分解
super-agent chat

> 我想实现一个完整的电商购物车系统
AI: 这是一个复杂的功能，建议分步实现：
    1. 购物车数据模型
    2. 添加/删除商品 API
    3. 库存检查逻辑
    4. 价格计算（含优惠券）
    5. 前端 UI 组件
    
> 好的，先从第一步开始
```

### 3. 使用项目规则文件

创建 `.super-agent.yaml` 配置项目特定规则：

```yaml
# .super-agent.yaml
project:
  name: my-awesome-project
  type: web-app
  
code_style:
  language: typescript
  framework: react
  indent: 2
  quotes: single
  
conventions:
  - 使用函数式组件和 Hooks
  - 状态管理使用 Zustand
  - API 调用使用 React Query
  - 组件文件使用 PascalCase
  
ignore:
  - node_modules/
  - dist/
  - .env
```

### 4. 保存常用提示词

```bash
# 保存提示词模板
super-agent prompt save review "请审查这段代码的：
1. 性能问题
2. 安全漏洞
3. 代码规范
4. 潜在 bug"

# 使用模板
super-agent prompt use review ./src/api/auth.py
```

---

## ⚙️ 配置详解

### 配置文件位置

- 全局配置: `~/.super-agent/config.yaml`
- 项目配置: `./.super-agent.yaml`

### 完整配置示例

```yaml
# ~/.super-agent/config.yaml

# LLM 配置
llm:
  provider: openai           # openai, anthropic, ollama
  model: gpt-4-turbo        # 模型名称
  api_key: ${OPENAI_API_KEY} # 支持环境变量
  temperature: 0.7
  max_tokens: 4096

# 记忆配置
memory:
  short_term_size: 20       # 短期记忆条数
  long_term_backend: chroma # chroma, pinecone, none
  vector_db_path: ~/.super-agent/vectors

# 工具配置
tools:
  terminal:
    enabled: true
    allowed_commands:
      - npm
      - pip
      - git
      - docker
    blocked_commands:
      - rm -rf /
      - sudo
  
  file:
    allowed_paths:
      - ./src
      - ./tests
    blocked_patterns:
      - "*.env"
      - "**/secrets/**"

# 日志配置
logging:
  level: info               # debug, info, warning, error
  file: ~/.super-agent/logs/agent.log

# 安全配置
security:
  confirm_destructive: true # 破坏性操作需确认
  sandbox_mode: false       # 沙箱模式
```

---

## 🔧 故障排除

### 常见问题

#### Q: API Key 无效
```bash
# 检查配置
super-agent config show

# 重新设置
super-agent config set api_key YOUR_NEW_KEY
```

#### Q: 生成的代码有错误
```bash
# 让 AI 自动修复
super-agent fix-errors

# 或手动指出问题
super-agent do "上一步生成的代码有语法错误，请修复"
```

#### Q: 响应太慢
```bash
# 使用更快的模型
super-agent config set model gpt-3.5-turbo

# 或限制上下文大小
super-agent config set context_limit 10000
```

#### Q: 内存使用过高
```bash
# 清理缓存
super-agent cache clear

# 减少向量存储
super-agent memory compact
```

### 获取帮助

```bash
# 查看帮助
super-agent --help
super-agent do --help

# 查看日志
super-agent logs

# 生成诊断报告
super-agent diagnose > report.txt
```

---

## 📚 下一步学习

1. **阅读完整文档**: [文档中心](https://docs.super-agent.dev)
2. **查看示例项目**: [examples/](https://github.com/your-org/super-agent/tree/main/examples)
3. **加入社区**: [Discord](https://discord.gg/superagent)
4. **贡献代码**: [贡献指南](./CONTRIBUTING.md)

---

## 🎓 学习资源

### 视频教程
- [10 分钟上手 SuperAgent](https://youtube.com/watch?v=xxx)
- [构建你的第一个 AI 辅助项目](https://youtube.com/watch?v=xxx)

### 博客文章
- [SuperAgent 最佳实践](https://blog.super-agent.dev/best-practices)
- [从零到超级个体](https://blog.super-agent.dev/super-individual)

### 社区资源
- [Awesome SuperAgent](https://github.com/your-org/awesome-super-agent) - 精选资源列表
- [插件市场](https://plugins.super-agent.dev) - 社区插件

---

**开始你的超级个体之旅吧！** 🚀

