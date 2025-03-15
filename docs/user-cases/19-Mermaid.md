---
sidebar_position: 19
---

# 在Mermaid中使用



把 DeepSeek + Mermaid 两种利器组合起来使用，让你制作流程图、时序图、饼图等等图表都能信手拈来！

接下来的教学中，我们将演示具体的操作流程，用到的工具：

- **DeepSeek + Word**：接入了DeepSeek大模型的Word神器，用简单语言描述就能生成图表的设计蓝图
- **Mermaid**：一款优秀的图表绘制工具，支持在线编辑和实时生成多达20多种图表效果



> Word集成DeepSeek大模型的详细配置流程，请参考：
>
> [手把手教你用DeepSeek+XMind秒变思维导图高手](https://mp.weixin.qq.com/s/bNSoXfVC1h9NyaeveOGzNA)



#### 第一步：DeepSeek生成图表脚本

1、在Word文档中输入一段需求描述文字，选定文字，再点击DeepSeek功能按钮。

```
# 输入文字
  我想画一个跨境电商网站交易的流程图，请用Mermaid语法格式输出
```

![1 1](https://github.com/user-attachments/assets/11b604d8-5dd6-49ef-bfc9-4b1ff893bb63)

```
# 输出说明
  <think>...</think>之间为 deepseek-r1 模型输出的思维链内容。也可以通过改写VB宏命令脚本控制不输出，有兴趣的同学可以尝试优化。
```

2、复制DeepSeek生成的图表脚本。

![1 2](https://github.com/user-attachments/assets/7382937f-ed4e-45ad-93d5-68f50adac84c)



#### 第二步：Mermaid一键生成图表

1、打开Mermaid在线编辑器。

```
# 在线编辑器
  https://mermaid-live.nodejs.cn/
```

![2 1](https://github.com/user-attachments/assets/16ec025d-2354-4b8d-99cb-3f21ed44a405)

2、一键生成图表。

![2 2](https://github.com/user-attachments/assets/6c8760e5-d9e9-4d1d-a769-d676dd7ea95e)

3、操作图表。

![2 3](https://github.com/user-attachments/assets/1960ea33-caff-40e2-ab7f-a61614c9fd10)



