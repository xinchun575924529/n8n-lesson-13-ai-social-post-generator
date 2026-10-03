# 04 免费替代方案：中国可跑版

| 原节点 | 原服务 | 替身 | 成本 | 改动点 |
|---|---|---|---|---|
| Groq ×3 | groq gpt-oss-120b | **DeepSeek deepseek-chat** | ~免费 | Agent 换 HTTP 直连/Code httpRequest；`response_format={type:'json_object'}` 替代 outputParserStructured，system prompt 里写明 schema |
| AI Image Generator | OpenAI gpt-image-1-mini | **Seedream**（豆包） | 免费额度 | 尺寸枚举同形同值 1:1 映射（1024x1024/1024x1536/1536x1024）；脚本化调用 generate_image.py |
| Upload to Google Drive | Google Drive OAuth | **本地/国内图床托管** | 0 | 时间戳文件名 `post-<ts>.png` 修掉撞名坑；本地链接天然无 403 权限坑 |

## 为什么这样换
1. **DeepSeek 的 json_object 模式**是结构化解析器的完美替身：比「LLM 自觉输出 JSON 再 parse」稳一个数量级。
2. **Seedream 尺寸恰好与 OpenAI 枚举同名**，表单字段零改动（巧合但好用）；换其他后端需重做映射表。
3. **Drive 在中国不可直连**且共享权限默认关闭，本地托管（或七牛/ OSS）一次解决两个问题。

## 替身版额外的坑（本课实测新增）
- Code 节点调 child_process 需 `NODE_FUNCTION_ALLOW_BUILTIN` 显式加 `child_process`
- DeepSeek 的 system prompt 必须明示 JSON schema，否则键名漂移（如把 `twitter (x)` 写成 `twitter`）
- CLI 执行与生图脚本超时要留足（Seedream 单张 30-90s）