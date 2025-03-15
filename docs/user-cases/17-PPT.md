---
sidebar_position: 17
---

# 在PPT中使用



之前的教学演示中，我们将 DeepSeek 接入Word，打造了一个个人专属的智能写作神器。在日常工作中，也会有大量的将创作文案生成PPT的需求。

今天，接着给大家演示如何在Word中使用 DeepSeek 大模型快速撰写PPT大纲，再借助 Kimi 一键制作PPT的操作实践。

需要用到：

- **DeepSeek + Word**：负责撰写PPT提纲
- **Kimi**：负责根据提纲润色PPT的内容以及制作PPT



> 让我们先回顾一下 DeepSeek 接入Word的操作流程

#### 第一步：启用开发工具

打开Word文档，依次点击：【文件】>【更多...】>【选项】>【自定义功能区】，勾选【开发工具】。

![1](https://github.com/user-attachments/assets/01481119-5e9e-47bf-9cea-0ba4c8173ba5)

#### 第二步：启用宏命令

按如下操作启用宏命令：【文件】>【更多...】>【选项】>【信任中心】>【信任中心设置】>【宏设置】，点选【启用所有宏】。

![2](https://github.com/user-attachments/assets/8d7752c9-440c-4b2a-9059-91737f6b2167)

#### 第三步：创建VB宏命令

1. 点击菜单【开发工具】下的VB编辑器【Visual Basic】。

   ![3 1](https://github.com/user-attachments/assets/3c0cd83e-6c45-4283-9f3d-180dda9030a8)

2. 进入后选择菜单【插入】>【模块】，命名为“DeepSeek”。

   ![3 2](https://github.com/user-attachments/assets/c9833fcb-3c9e-4ade-902d-0b7a2b470319)

3. 复制下面的代码到DeepSeek模块中。

   ```visual basic
   Function CallDeepSeekAPI(inputText As String) As String
     Dim strAPI As String
     Dim strAPIKey As String
     Dim strSendTxt As String
     Dim objHttp As Object
     Dim intStatusCode As Integer
     Dim strResponse As String
     
     ' API服务地址
     strAPI = "https://api.baystoneai.cn/v1/chat/completions"
     
     ' API密钥：替换为你在API服务平台申请的API Key
     strAPIKey = "YOUR_API_KEY"
     If strAPIKey = "" Then
       MsgBox "Please enter the API key."
       Exit Function
     ElseIf Selection.Type <> wdSelectionNormal Then
       MsgBox "Please select text."
       Exit Function
     End If
     
     ' API模型名称
     strSendTxt = "{""model"": ""deepseek-r1-distill-qwen-32b"", " & vbCrLf & _
                """messages"": [" & vbCrLf & _
                "   {""role"": ""system"", ""content"": ""You are a helpful assistant.""}, " & vbCrLf & _
                "   {""role"": ""user"", ""content"": """ & inputText & """}" & vbCrLf & _
                "], " & vbCrLf & _
                """max_tokens"": 8192, " & vbCrLf & _
                """stream"": false" & vbCrLf & _
                "}"
     
     Set objHttp = CreateObject("MSXML2.XMLHTTP")
     ' 调试用：弹出窗口显示发送报文
     ' MsgBox strSendTxt, vbInformation, "Debug Info"
     With objHttp
       .Open "POST", strAPI, False
       .setRequestHeader "Content-Type", "application/json"
       .setRequestHeader "Authorization", "Bearer " & strAPIKey
       .send strSendTxt
       intStatusCode = .Status
       strResponse = .responseText
     End With
    
     ' 调试用: 弹出窗口显示 API 的响应
     ' MsgBox "API Response: " & strResponse, vbInformation, "Debug Info"
    
     If intStatusCode = 200 Then
       CallDeepSeekAPI = strResponse
     Else
       CallDeepSeekAPI = "Error: " & intStatusCode & " - " & strResponse
     End If
    
     Set objHttp = Nothing
   End Function
   
   
   Sub DeepSeekR1()
     Dim strInputText As String
     Dim strResponse As String
     Dim objRegex As Object
     Dim objReasoningRegex As Object
     Dim objContentRegex As Object
     Dim objMatches As Object
     Dim objReasoningMatches As Object
     Dim objOriginalSelection As Object
     Dim strReasoningContent As String
     Dim strFinalContent As String
    
     ' 保存原始选中的文本
     Set objOriginalSelection = Selection.Range.Duplicate
     
     strInputText = Replace(Replace(Replace(Replace(Replace(Selection.Text, "\", "\\"), vbCrLf, ""), vbCr, ""), vbLf, ""), Chr(34), "\""")
     strResponse = CallDeepSeekAPI(strInputText)
     
     If Left(strResponse, 5) <> "Error" Then
       ' 创建正则表达式对象来分别匹配推理内容和最终回答
       Set objReasoningRegex = CreateObject("VBScript.RegExp")
       With objReasoningRegex
         .Global = True
         .MultiLine = True
         .IgnoreCase = False
         .Pattern = """reasoning_content"":""(.*?)"""
       End With
       
       Set objContentRegex = CreateObject("VBScript.RegExp")
       With objContentRegex
         .Global = True
         .MultiLine = True
         .IgnoreCase = False
         .Pattern = """content"":""(.*?)"""
       End With
    
       ' 提取推理内容
       Set objReasoningMatches = objReasoningRegex.Execute(strResponse)
       If objReasoningMatches.Count > 0 Then
         strReasoningContent = objReasoningMatches(0).SubMatches(0)
         strReasoningContent = Replace(strReasoningContent, "\n\n", vbNewLine)
         strReasoningContent = Replace(strReasoningContent, "\n", vbNewLine)
         strReasoningContent = Replace(Replace(strReasoningContent, """", Chr(34)), """", Chr(34))
       End If
    
       ' 提取最终回答
       Set objMatches = objContentRegex.Execute(strResponse)
       If objMatches.Count > 0 Then
         strFinalContent = objMatches(0).SubMatches(0)
         strFinalContent = Replace(strFinalContent, "\n\n", vbNewLine)
         strFinalContent = Replace(strFinalContent, "\n", vbNewLine)
         strFinalContent = Replace(Replace(strFinalContent, """", Chr(34)), """", Chr(34))
    
         ' 取消选中原始文本
         Selection.Collapse Direction:=wdCollapseEnd
    
         ' 插入推理过程（如果存在）
         If Len(strReasoningContent) > 0 Then
           Selection.TypeParagraph
           Selection.TypeText "推理过程："
           Selection.TypeParagraph
           Selection.TypeText strReasoningContent
           Selection.TypeParagraph
           Selection.TypeText "最终回答："
           Selection.TypeParagraph
         End If
    
         ' 插入最终回答
         Selection.TypeText strFinalContent
    
         ' 将光标移回原来选中文本的末尾
         objOriginalSelection.Select
       Else
         MsgBox "Failed to parse API response.", vbExclamation
       End If
     Else
       MsgBox strResponse, vbCritical
     End If
   End Sub
   ```

   > 代码说明：
   >
   > 以下3处需要关注。其中第2处，要注意替换修改YOUR_API_KEY为申请的API Key

   ```
   # 第1处 API服务地址
     strAPI = "https://api.baystoneai.cn/v1/chat/completions"
   
   # 第2处 API密钥：替换为你在API服务平台申请的API Key，下一章节有申请攻略
     strAPIKey = "YOUR_API_KEY"
   
   # 第3处 API模型名称：其中"deepseek-r1-distill-qwen-32b"为模型名称，可替换为API服务平台支持的deepseek的不同版本
     strSendTxt = "{""model"": ""deepseek-r1-distill-qwen-32b"", " & vbCrLf & _
   ```

4. 其中的 API Key， 申请地址：[https://api.baystoneai.com/](https://api.baystoneai.com/)，申请攻略可以查阅公众号文章“[无需调试，2分钟快速接入 DeepSeek R1](https://mp.weixin.qq.com/s/QvUop_5VwRcf_vtUVDJ7tw)”。申请完成后，替换这里的YOUR_API_KEY。

   ![3 4](https://github.com/user-attachments/assets/c2ca2358-427e-44d1-9370-571ee15fc8c4)

5. 保存VB脚本并关闭窗口；再依次点击：【文件】>【更多...】>【选项】>【自定义功能区】，选择这里的“宏”，即可看到刚才创建的宏命令。

   ![3 5 1](https://github.com/user-attachments/assets/ce97a210-0f6a-455e-acb1-042dd984db81)

   > 模版文件的"另存为"路径为：
   >
   > C:\Users\你的电脑用户名\AppData\Roaming\Microsoft\Word\STARTUP

   ![3 5 2](https://github.com/user-attachments/assets/b43b168e-1530-42d1-9674-b4b4c5ce5b2c)


#### 第四步：新建宏命令组

1. 在右侧继续右键【开发工具】，在它下面【添加新组】，并可重命名这个组。

   ![4 1 1](https://github.com/user-attachments/assets/54c22739-80b5-4ddb-b006-3c9869ea8adf)

   ![4 1 2](https://github.com/user-attachments/assets/1f08a295-e8a0-4908-8181-e2a6e969c3cc)

   添加的新组如下：

   ![4 1 3](https://github.com/user-attachments/assets/542925d6-9f9c-450d-9895-ae626bf877c6)


2. 将第三步创建的宏命令添加到这个组下面。

   ![4 2 1](https://github.com/user-attachments/assets/396ce53c-bf59-43cf-9838-855dd16ee395)

   在菜单【开发工具】中就可以看到这个按钮：

   ![4 2 2](https://github.com/user-attachments/assets/3d096655-04ed-47ce-8e40-0d66c9bba9b8)



> 用 DeepSeek + Word 生成PPT提纲

#### 第五步：DeepSeek生成PPT提纲

在Word文档中输入一段文字，选定文字，再点击新建的功能按钮。

```
# 输入文字
  写一份关于《桃花源记》的跨学科(语文+思政)教学设计PPT框架
```

![5 1](https://github.com/user-attachments/assets/66367533-c480-4c06-960d-57d0d9ecd904)

![5 2](https://github.com/user-attachments/assets/1d6a028c-32a6-4999-b567-a352601017eb)

```
# 输出说明
  <think>...</think>之间为 deepseek-r1 模型输出的思维链内容。也可以通过改写VB宏命令脚本控制不输出，有兴趣的朋友可以尝试优化。
```



> 用 Kimi 制作PPT

#### 第六步：Kimi一键生成PPT

1. 打开Kimi+的PPT助手

   ![6 1](https://github.com/user-attachments/assets/f9a3522c-6e4e-47a9-81f5-50a67ad7d718)

2. 复制DeepSeek生成的提纲（正文内容）发给PPT助手

   ![6 2](https://github.com/user-attachments/assets/3ad22f1f-cbf3-47ad-ab02-2ac8de43a154)


3. 一键生成PPT

   ![6 3 1](https://github.com/user-attachments/assets/d6619217-3105-4c0f-b986-b49f8dd98fc1)

   ![6 3 2](https://github.com/user-attachments/assets/12c780a3-80ab-4ec9-97b2-97f3dfd19340)

   ![6 3 3](https://github.com/user-attachments/assets/b1840df3-bdb5-40f9-ab11-5dc7034164e1)

   ![6 3 4](https://github.com/user-attachments/assets/c5b4a989-3c2c-4a92-9ebd-deae697e8903)



