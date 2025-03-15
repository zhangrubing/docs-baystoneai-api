---
sidebar_position: 20
---

# 在Draw.io中使用



把 DeepSeek + Draw.io 两种利器组合起来使用，让你在线绘制流程图、思维导图、网络拓扑图、UML图、ER图、组织结构图、泳道图、文氏图、电路图等等图表都能信手拈来！

接下来的教学中，我们将演示具体的操作流程，用到的工具：

- **DeepSeek + Word**：接入了DeepSeek大模型的Word神器，用简单语言描述就能生成图表的设计蓝图
- **Draw.io**：一款免费、无需注册登录、强大且支持在线编辑和导出的图表绘制神器



> Word集成DeepSeek大模型的详细配置流程，请参考：
>
> [手把手教你用DeepSeek+XMind秒变思维导图高手](https://mp.weixin.qq.com/s/bNSoXfVC1h9NyaeveOGzNA)



#### 第一步：DeepSeek生成图表脚本

1、在Word文档中输入一段需求描述文字，选定文字，再点击DeepSeek功能按钮。

```
# 输入文字
  我想画一个跨境电商网站交易的流程图，请用Mermaid语法格式输出
```

![1 1](https://github.com/user-attachments/assets/db310fb5-327b-4305-8697-023e8fad7d0f)

```
# 输出说明
  <think>...</think>之间为 deepseek-r1 模型输出的思维链内容。也可以通过改写VB宏命令脚本控制不输出，有兴趣的同学可以尝试优化。
```

2、复制DeepSeek生成的图表脚本。

![1 2](https://github.com/user-attachments/assets/4515095a-a0d9-444e-891b-bf2670b4bf02)



#### 第二步：Draw.io一键生成图表

1、打开Draw.io在线编辑器，依次点击快捷菜单栏的：【+】>【高级】>【Mermaid】。

```
# 在线编辑器
  http://draw.io
```

![2 1](https://github.com/user-attachments/assets/da09c00e-1dbc-4d2b-b65a-653dfae956a1)

2、一键生成图表。

![image-20250305163648717](C:\Users\JetLee\Desktop\DeepSeek\宣传文案\18. DeepSeek+Draw.io\2.2.png)

3、操作图表。

![image-20250305163954345](C:\Users\JetLee\Desktop\DeepSeek\宣传文案\18. DeepSeek+Draw.io\2.3.png)



