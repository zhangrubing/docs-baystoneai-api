---
sidebar_position: 16
---

# 在WPS中使用



**横空出世的 DeepSeek，在自然语言处理方面表现出了卓越的能力，今天就来给大家详细介绍如何将 DeepSeek 接入 Word，让它成为文案创作的得力助手。**



#### 第一步：启用开发工具

打开Word文档，依次点击：【文件】>【更多...】>【选项】>【自定义功能区】，勾选【开发工具】。

![1](https://github.com/user-attachments/assets/1b7ee561-d229-4af6-a125-832de276d66f)



#### 第二步：启用宏命令

按如下操作启用宏命令：【文件】>【更多...】>【选项】>【信任中心】>【信任中心设置】>【宏设置】，点选【启用所有宏】。

![2](https://github.com/user-attachments/assets/4576cad2-bcce-457e-9323-d4c467dc9d4d)



#### 第三步：创建VB宏命令

1. 点击菜单【开发工具】下的VB编辑器【Visual Basic】。

   ![3 1](https://github.com/user-attachments/assets/5640f340-dfbe-4cb7-8484-1a271f5e6427)

2. 进入后选择菜单【插入】>【模块】，命名为“DeepSeek”。

   ![3 2](https://github.com/user-attachments/assets/252c0c04-e3af-4780-903f-236f8dff3497)

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

   ![3 4](https://github.com/user-attachments/assets/3ee42884-bb16-4721-ba1b-6f202f1f7eb6)

5. 保存VB脚本并关闭窗口；再依次点击：【文件】>【更多...】>【选项】>【自定义功能区】，选择这里的“宏”，即可看到刚才创建的宏命令。

   ![3 5 1](https://github.com/user-attachments/assets/a63255a7-357f-4d9f-8835-70656719e3d2)

   > 模版文件的"另存为"路径为：
   >
   > C:\Users\你的电脑用户名\AppData\Roaming\Microsoft\Word\STARTUP

   ![3 5 2](https://github.com/user-attachments/assets/340533a3-d031-4d73-b623-292b9f08ed5f)



#### 第四步：新建宏命令组

1. 在右侧继续右键【开发工具】，在它下面【添加新组】，并可重命名这个组。

   ![4 1 1](https://github.com/user-attachments/assets/885398db-4ddb-426f-8b63-12a8e76e842c)

   ![4 1 2](https://github.com/user-attachments/assets/726fe4a0-ff18-452f-a44a-cb3bcb50cc7e)

   添加的新组如下：

   ![4 1 3](https://github.com/user-attachments/assets/4f57013c-3b90-4cf5-a503-acee910d64b1)

2. 将第三步创建的宏命令添加到这个组下面。

   ![4 2 1](https://github.com/user-attachments/assets/7ed7c252-99ad-4a12-b9c6-fc7d55bf2923)

   在菜单【开发工具】中就可以看到这个按钮：

   ![4 2 2](https://github.com/user-attachments/assets/d0e6cfbb-b990-4b01-abbf-6ab6ef4fd564)



#### 第五步：开始创作吧

在Word文档中测试一下这个功能，输入一段文字，点击新建的功能按钮。

```
# 输入文字
  请写一篇300字的有关AIGC发展前景的科技文章。
```

![5](https://github.com/user-attachments/assets/55a7c3fa-3efa-4a27-a4f4-bf4bf993b1da)

```
# 输出说明
  <think>...</think>为 deepseek-r1 模型输出的思维链内容。也可以通过改写VB宏命令脚本控制不输出，有兴趣的朋友可以尝试优化。
```

之后，内容扩写、检查错别字、翻译、智能问答等等需求都可以直接在Word里完成。是不是很方便？


