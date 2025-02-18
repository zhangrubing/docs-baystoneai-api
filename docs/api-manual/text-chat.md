---
sidebar_position: 1
---

# 文本模型

### 1. 概述
API 提供了两种请求方式：
- **流式处理（Streaming）**：逐步返回数据，提高交互体验。
- **非流式处理（Non-Streaming）**：等待完整响应后一次性返回数据。

### 2. 认证
所有 API 请求都需要使用 API Key 进行身份验证。  
在请求头 `Authorization` 中添加：
```http
Authorization: Bearer YOUR_API_KEY
```

### 3. 创建对话请求

#### 3.1 请求参数说明

- 请求方法: POST
- 请求路径: `https://api.baystoneai.cn/v1/chat/completions`
- 请求头: 
```
Content-Type: application/json
Authorization: Bearer YOUR_API_KEY
```
请求参数

| 参数 | 类型 | 必填 | 默认值 | 说明 |
|------|------|------|------|------|
| `model` | `string` | 是 | 无 | 指定使用的模型，如 `"gpt-4"`, `"gpt-3.5-turbo"` 等 |
| `messages` | `array` | 是 | 无 | 聊天历史记录，包含 `role` 和 `content` |
| `temperature` | `float` | 否 | `1.0` | 控制输出的随机性，取值范围 `0~2`，值越高生成内容越有创造性 |
| `max_tokens` | `integer` | 否 | 无 | 限制模型生成的最大 token 数，防止输出过长 |
| `top_p` | `float` | 否 | `1.0` | 核采样参数，较低的值（如 0.1）会让模型更确定 |
| `frequency_penalty` | `float` | 否 | `0.0` | 频率惩罚，取值 `-2.0~2.0`，值越高减少重复词出现概率 |
| `presence_penalty` | `float` | 否 | `0.0` | 话题惩罚，取值 `-2.0~2.0`，值越高更倾向生成新话题内容 |
| `stop` | `string/array` | 否 | 无 | 设定一个或多个终止输出的标识符 |
| `stream` | `boolean` | 否 | `false` | 是否启用流式响应，`true` 逐步返回内容，`false` 一次性返回完整内容 |
| `logprobs` | `integer` | 否 | 无 | 返回 `logprobs` 最高的前 N 个 token 概率（最大支持 `5`） |
| `n` | `integer` | 否 | `1` | 生成多少个独立的回复（增加会消耗更多 token） |
| `user` | `string` | 否 | 无 | 用于用户标识，可用于 API 追踪 |

---

##### `messages` 参数结构

`messages` 需要包含一个 **对话历史** 数组，格式如下：
```json
"messages": [
  {"role": "system", "content": "You are a helpful assistant."},
  {"role": "user", "content": "What is the capital of France?"},
  {"role": "assistant", "content": "The capital of France is Paris."}
]
```
#### 3.2 返回参数说明

主参数说明:

| 参数 | 类型 | 说明 |
|------|------|------|
| `id` | `string` | 该请求的唯一标识符 |
| `object` | `string` | 响应对象类型，通常为 `"chat.completion"` |
| `created` | `integer` | Unix 时间戳，表示响应的生成时间 |
| `model` | `string` | 使用的模型名称（如 `"gpt-4"`） |
| `choices` | `array` | AI 生成的多个回答，每个 `choice` 代表一个可能的输出 |
| `usage` | `object` | Token 消耗统计信息 |

`choices` 参数
choices 数组包含 AI 生成的回答，每个元素结构如下：
```json
{
  "message": {"role": "assistant", "content": "Hello! How can I help you?"},
  "finish_reason": "stop",
  "index": 0
}

```

| 参数 | 类型 | 说明 |
|------|------|------|
| `index` | `integer` | 该回答在 `choices` 数组中的索引 |
| `message` | `object` | AI 生成的文本消息 |
| `message.role` | `string` | 角色标识，始终为 `"assistant"` |
| `message.content` | `string` | 生成的文本内容 |
| `finish_reason` | `string` | 结束原因（见下表） |

