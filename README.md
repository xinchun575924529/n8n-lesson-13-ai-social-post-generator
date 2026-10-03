# n8n 实战课 13：一句话生成社媒三件套 + AI 配图（AI Post Generation）

> 官方模板：[Generate social media posts and images with Groq, OpenAI and Google Drive](https://n8n.io/workflows/19932/)（模板 ID 19932，13 功能节点 + 5 张教学便签）

填一个表单（一句话创意 + 图片尺寸），自动完成「AI 写生图提示词 → AI 生图 → 图床托管 → AI 写 Instagram/LinkedIn/X 三平台文案 → AI 生成精美 HTML 结果页」全流程。

## 你会学到

1. **n8n Form 触发器/回显表单**：表单触发 + completion 页直接渲染 LLM 生成的 HTML
2. **AI Agent 链式编排**：3 个 LangChain Agent 串联（提示词工程 → 文案 → HTML），每段配 Groq 模型 + 结构化输出解析器
3. **Structured Output Parser 实战**：用 JSON schema example 约束 LLM 输出（含 `twitter (x)` 这种带空格括号的键名处理）
4. **跨节点引用**：`$('Form Submission Trigger').item.json.Resolution`、`$('Upload to Google Drive').item.json.webViewLink` 的正确姿势
5. **九大实锤坑**：键名漂移静默 undefined / Drive 撞名+403 / LLM HTML 的 XSS（见 docs/02-pitfalls.md）

## 课程文件

| 文件 | 内容 |
|---|---|
| `workflow.json` | 官方模板原样（13 功能节点） |
| `workflow-custom.json` | 中国可跑免费替代版（DeepSeek×3 + Seedream 生图 + 本地托管，已脱敏） |
| `docs/00~05` | 总览 / 架构 / 坑集 / 验证报告 / 免费替代 / 生产加固 |
| `exercises/exercise.md` | 3 道动手练习（含答案） |

## 三层验证结论（本课全部实测通过）
- **A 单元级**：4 组逻辑契约忠实移植，**24/24 断言全过**
- **B mock 全链**：外部服务全 mock 保结构，**15/15 断言全过**
- **C 真跑**：DeepSeek×3 真调 + Seedream 真生图 + 本地托管，**12/12 断言全过，0 元成本**，产出 2.5MB 实拍级图片 + 完整 HTML 结果页

## 一分钟看懂它干嘛
```text
表单提交（创意+尺寸）
  → AI Agent1 把一句话扩写成专业生图提示词
  → AI 生图 → 上传图床拿链接
  → AI Agent2 写 Instagram / LinkedIn / X 三条文案
  → AI Agent3 把文案+图链接渲染成精美 HTML 结果页
```