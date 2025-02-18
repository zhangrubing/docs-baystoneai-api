---
sidebar_position: 1
---
# 推理模型

### 1. 概述

DeepSeek-R1 是 deepseek-ai 推出的一系列高性能语言模型，专注于通过生成思维链内容（reasoning_content）来优化答案的准确性和逻辑性。该模型接口兼容 deepseek 生态，为无缝集成提供支持。为确保最佳使用体验，建议在部署前升级 OpenAI SDK，以适配新增参数和功能。

#### 支持模型列表

- DeepSeek-R1-671B                
- DeepSeek-V3-671B                
- DeepSeek-R1-Distill-Llama-70B   
- DeepSeek-R1-Distill-Qwen-32B    
- DeepSeek-R1-Distill-Qwen-14B    
- DeepSeek-R1-Distill-Llama-8B    
- DeepSeek-R1-Distill-Qwen-7B     
- DeepSeek-R1-Distill-Qwen-1.5B   
- Qwen2.5-instruct-14B

### 2. 安装与升级

在部署使用 DeepSeek-R1 之前，请确保已安装最新版本的 OpenAI SDK。可以通过以下命令进行升级：

```bash
pip3 install -U openai
```

### 3. API 参数

- **输入参数**：
  - `max_tokens`：回答的最大长度（包含思维链输出）。其中，`deepseek-ai/DeepSeek-R1` 和 `Pro/deepseek-ai/DeepSeek-R1` 的 `max_tokens` 最大为 8K，其他模型最大为 16K。

- **返回参数**：
  - `reasoning_content`：思维链内容，与 `content` 同级。
  - `content`：最终回答内容。

### 4. 上下文拼接

在每一轮对话过程中，模型会输出思维链内容（`reasoning_content`）和最终回答（`content`）。在下一轮对话中，之前轮次输出的思维链内容不会被拼接到上下文中。

### 5. Python 请求示例

#### 流式输出请求

```python
from openai import OpenAI

API_BASE_URL = "https://api.baystoneai.cn"
API_KEY = "{YOUR_API_KEY}"

client = OpenAI(base_url=f"{API_BASE_URL}/v1", api_key=API_KEY)

# 控制是否使用流式输出
isStream = True

response = client.chat.completions.create(
    model="DeepSeek-R1-Distill-Qwen-14B",
    messages=[
        {"role": "user", "content": "你是谁"}
    ],
    stream=isStream
)

if isStream:
    # 流式输出处理
    for chunk in response:
        # 打印完整的 chunk，方便调试
        # print(chunk)

        # 检查 chunk.choices 是否有内容
        if hasattr(chunk, 'choices') and len(chunk.choices) > 0:
            # 访问 choices[0].delta.content
            delta = chunk.choices[0].delta
            if hasattr(delta, 'content') and delta.content is not None:
                # 打印内容到终端
                print(delta.content, end='', flush=True)
    print()  # 输出完成后换行
else:
    # 非流式输出处理
    print(response.choices[0].message.content)
```

#### 非流式输出请求

```python
from openai import OpenAI

API_BASE_URL = "https://api.baystoneai.cn"
API_KEY = "{YOUR_API_KEY}"

client = OpenAI(base_url=f"{API_BASE_URL}/v1", api_key=API_KEY)

# 控制是否使用流式输出
isStream = false

response = client.chat.completions.create(
    model="DeepSeek-R1-Distill-Qwen-14B",
    messages=[
        {"role": "user", "content": "你是谁"}
    ],
    stream=isStream
)

if isStream:
    # 流式输出处理
    for chunk in response:
        # 打印完整的 chunk，方便调试
        # print(chunk)

        # 检查 chunk.choices 是否有内容
        if hasattr(chunk, 'choices') and len(chunk.choices) > 0:
            # 访问 choices[0].delta.content
            delta = chunk.choices[0].delta
            if hasattr(delta, 'content') and delta.content is not None:
                # 打印内容到终端
                print(delta.content, end='', flush=True)
    print()  # 输出完成后换行
else:
    # 非流式输出处理
    print(response.choices[0].message.content)
```

### 6. 注意事项
- API 密钥：请确保使用正确的 API 密钥进行身份验证。
- 流式输出：流式输出适用于需要逐步接收响应的场景，而非流式输出则适用于一次性获取完整响应的场景。