`finish_reason` 可能的值

| 值 | 说明 |
|----|------|
| `stop` | 生成完整，正常结束 |
| `length` | 达到 `max_tokens` 限制，强制截断 |
| `content_filter` | 生成的内容被 OpenAI 过滤 |
| `function_call` | 触发函数调用模式 |
| `tool_calls` | 触发工具调用模式 |

`usage` 参数
usage 统计了本次 API 请求消耗的 Token 数量：

```json
{
  "prompt_tokens": 10,
  "completion_tokens": 20,
  "total_tokens": 30
}

```

| 参数 | 类型 | 说明 |
|------|------|------|
| `prompt_tokens` | `integer` | 请求（输入）消耗的 token 数 |
| `completion_tokens` | `integer` | 生成（输出）消耗的 token 数 |
| `total_tokens` | `integer` | 本次请求的总 token 消耗（`prompt_tokens + completion_tokens`） |

常见错误代码

| HTTP 状态码 | 错误信息 | 可能原因 | 解决方案 |
|------------|---------|--------|---------|
| `400` | `Bad Request` | 请求参数错误，格式无效 | 检查请求 JSON 结构，确保参数正确 |
| `401` | `Unauthorized` | API Key 无效或未提供 | 确保 `Authorization` 头包含正确的 API Key |
| `403` | `Forbidden` | 没有权限访问该资源 | 确保 API Key 具有正确的访问权限 |
| `404` | `Not Found` | 请求的资源不存在 | 确保 URL 和端点正确 |
| `429` | `Too Many Requests` | 请求超出速率限制 | 降低请求频率或升级 API 订阅计划 |
| `500` | `Internal Server Error` | 服务器内部错误 | 尝试稍后重试 |
| `503` | `Service Unavailable` | OpenAI 服务器暂时不可用 | 可能是服务器维护，稍后重试 |

示例: API Key 无效
```json
{
  "error": {
    "message": "Invalid API key provided",
    "type": "invalid_request_error",
    "param": null,
    "code": "401"
  }
}
```

#### 3.3 调用示例


##### 3.3.1 非流式处理（Non-Streaming）

请求

```json
POST https://api.baystoneai.cn/v1/chat/completions
Content-Type: application/json
Authorization: Bearer YOUR_API_KEY

{
  "model": "DeepSeek-R1-Distill-Qwen-14B",
  "messages": [
    {"role": "system", "content": "You are a helpful assistant."},
    {"role": "user", "content": "Explain quantum mechanics in simple terms."}
  ],
  "temperature": 0.7
}
```

响应

```json
{
  "id": "chatcmpl-123",
  "object": "chat.completion",
  "created": 1677858242,
  "model": "DeepSeek-R1-Distill-Qwen-14B",
  "choices": [
    {
      "message": {"role": "assistant", "content": "Quantum mechanics is the study of..."},
      "finish_reason": "stop",
      "index": 0
    }
  ],
  "usage": {
    "prompt_tokens": 50,
    "completion_tokens": 150,
    "total_tokens": 200
  }
}
```

##### 3.3.2 流式处理（Streaming）

请求
```json
POST https://api.baystoneai.cn/v1/chat/completions
Content-Type: application/json
Authorization: Bearer YOUR_API_KEY

{
  "model": "DeepSeek-R1-Distill-Qwen-14B",
  "messages": [
    {"role": "system", "content": "You are a helpful assistant."},
    {"role": "user", "content": "Explain quantum mechanics in simple terms."}
  ],
  "stream": true
}

```

响应（部分数据）
```json
{
  "id": "chatcmpl-456",
  "object": "chat.completion.chunk",
  "created": 1677858243,
  "model": "DeepSeek-R1-Distill-Qwen-14B",
  "choices": [
    {
      "delta": {"content": "Quantum "},
      "finish_reason": null,
      "index": 0
    }
  ]
}


```

