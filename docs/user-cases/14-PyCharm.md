---
sidebar_position: 14
---

# 在PyCharm中使用



PyCharm 是一款非常常用的Python集成开发环境（IDE）。

DeepSeek 大模型能力顶级，特别是 DeepSeek-R1 专为复杂推理任务设计，强化了数学、代码生成和逻辑推理领域性能，适用于科研、算法交易、代码生成等复杂任务。

今天，手把手的教给大家如何在 PyCharm 中集成 DeepSeek 大模型的能力，搭建一个AI代码助手，让编程更轻松、更高效。



#### 第一步：适配 PyCharm 版本

这里会使用插件 Proxy AI（CodeGPT），它是一个AI驱动的代码助手，能够辅助开发者的编程工作，已兼容了对包括PyCharm (Professional, Community)在内的19种开发工具的支持。

插件信息详见：[https://plugins.jetbrains.com/plugin/21056-proxy-ai](https://plugins.jetbrains.com/plugin/21056-proxy-ai)

支持的 PyCharm 版本系列如下：

![1](https://github.com/user-attachments/assets/15e266c0-fa57-4e98-9eae-ab3e41c1cfbf)

**确保 PyCharm 版本为 2022.3+**，本文所用的 PyCharm 版本为2024.1。



#### 第二步：安装 Proxy AI 插件

![2 1](https://github.com/user-attachments/assets/4621518d-61a3-43af-8429-80d180077c0c)

![2 2](https://github.com/user-attachments/assets/f2693308-3def-4a4e-a245-5fff91cb5a87)

![2 3](https://github.com/user-attachments/assets/c4bcf269-325c-4082-acba-2d405c1f5618)



#### 第三步：配置 DeepSeek 模型

1. 申请 DeepSeek 免费 API Key

   英智未来旗下大模型API服务平台，已支持包括 deepseek-671b 在内的多个尺寸的 DeepSeek 大模型，申请地址：

   [https://api.baystoneai.com/](https://api.baystoneai.com/)

   API Key申请方法，请查阅英智公众号文章 [无需调试，2分钟快速接入 DeepSeek R1](https://mp.weixin.qq.com/s/QvUop_5VwRcf_vtUVDJ7tw)

2. 配置 DeepSeek 模型

   ![3 2 1](https://github.com/user-attachments/assets/901d0eea-b1f2-4cc2-b7d9-05466aa1f809)

   ![3 2 2](https://github.com/user-attachments/assets/3b29c655-df10-4454-b1d6-122b137203ea)

   ![3 2 3](https://github.com/user-attachments/assets/6f140344-5819-4a4e-a500-3624b64d4392)

3. 启用 DeepSeek 模型

   ![3 3 1](https://github.com/user-attachments/assets/693b2a3e-27dd-4a6d-be4c-2ccfdeb3f3de)

   ![3 3 2](https://github.com/user-attachments/assets/4c1036a3-59da-4a2b-b775-7b592b8b7a9c)



#### 第四步：开始使用 DeepSeek 模型

以下使用 DeepSeek 模型演示代码生成能力：

![4 1](https://github.com/user-attachments/assets/84a6333b-c6ac-4766-8df6-c1a443f3312d)

![4 2](https://github.com/user-attachments/assets/6146a476-d03a-4d99-bf51-908b6910f08d)

![4 3](https://github.com/user-attachments/assets/0cab04ef-446e-4118-95f5-238885d9e2b8)

更多的应用场景，包括：问题解答、代码补全、代码解释等能力，大家可以自行探索。



