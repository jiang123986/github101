# SuperAgent 技术选型

## 概览

本文档详细说明 SuperAgent 项目的技术选型及其理由。

## 核心技术栈

```
┌─────────────────────────────────────────────────────────────┐
│                       技术架构全景                           │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  前端层        │  后端层        │  AI层          │  存储层   │
│  ─────────    │  ─────────    │  ─────────    │  ─────────│
│  React        │  FastAPI      │  LangChain    │  PostgreSQL│
│  TypeScript   │  Python 3.11+ │  OpenAI       │  Redis     │
│  TailwindCSS  │  Pydantic     │  Claude       │  ChromaDB  │
│  Vite         │  AsyncIO      │  Ollama       │  S3/MinIO  │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

---

## 1. 编程语言

### Python 3.11+ (主要语言)

**选择理由**:
- 🤖 AI/ML 生态系统最丰富
- 🔧 快速原型开发
- 📚 丰富的库支持
- 🌐 社区活跃度高

**关键特性使用**:
```python
# 类型注解 (PEP 604)
def process(data: str | None) -> list[dict]:
    ...

# 结构化模式匹配 (PEP 634)
match command:
    case {"action": "create", "target": target}:
        await create(target)
    case {"action": "delete", "id": id}:
        await delete(id)

# 异常组 (PEP 654)
try:
    await asyncio.gather(*tasks)
except* ValidationError as eg:
    handle_validation_errors(eg.exceptions)
```

### TypeScript (前端)

**选择理由**:
- ✅ 类型安全
- 🔍 更好的 IDE 支持
- 📦 与 React 生态完美配合

---

## 2. 后端框架

### FastAPI

**选择理由**:
- ⚡ 高性能 (基于 Starlette)
- 📝 自动 API 文档
- 🔒 内置数据验证
- 🔄 原生异步支持

**对比其他框架**:

| 特性 | FastAPI | Django | Flask |
|------|---------|--------|-------|
| 性能 | ⭐⭐⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐⭐ |
| 异步支持 | 原生 | 有限 | 需插件 |
| 类型检查 | 原生 | 有限 | 需插件 |
| 学习曲线 | 低 | 中 | 低 |
| API 文档 | 自动 | 需插件 | 需插件 |

**示例代码**:

```python
from fastapi import FastAPI, HTTPException
from pydantic import BaseModel

app = FastAPI(title="SuperAgent API")

class TaskRequest(BaseModel):
    description: str
    context: dict | None = None

@app.post("/api/v1/tasks")
async def create_task(request: TaskRequest):
    """创建新任务"""
    task = await orchestrator.create_task(request.description)
    return {"task_id": task.id, "status": "created"}
```

---

## 3. AI/LLM 框架

### LangChain

**选择理由**:
- 🔗 强大的链式组合能力
- 🛠️ 丰富的工具集成
- 📊 内置追踪和调试
- 🔌 多模型支持

**核心使用场景**:

```python
from langchain.agents import AgentExecutor
from langchain.tools import Tool
from langchain_openai import ChatOpenAI

# 创建工具
tools = [
    Tool(
        name="code_search",
        func=search_codebase,
        description="在代码库中搜索相关代码"
    ),
    Tool(
        name="file_edit",
        func=edit_file,
        description="编辑文件内容"
    ),
]

# 创建智能体
agent = create_react_agent(
    llm=ChatOpenAI(model="gpt-4-turbo"),
    tools=tools,
    prompt=agent_prompt
)

executor = AgentExecutor(agent=agent, tools=tools)
```

### 支持的 LLM 提供商

| 提供商 | 模型 | 适用场景 |
|--------|------|----------|
| **OpenAI** | GPT-4, GPT-4-Turbo | 复杂推理、代码生成 |
| **Anthropic** | Claude 3 Opus/Sonnet | 长上下文、安全任务 |
| **本地部署** | Ollama (Llama, Mistral) | 隐私敏感场景 |
| **Azure** | Azure OpenAI | 企业合规场景 |

---

## 4. 向量数据库

### ChromaDB (默认)

**选择理由**:
- 🚀 轻量级，开箱即用
- 💾 支持持久化
- 🔧 API 简洁
- 📦 可嵌入应用

**示例代码**:

```python
import chromadb
from chromadb.utils import embedding_functions

# 初始化
client = chromadb.PersistentClient(path="./data/vectors")
embedding_fn = embedding_functions.OpenAIEmbeddingFunction()

# 创建集合
collection = client.get_or_create_collection(
    name="codebase",
    embedding_function=embedding_fn
)

# 索引代码
collection.add(
    documents=["def hello(): print('world')"],
    metadatas=[{"file": "main.py", "line": 1}],
    ids=["main.py:1"]
)

# 语义搜索
results = collection.query(
    query_texts=["打印输出函数"],
    n_results=5
)
```

### 备选方案

| 数据库 | 特点 | 适用场景 |
|--------|------|----------|
| **Pinecone** | 云托管，高可用 | 生产环境 |
| **Milvus** | 高性能，分布式 | 大规模部署 |
| **Weaviate** | GraphQL API | 复杂查询 |
| **Qdrant** | Rust 实现，高效 | 性能敏感 |

---

## 5. 数据库

### PostgreSQL (主数据库)

**选择理由**:
- 📊 强大的关系型能力
- 🔍 全文搜索支持
- 📈 JSON 支持 (JSONB)
- 🔒 可靠性高

**数据模型示例**:

```sql
-- 任务表
CREATE TABLE tasks (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID REFERENCES users(id),
    description TEXT NOT NULL,
    status VARCHAR(20) DEFAULT 'pending',
    context JSONB,
    result JSONB,
    created_at TIMESTAMP DEFAULT NOW(),
    updated_at TIMESTAMP DEFAULT NOW()
);

