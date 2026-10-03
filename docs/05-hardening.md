# 05 生产加固清单

直接照模板上线会踩坑，按此清单加固：

## 必做（堵高危）
1. **键名规范化**：把 schema 里的 `twitter (x)` 改成 `twitter_x`，HTML Agent 表达式同步改——一劳永逸消除键名漂移坑。
2. **XSS 净化**：Display 节点前加一道 HTML 白名单过滤（Code 节点剥 `<script>`/事件属性/iframe），或改用表单字段逐项回显而不是整段 HTML。
3. **Drive 权限**：上传后补一个权限调用（permissions: anyone+reader），或在 HTML 里放代理链接。

## 应做（堵中危）
4. **文件名唯一化**：`post-{{$execution.id}}.png` 或时间戳。
5. **解析兜底**：JSON.parse 包 try/catch，失败时走「原文回退 + 错误标记」分支。
6. **剥围栏**：对 LLM 输出统一 `.replace(/^```\w*\n?/, '').replace(/```$/, '')`。
7. **表单必填**：Resolution 勾上 required，或表达式给默认值 `{{ ... || "1024x1024" }}`。

## 选做（体验）
8. **错误网**：Error Trigger 工作流捕获失败 → 表单/通知渠道回执「生成失败原因」。
9. **Agent2 补 system message**：明确三平台各自的语气/长度/标签规范。
10. **审计字段**：记录 executionId、prompt、图片链接，便于回溯每次生成。