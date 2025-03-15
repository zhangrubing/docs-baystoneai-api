---
sidebar_position: 16
---

# 在WPS中使用



今天，接着给大家详细介绍如何将 DeepSeek 接入 WPS，打造你的AI创作助手。



#### 第一步：启用开发工具

打开WPS文档，依次点击：【文件】>【选项】>【自定义功能区】，勾选【工具】。

![1](https://github.com/user-attachments/assets/58e4d8fb-9575-4377-8d02-0a4dfd9a67e1)

#### 第二步：启用宏命令

按如下操作启用宏命令：【文件】>【选项】>【信任中心】>【宏安全性】，点选【低】。

![2](https://github.com/user-attachments/assets/b1d7eb16-084d-41ed-be24-65e620e6dbec)

#### 第三步：创建VB宏命令

1. 点击菜单【工具】＞【开发工具】下的【VB编辑器】。

   ![3 1 1](https://github.com/user-attachments/assets/52b4d6ac-7b70-4809-8d40-d2858abbfbf1)

   ![3 1 2](https://github.com/user-attachments/assets/62c47c08-1cf6-4318-a489-de164ffefbcb)

   ![3 1 3](https://github.com/user-attachments/assets/baa863b6-3e59-4891-ae29-6cbfb9c0f666)

2. 进入后选择菜单【插入】>【模块】，命名为“DeepSeek”。

   ![3 2](https://github.com/user-attachments/assets/f4134cd7-6224-44c4-b983-32f76cf67d7e)

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

   ![3 4](https://github.com/user-attachments/assets/203dc73a-2245-471c-a73a-ea3c0e2967ca)

5. 保存VB脚本并关闭窗口；再依次点击：【文件】>【选项】>【自定义功能区】，选择这里的“宏”，即可看到刚才创建的宏命令。

   ![3 5 1](https://github.com/user-attachments/assets/0f4a0b60-cde9-4ce8-8f37-0706421d6bfe)

   > 模版文件的"另存为"路径为：C:\Users\你的电脑用户名\AppData\Roaming\kingsoft\wps\startup

   ![3 5 2](https://github.com/user-attachments/assets/623b92ed-0135-482a-9878-735f54f19e2c)

#### 第四步：新建宏命令组

1. 再依次点击：【工具】>【新建组】，添加新组，并可重命名这个组。

   ![4 1 1](https://github.com/user-attachments/assets/fe5283b9-d119-4345-830c-1266f859bb95)

   ![4 1 2](https://github.com/user-attachments/assets/e6dd31a1-55f2-4c62-9a05-d769b8559473)

   添加的新组如下：

   ![4 1 3](https://github.com/user-attachments/assets/99fc8357-8dce-4699-bd96-0dabafb92814)

2. 将第三步创建的宏命令添加到这个组下面。

   ![4 2 1](https://github.com/user-attachments/assets/9c9ca1cf-0ce2-4498-87de-b4f5b8adab6f)

   在菜单【工具】中就可以看到这个新增的功能按钮：

   ![4 2 2](https://github.com/user-attachments/assets/11ad0b75-36a6-4b1a-ac39-8723bb6783d7)



#### 第五步：开始创作吧

在WPS文档中输入一段文字，选定文字，再点击新建的功能按钮。

```
# 输入文字
  帮我写一篇200字的科幻小说，有关外星人入侵地球。
```

![5 1](https://github.com/user-attachments/assets/60bae644-f620-438c-885a-8042f5a2f40b)

![5 2](https://github.com/user-attachments/assets/84257014-45ad-4207-a7b5-0d1193aa8e14)

```
# 输出说明
  <think>...</think>之间为 deepseek-r1 模型输出的思维链内容。也可以通过改写VB宏命令脚本控制不输出，有兴趣的同学可以尝试优化。
```