-- 智能体执行记录
CREATE TABLE agent_executions (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    task_id UUID REFERENCES tasks(id),
    agent_type VARCHAR(50) NOT NULL,
    input JSONB,
    output JSONB,
    tokens_used INTEGER,
    duration_ms INTEGER,
    created_at TIMESTAMP DEFAULT NOW()
);
```

### Redis (缓存和消息队列)

**用途**:
- 🚀 高速缓存
- 📬 任务队列 (with Redis Streams)
- 🔄 会话管理
- ⏰ 分布式锁

```python
import redis.asyncio as redis

redis_client = redis.Redis(host='localhost', port=6379)

# 缓存 LLM 响应
async def cached_llm_call(prompt: str) -> str:
    cache_key = f"llm:{hash(prompt)}"
    
    # 检查缓存
    if cached := await redis_client.get(cache_key):
        return cached.decode()
    
    # 调用 LLM
    response = await llm.generate(prompt)
    
    # 写入缓存
    await redis_client.setex(cache_key, 3600, response)
    
    return response
```

---

## 6. 前端技术

### React + TypeScript

**选择理由**:
- 🏗️ 组件化架构
- 📦 丰富的生态
- ⚡ 高性能虚拟 DOM

### UI 框架: Tailwind CSS + shadcn/ui

**选择理由**:
- 🎨 高度可定制
- 📱 响应式设计
- 🚀 构建体积小
- ♿ 无障碍支持

**示例组件**:

```tsx
import { Button } from "@/components/ui/button"
import { Card } from "@/components/ui/card"

function TaskCard({ task }: { task: Task }) {
  return (
    <Card className="p-4 hover:shadow-lg transition-shadow">
      <h3 className="text-lg font-semibold">{task.title}</h3>
      <p className="text-gray-600 mt-2">{task.description}</p>
      <div className="flex gap-2 mt-4">
        <Button variant="default">执行</Button>
        <Button variant="outline">编辑</Button>
      </div>
    </Card>
  )
}
```

### 状态管理: Zustand

**选择理由**:
- 📦 轻量 (~1KB)
- 🔧 API 简洁
- ⚛️ 与 React 完美集成

```typescript
import { create } from 'zustand'

interface TaskStore {
  tasks: Task[]
  currentTask: Task | null
  addTask: (task: Task) => void
  setCurrentTask: (task: Task) => void
}

const useTaskStore = create<TaskStore>((set) => ({
  tasks: [],
  currentTask: null,
  addTask: (task) => set((state) => ({ 
    tasks: [...state.tasks, task] 
  })),
  setCurrentTask: (task) => set({ currentTask: task }),
}))
```

---

## 7. 开发工具

### 代码质量

| 工具 | 用途 | 配置文件 |
|------|------|----------|
| **Ruff** | Python Linter | `pyproject.toml` |
| **Black** | Python 格式化 | `pyproject.toml` |
| **mypy** | 类型检查 | `mypy.ini` |
| **ESLint** | JS/TS Linter | `.eslintrc.js` |
| **Prettier** | 代码格式化 | `.prettierrc` |

### 测试框架

| 工具 | 用途 |
|------|------|
| **pytest** | Python 单元测试 |
| **pytest-asyncio** | 异步测试支持 |
| **Vitest** | 前端测试 |
| **Playwright** | E2E 测试 |

### CI/CD

```yaml
# .github/workflows/ci.yml
name: CI

on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Setup Python
        uses: actions/setup-python@v5
        with:
          python-version: '3.11'
      
      - name: Install dependencies
        run: pip install -e ".[dev]"
      
      - name: Lint
        run: |
          ruff check .
          mypy .
      
      - name: Test
        run: pytest --cov
```

---

## 8. 部署方案

### Docker 容器化

```dockerfile
# Dockerfile
FROM python:3.11-slim

WORKDIR /app

# 安装依赖
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# 复制代码
COPY . .

# 启动
CMD ["uvicorn", "super_agent.main:app", "--host", "0.0.0.0", "--port", "8000"]
```

### Docker Compose (开发环境)

```yaml
# docker-compose.yml
version: '3.8'

services:
  api:
    build: .
    ports:
      - "8000:8000"
    environment:
      - DATABASE_URL=postgresql://user:pass@db:5432/superagent
      - REDIS_URL=redis://redis:6379
    depends_on:
      - db
      - redis
  
  db:
    image: postgres:16
    volumes:
      - postgres_data:/var/lib/postgresql/data
    environment:
      - POSTGRES_USER=user
      - POSTGRES_PASSWORD=pass
      - POSTGRES_DB=superagent
  
  redis:
    image: redis:7-alpine
    volumes:
      - redis_data:/data

volumes:
  postgres_data:
  redis_data:
```

### Kubernetes (生产环境)

- 使用 Helm Chart 管理部署
- 支持水平扩展
- 配置 HPA 自动伸缩

---

## 总结

SuperAgent 的技术选型遵循以下原则：

1. **务实优先**: 选择成熟稳定的技术
2. **可扩展性**: 支持水平扩展
3. **开发效率**: 提供良好的开发体验
4. **社区支持**: 选择活跃的开源项目
5. **性能考量**: 在关键路径上优化性能

技术选型会随着项目发展持续评估和调整。

