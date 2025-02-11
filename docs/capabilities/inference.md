---
sidebar_position: 1
---
# 推理模型

## 概述

DeepSeek-R1 是由 deepseek-ai 开发的一系列高级语言模型，旨在通过输出思维链内容（`reasoning_content`）来提升最终回答的准确性。目前，该接口与 deepseek 接口兼容。使用该模型时，建议先升级 OpenAI SDK 以支持新参数。

### 支持模型列表

- `deepseek-ai/DeepSeek-R1`
- `Pro/deepseek-ai/DeepSeek-R1`
- `deepseek-ai/DeepSeek-R1-Distill-Llama-70B`
- `deepseek-ai/DeepSeek-R1-Distill-Qwen-32B`
- `deepseek-ai/DeepSeek-R1-Distill-Qwen-14B`
- `deepseek-ai/DeepSeek-R1-Distill-Llama-8B`
- `deepseek-ai/DeepSeek-R1-Distill-Qwen-7B`
- `deepseek-ai/DeepSeek-R1-Distill-Qwen-1.5B`
- `Pro/deepseek-ai/DeepSeek-R1-Distill-Llama-8B`
- `Pro/deepseek-ai/DeepSeek-R1-Distill-Qwen-7B`
- `Pro/deepseek-ai/DeepSeek-R1-Distill-Qwen-1.5B`

## 安装与升级

在使用 DeepSeek-R1 之前，请确保已安装最新版本的 OpenAI SDK。可以通过以下命令进行升级：

```bash
pip3 install -U openai
```
## API 参数

- **输入参数**：
  - `max_tokens`：回答的最大长度（包含思维链输出）。其中，`deepseek-ai/DeepSeek-R1` 和 `Pro/deepseek-ai/DeepSeek-R1` 的 `max_tokens` 最大为 8K，其他模型最大为 16K。

- **返回参数**：
  - `reasoning_content`：思维链内容，与 `content` 同级。
  - `content`：最终回答内容。

## 上下文拼接

在每一轮对话过程中，模型会输出思维链内容（`reasoning_content`）和最终回答（`content`）。在下一轮对话中，之前轮次输出的思维链内容不会被拼接到上下文中。

## OpenAI 请求示例

### 流式输出请求

```python
```
