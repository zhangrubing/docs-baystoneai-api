---
sidebar_position: 7
title: 在DB-GPT中使用 
---

### 1. 关于 DB-GPT

DB-GPT 是一个开源的 AI 原生数据应用开发框架，旨在通过集成大型语言模型（LLM）来增强与数据库的交互能力。该框架支持多模型管理、Text-to-SQL 优化、检索增强生成（RAG）框架、多智能体（Multi-Agents）协作以及智能体工作流表达式语言（AWEL）的开发。

### 2. 获取配置需要的参数

- 服务地址: https://api.baystoneai.cn/v1
- API-KEY: (登录后在管理台获取)  
- 模型: deepseek-r1-distill-qwen-14b

### 3. 部署 DB-GPT


#### 3.1 克隆 DB-GPT 源码

```bash
git clone https://github.com/eosphoros-ai/DB-GPT.git
```

#### 3.2 创建虚拟环境并安装依赖

```bash
# cd 到 DB-GPT 源码根目录
cd DB-GPT

# DB-GPT 要求python >= 3.10
conda create -n dbgpt_env python=3.10
conda activate dbgpt_env

# 这里选择代理模型类依赖安装
pip install -e ".[proxy]"

```

#### 3.3 配置基础的环境变量

```bash
# 复制模板 env 文件为 .env
cp .env.template .env
```


#### 3.4 修改环境变量文件.env，配置 Deepseek-R1 模型

```bash
# 配置具体使用的模型名称
BAYSTONEAI_MODEL_VERSION=deepseek-r1-distill-qwen
BAYSTONEAI_API_BASE=https://api.baystoneai.cn/v1
# 记得填写您在步骤2中获取的 API Key
BAYSTONEAI_API_KEY={API-KEY}
```

#### 3.5 在 DB-GPT 中配置模型：
在 DB-GPT 的配置文件中，添加对 DeepSeek-R1 模型的支持。

```bash
models:
  - name: deepseek-r1
    type: proxy
    provider: baystoneai
    model_name: ${BAYSTONEAI_MODEL_VERSION}
    api_base: ${BAYSTONEAI_API_BASE}
    api_key: ${BAYSTONEAI_API_KEY}
```

#### 3.6 启动 DB-GPT 服务

```
dbgpt start webserver --port 5670
```

在浏览器打开地址 http://127.0.0.1:5670/ 即可访问部署好的 DB-GPT


### 4.通过 DB-GPT Python SDK 使用 Deepseek-R1 的模型

#### 4.1 安装 DB-GPT Python 包

```bash
pip install "dbgpt>=0.6.3rc2" openai requests numpy
```
#### 4.2 使用 Deepseek-R1 的大语言模型

```python
import os
from dbgpt.core import ModelRequest
from dbgpt.model.proxy import ProxyLLMClient

# 从环境变量获取模型配置
model_name = os.getenv("BAYSTONEAI_MODEL_VERSION")
api_base = os.getenv("BAYSTONEAI_API_BASE")
api_key = os.getenv("BAYSTONEAI_API_KEY")

# 初始化客户端
client = ProxyLLMClient(
    api_base=api_base,
    api_key=api_key,
    model_name=model_name
)

# 创建请求
request = ModelRequest(
    model=model_name,
    messages=[
        {"role": "system", "content": "你是一个乐于助人的 AI 助手。"},
        {"role": "user", "content": "你好"}
    ]
)

# 发送请求并获取响应
response = client.generate(request)
print(response)

```

请确保您的环境中已安装 dbgpt 库，并正确配置了上述环境变量。

通过上述步骤，您即可在 DB-GPT 中集成并使用兼容 OpenAI API 的第三方模型 DeepSeek-R1。
