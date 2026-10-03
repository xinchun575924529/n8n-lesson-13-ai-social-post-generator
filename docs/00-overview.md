# 00 总览：这个工作流解决什么问题

## 业务场景
自媒体/市场运营每天要为一个主题产出多平台内容：文案风格要随平台变化（Instagram 重标签、LinkedIn 重专业、X 重简短），还要配图。这个工作流把「一句话创意 → 三平台文案 + 配图 + 可分享的结果页」压缩成一次表单提交。

## 输入 / 输出
- **输入**：n8n 托管表单，两个字段：`input`（创意描述，必填）+ `Resolution`（图片尺寸，枚举 1024x1024 / 1024x1536 / 1536x1024）
- **输出**：表单 completion 页直接展示 AI 生成的 HTML 页面（三段文案 + 「check image」按钮跳图）

## 数据流（6 步主链）
```text
Form Submission Trigger
  → Prompt Creation Agent (Groq, {"prompt"})      # 创意→生图提示词
  → AI Image Generator (OpenAI gpt-image-1-mini)  # 提示词→图
  → Upload to Google Drive                         # 图→可访问链接
  → Post Content Generator (Groq, 三平台 schema)   # 提示词→三平台文案
  → HTML Generation Agent (Groq, {"html file code"}) # 文案+图链→HTML
  → Display Final Result (Form completion)         # 渲染 HTML
```

## 为什么值得学
- 13 个节点覆盖 n8n AI 编排的核心四件套：**Form / Agent / Structured Parser / 跨节点引用**
- 是一个「完整产品闭环」的最小样本：输入表单 → AI 加工 → 存储 → 结果页
- 脆弱点密集且典型，是讲「LLM 工作流如何加固」的绝佳反面教材