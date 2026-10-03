# 01 架构拆解：3 个 Agent 如何串联

## 节点清单（13 功能节点）
| # | 节点 | 类型 | 职责 |
|---|---|---|---|
| 1 | Form Submission Trigger | formTrigger | 收创意+尺寸 |
| 2 | Prompt Creation Agent | langchain.agent | 创意→生图提示词 |
| 3 | Language Model Processor | lmChatGroq | Agent1 的模型（gpt-oss-120b） |
| 4 | Output Structuring Parser | outputParserStructured | schema: {"prompt"} |
| 5 | AI Image Generator | langchain.openAi (image) | gpt-image-1-mini 生图 |
| 6 | Upload to Google Drive | googleDrive | 存图产 webViewLink |
| 7 | Post Content Generator | langchain.agent | 写三平台文案 |
| 8 | Chat Model Execution | lmChatGroq | Agent2 的模型 |
| 9 | Output Parsing Agent | outputParserStructured | schema: instagram/linkedin/twitter (x) |
| 10 | HTML Generation Agent | langchain.agent | 组装最终 HTML |
| 11 | Groq Chat Model Execution | lmChatGroq | Agent3 的模型 |
| 12 | Structured Output Parser | outputParserStructured | schema: {"html file code"} |
| 13 | Display Final Result | form (completion) | 渲染 HTML |

## 关键接线逻辑
- **AI 子连接**：每个 Agent 挂两条 `ai_*` 连接——`ai_languageModel`（Groq）+ `ai_outputParser`（解析器）。模型和解析器不孤立配置，是 Agent 的"器官"。
- **主链引用**：
  - 生图节点 prompt = `$json.output.prompt`（来自 Agent1 解析后输出）
  - 生图节点 size = `$('Form Submission Trigger').item.json.Resolution`（跳级引用触发器）
  - HTML Agent 的 text 表达式引用 `$('Upload to Google Drive').item.json.webViewLink`（跳级引用 Drive）
- **单 item 链**：全链始终保持 1 个 item，无 fan-out。

## 提示词设计
- Agent1 system："You are a helpful image generation prompt generator..."
- Agent3 system 要求：先 Instagram 后 LinkedIn 再 Twitter，加一个「check image」按钮跳转图片链接。
- Agent2 没有 system message，靠 text 表达式里的指令裸跑（脆弱点之一）。