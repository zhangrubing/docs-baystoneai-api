---
sidebar_position: 13
---

# 在IEDA中使用



DeepSeek 大模型能力顶级，特别是 DeepSeek-R1 专为复杂推理任务设计，强化了数学、代码生成和逻辑推理领域性能，适用于科研、算法交易、代码生成等复杂任务。

今天，结合常用的编程工具 IDEA，介绍如何集成 DeepSeek 大模型的能力，搭建一个AI代码助手。



#### 第一步：适配 IDEA 版本

这里会使用插件 Proxy AI（CodeGPT），它是一个AI驱动的代码助手，能够辅助开发者的编程工作，已兼容了对包括IDEA (Ultimate, Community)在内的19种开发工具的支持。

插件信息详见：[https://plugins.jetbrains.com/plugin/21056-proxy-ai](https://plugins.jetbrains.com/plugin/21056-proxy-ai)

支持的 IDEA 版本系列如下：

![1](https://github.com/user-attachments/assets/76f57b52-36a2-439f-8336-d28e592318ec)

**确保 IDEA 版本为 2022.3+**，本文所用的 IDEA 版本为2024.1。



#### 第二步：安装 Proxy AI 插件

![2 1](https://github.com/user-attachments/assets/1ae420a6-a64d-4f4a-a34e-8b323a14e539)

![2 2](https://github.com/user-attachments/assets/60af77a4-aa7b-4f41-81b7-835c1327c816)

![2 3](https://github.com/user-attachments/assets/044cd4ca-7fe3-427f-bc17-490f4e788a87)



#### 第三步：配置 DeepSeek 模型

1. 申请 DeepSeek 免费 API Key

   英智未来旗下大模型API服务平台，已支持包括 deepseek-671b 在内的多个尺寸的 DeepSeek 大模型，申请地址：

   [https://api.baystoneai.com/](https://api.baystoneai.com/)

   API Key申请方法，请查阅英智公众号文章 [无需调试，2分钟快速接入 DeepSeek R1](https://mp.weixin.qq.com/s/QvUop_5VwRcf_vtUVDJ7tw)

2. 配置 DeepSeek 模型

   ![3 2 1](https://github.com/user-attachments/assets/92bae2cf-3d30-4562-8d8d-70b07a22996a)

   ![3 2 2](https://github.com/user-attachments/assets/dcedcf99-d79e-4144-83ad-2b8236641aa9)

   ![3 2 3](https://github.com/user-attachments/assets/92e8149b-7841-4dd6-a1e2-1730c9fdbbc2)

3. 启用 DeepSeek 模型

   ![3 3 1](https://github.com/user-attachments/assets/491e9ee3-6097-4525-8267-cd26d2b2e230)

   ![3 3 2](https://github.com/user-attachments/assets/0876d964-8306-4967-a889-a3ea1d072beb)



#### 第四步：开始使用 DeepSeek 模型

以下使用 DeepSeek 模型演示代码生成能力：

![4 1](https://github.com/user-attachments/assets/26529d21-40a3-4c3c-bc14-7e474f490f7c)

![4 2](https://github.com/user-attachments/assets/428d0049-f91b-4e25-8d26-4b3e65a9ea48)

![4 3](https://github.com/user-attachments/assets/f038c27f-1268-4298-a313-23dc504061da)

更多的应用场景，包括：问题解答、代码补全、代码解释等能力，大家可以自行探索。



