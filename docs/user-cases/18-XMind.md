---
sidebar_position: 18
---

# 在XMind中使用



把 DeepSeek + Xmind 两种利器组合起来使用，让制作思维导图变得轻而易举！

接下来的教学中，我们将演示具体的操作流程，用到的工具：

- **DeepSeek + Word**：接入了 DeepSeek 大模型的Word神器
- **XMind**：最常用的思维导图和头脑风暴软件



> 让我们先回顾一下 DeepSeek 接入Word的操作流程

#### 第一步：启用开发工具

打开Word文档，依次点击：【文件】>【更多...】>【选项】>【自定义功能区】，勾选【开发工具】。

![1](https://github.com/user-attachments/assets/946afe14-0cda-43e6-a364-a2362587ccb4)

#### 第二步：启用宏命令

按如下操作启用宏命令：【文件】>【更多...】>【选项】>【信任中心】>【信任中心设置】>【宏设置】，点选【启用所有宏】。

![2](https://github.com/user-attachments/assets/139b9202-11e7-4154-ab64-cffde41891b4)

#### 第三步：创建VB宏命令

1. 点击菜单【开发工具】下的VB编辑器【Visual Basic】。

   ![3 1](https://github.com/user-attachments/assets/88c99716-a6f5-4530-9a9b-e12febc16122)

2. 进入后选择菜单【插入】>【模块】，命名为“DeepSeek”。

   ![3 2](https://github.com/user-attachments/assets/3fd0378c-bce2-4589-be41-c3e7a06a457c)

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

4. 其中的 API Key， 申请地址：[https://api.baystoneai.com](https://api.baystoneai.com/)，申请攻略可以查阅公众号文章“[无需调试，2分钟快速接入 DeepSeek R1](https://mp.weixin.qq.com/s/QvUop_5VwRcf_vtUVDJ7tw)”。申请完成后，替换这里的YOUR_API_KEY。

   ![3 4](https://github.com/user-attachments/assets/06eb4e04-b3a2-4a8b-b1a2-d48966da79d2)

5. 保存VB脚本并关闭窗口；再依次点击：【文件】>【更多...】>【选项】>【自定义功能区】，选择这里的“宏”，即可看到刚才创建的宏命令。

   ![3 5 1](https://github.com/user-attachments/assets/e0770e68-f941-439a-bc3c-1a7ec200c69a)

   > 模版文件的"另存为"路径为：
   >
   > C:\Users\你的电脑用户名\AppData\Roaming\Microsoft\Word\STARTUP

   ![3 5 2](https://github.com/user-attachments/assets/cd827948-1978-4fb7-94e1-a10f94fd673b)


#### 第四步：新建宏命令组

1. 在右侧继续右键【开发工具】，在它下面【添加新组】，并可重命名这个组。

   ![4 1 1](https://github.com/user-attachments/assets/651d0343-8a46-4cb6-a206-a15a0365225b)

   ![4 1 2](https://github.com/user-attachments/assets/4345bdcf-4ca5-4a72-973a-8db036e3141a)

   添加的新组如下：

   ![4 1 3](https://github.com/user-attachments/assets/9e419e53-b30d-4a79-b981-74b8b1ece579)

2. 将第三步创建的宏命令添加到这个组下面。

   ![4 2 1](https://github.com/user-attachments/assets/2228eb2d-306c-42f9-9fbc-9ec3dcd0762c)

   在菜单【开发工具】中就可以看到这个按钮：

   ![4 2 2](https://github.com/user-attachments/assets/6b93be2d-fcd3-4e52-bd66-5dc13d4c85b4)



> 用 DeepSeek + Word 生成方案提纲

#### 第五步：DeepSeek生成方案提纲

1. 在Word文档中输入一段文字，选定文字，再点击新建的功能按钮。

   ```
   # 输入文字
     你是一名宠物用品的跨境网站运营，请整理一份【宠物用品跨境电商网站调研方案】的思维导图大纲，要求全面列出需要调研的内容板块、以及对应的事项。生成内容以MarkDown代码格式输出
   ```

   ![5 1 1](https://github.com/user-attachments/assets/ce8bc6b1-9204-4df0-93ae-6ef43031ee7e)

   ![5 1 2](https://github.com/user-attachments/assets/88e0a25a-0bfc-439c-bcf6-122a199d8b49)

   ```
   # 输出说明
     <think>...</think>之间为 deepseek-r1 模型输出的思维链内容。也可以通过改写VB宏命令脚本控制不输出，有兴趣的同学可以尝试优化。
   ```

2. 将DeepSeek生成的方案内容，复制出来到记事本，保存为Markdown文件（*.md）。

   ```
   # Markdown文件名
     宠物用品跨境电商网站调研方案.md
   ```



> 用 XMind 生成思维导图

#### 第六步：XMind一键生成思维导图

1. 打开XMind，【新建】的模版主题任选。

   ![6 1](https://github.com/user-attachments/assets/0aca97c7-a1db-4aa8-8eea-571f78bee100)

2. 依次点击：【文件】>【导入】>【Markdown】，选择第五步保存的Markdown文件，即可一键生成思维导图。

   ![6 2](https://github.com/user-attachments/assets/1a7d985a-9759-4000-b4c8-30044cfddfc9)



