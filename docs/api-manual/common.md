---
sidebar_position: 1
---

# 通用

### 1. 概述

英智大模型推理API, 提供了与OpenAI API兼容的方式。 

主要包含: 请求的服务地址, 请求的路径和2种请求方式, 流式处理和非流式处理。


### 2. API BASE URL地址

https://api.baystoneai.cn

所有 API 端点都基于此 URL 进行调用，例如：

| 端点 | 说明 | 完整 URL |
|------|------|---------|
| `chat/completions` | 生成对话 | `https://api.baystoneai.cn/v1/chat/completions` |
| `completions` | 传统文本补全 | `https://api.baystoneai.cn/v1/completions` |
| `edits` | 文本编辑 | `https://api.baystoneai.cn/v1/edits` |
| `images/generations` | 生成图片 | `https://api.baystoneai.cn/v1/images/generations` |
| `models` | 获取可用模型列表 | `https://api.baystoneai.cn/v1/models` |

---


### 3. API-Key

登录官网在"我的控制台"获取

![api-key](./img/api-key.png)

所有 API 请求都需要使用 API Key 进行身份验证。  
在请求头 `Authorization` 中添加：
```http
Authorization: Bearer YOUR_API_KEY
```

### 4. 可用模型 

在官网首页可以查看所有可用模型
![models](./img/models.png)

### 5. 流式处理（Streaming）

流式处理是一种逐步返回数据的方式，适用于需要快速反馈和更流畅用户体验的场景。

#### 特点
- 逐步返回响应：模型会在生成内容时实时返回数据，而不是等到整个回答完成后再一次性返回。
- 更快的用户体验：用户可以更快地看到部分响应，而无需等待完整结果。
- 适用于实时交互：特别适用于聊天机器人、实时翻译等应用场景。
- 减少超时风险：对于长文本输出，流式处理可以降低因超时导致的请求失败。

#### 实现方式
在 API 请求中，使用 stream=true 参数即可启用流式处理 例如：

```python
import openai

API_BASE_URL = "https://api.baystoneai.cn"
API_KEY = "{YOUR_API_KEY}"

client = OpenAI(base_url=f"{API_BASE_URL}/v1", api_key=API_KEY)

response = client.chat.completions.create(
    model="llama-3.1-instruct",
    messages=[{"role": "user", "content": "Explain quantum mechanics"}],
    stream=True  # 启用流式处理
)

for chunk in response:
    print(chunk["choices"][0]["delta"].get("content", ""), end="")  # 持续输出数据

```

### 6. 非流式处理（Non-Streaming）

非流式处理是一次性返回完整数据的方式，适用于对响应时间要求不高的场景。

#### 特点
- 完整返回响应：模型会在生成完完整回答后才返回数据。
- 简单易用：适用于需要一次性获取完整结果的场景，例如批量处理任务、文档生成等。
- 可能有较高的延迟：对于长文本输出，等待时间较长，用户体验相对较慢。

```python
import openai

API_BASE_URL = "https://api.baystoneai.cn"
API_KEY = "{YOUR_API_KEY}"

client = OpenAI(base_url=f"{API_BASE_URL}/v1", api_key=API_KEY)

response = client.chat.completions.create(
    model="llama-3.1-instruct",
    messages=[{"role": "user", "content": "Explain quantum mechanics"}],
    stream=False  # 启用流式处理
)
print(response["choices"][0]["message"]["content"])  # 一次性输出完整结果
```
