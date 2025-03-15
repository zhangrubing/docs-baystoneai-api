---
sidebar_position: 12
---
# 在VSCode中通过插件Roo Code使用



### 1.前言

AI 大模型正引发编程领域的变革，带领开发者步入全新的 coding 时代。对话式编程工具 Cursor 已融入开发者日常，尽管当前它只是代码辅助工具，但基于大模型的>

VS Code 界面简约却功能强大，堪称全球最受欢迎的代码编辑器之一。它支持 Windows、macOS 和 Linux 等多种操作系统，具备语法高亮、智能代码补全（IntelliSen>


### 2.关于插件Roo Code 


Roo Code是Cline的升级版，大部分基础功能都差不多，只不过Roo Code会更加强大一些。Roo Code 是一款集成在编辑器中的人工智能自主编码助手，功能强大且灵活[>
用户可通过自定义模式塑造其 “个性” 和功能，比如创建 QA 工程师、产品经理等不同角色的模式。它有多种交互模式，如默认的代码模式、架构师模式、询问模式，[>


### 3.安装插件Roo Code 

打开VS Code 的扩展页面 搜索"Roo Code"
然后看到如下界面，就是Roo Code了。
![alt text](./img/roo-1.png)

点击"安装"就可以完成安装了
![alt text](./img/roo-2.png)

### 4 申请英智DeepSeek 免费API KEY

进入[英智API服务网站](https://api.baystoneai.com) 注册登录并获取以下参数备用

- 服务地址: https://api.baystoneai.cn
- API-KEY: [打开获取API-KEY](https://api.baystoneai.com/manager/apiKey)
- 模型: deepseek-r1-distill-qwen-32b

详细步骤可以参考 [英智API快速上手](https://dsdocs.baystoneai.com/docs/start-use/quickstart)


### 5.配置Roo Code的AI服务

Roo Code配置会更加简单些。

***打开设置界面***

输入API-KEY, Base URL以及模型即可。
![alt text](./img/roo-3.png)

可以根据需求设置模型相关的参数，以及系统权限配置。

![alt text](./img/roo-4.png)

其他主要设置还包括对话语言设置-选择中文，系统prompt设置，给它一个默认身份，比如Python专家。

![alt text](./img/roo-5.png)


### 6.使用DeepSeek-R1模型

配置好后，就可以直接和DeepSeek对话来编程了

验证服务是否可以使用， 输入"你好" 然后回车，看到如下图有信息回复说明服务是好的。
![alt text](./img/roo-6.png)

咱们让DeepSeek写一个简单的计算器，使用Python来实现。

在对话框中输入"使用python来写一个简单的计算器，使用tkinter来实现，保证界面简洁美观，代码可执行。"

![alt text](./img/roo-7.png)


ROO CODE生成的代码可以直接执行，并且没有报错，计算器界面如下：

![alt text](./img/roo-8.png)

这个时候界面有部分按钮被遮挡了，继续对话修改。

输入"计算器运行的结果看 右边部分的按钮被遮挡了 说明计算器的宽度没有控制好， 需要修改一下"

正在自动修改代码
![alt text](./img/roo-9.png)

修改后的界面

![alt text](./img/roo-10.png)

最终完成的代码
```python
import tkinter as tk

def main():
    root = tk.Tk()
    root.title("简单计算器")
    root.geometry("420x400")  # 增加窗口宽度
    root.configure(bg="#f0f8ff")

    # 显示结果
    result = tk.Entry(root, font=('Arial', 20), width=15, bd=5, state='readonly')
    result.grid(row=0, column=0, columnspan=4, padx=10, pady=10)

    # 操作函数
    def click(num):
        current = result.get()
        result.configure(state='normal')
        result.delete(0, tk.END)
        result.insert(0, current + str(num))
        result.configure(state='readonly')

    def clear():
        result.configure(state='normal')
        result.delete(0, tk.END)
        result.insert(0, '0')
        result.configure(state='readonly')

    def calculate():
        try:
            current = result.get()
            result.configure(state='normal')
            result.delete(0, tk.END)
            result.insert(0, eval(current))
            result.configure(state='readonly')
        except:
            result.configure(state='normal')
            result.delete(0, tk.END)
            result.insert(0, '错误')
            result.configure(state='readonly')

    # 创建按钮
    buttons = [
        ('7', 1, 0), ('8', 1, 1), ('9', 1, 2), ('/', 1, 3),
        ('4', 2, 0), ('5', 2, 1), ('6', 2, 2), ('*', 2, 3),
        ('1', 3, 0), ('2', 3, 1), ('3', 3, 2), ('-', 3, 3),
        ('0', 4, 0), ('.', 4, 1), ('=', 4, 2), ('+', 4, 3)
    ]

   for (text, row, col) in buttons:
        if text == '=':
            btn = tk.Button(root, text=text, font=('Arial', 18), bg='lightblue', 
                            command=calculate, width=6, height=2)  # 增加按钮宽度
        else:
            btn = tk.Button(root, text=text, font=('Arial', 18), bg='white', 
                            command=lambda t=text: click(t), width=6, height=2)  # 增加按钮宽度
        btn.grid(row=row, column=col, padx=5, pady=5)

    # 清除按钮
    clear_btn = tk.Button(root, text='C', font=('Arial', 18), bg='lightblue', 
                          command=clear, width=6, height=2)
    clear_btn.grid(row=4, column=3, padx=5, pady=5)

    root.mainloop()

if __name__ == "__main__":
    main()
```


