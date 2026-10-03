# 02 坑集：9 大实锤（全部经断言复现）

## 🔴 高危
### 坑1 · `twitter (x)` 键名（S2/S3/S4）
解析器 schema 里的键是 `twitter (x)`——**带空格带括号**。JS 里点号访问 `output.twitter (x)` 是语法错误，必须 `output['twitter (x)']`。更毒的是：LLM 只要输出 `twitter` 或 `twitter_x`，解析器**静默产出 undefined**，无报错，一路传到最终 HTML。

### 坑2 · Google Drive 共享权限 403（I3）
`webViewLink` 能拿到 ≠ 能打开。模板上传后**没有设置 anyone-with-link 权限**，收件人点「check image」按钮大概率 403。链接拼进 HTML 时一切看起来都正常。

### 坑3 · XSS 无过滤（D2）
Display Final Result 直接 `responseText = $json.output['html file code']` 渲染 LLM 生成的 HTML。LLM 若输出 `<script>alert(1)</script>`（恶意输入诱导/提示词注入），**原样在浏览器执行**。生产必须加净化（DOMPurify 或白名单标签）。

## 🟡 中危
### 坑4 · name="image" 硬编码撞名（G1）
Drive 上传文件名固定 `image`，每次运行同名。同文件夹下要么撞名要么堆积 image/image (1)/image (2)…

### 坑5 · LLM 输出未转义双引号 → JSON 崩（H3）
HTML 里天然全是双引号（`<a href="...">`），LLM 若放进 JSON 字符串没转义，`JSON.parse` 直接崩，模板**没有 try 兜底**。

### 坑6 · markdown 围栏不剥（H2）
LLM 习惯性给代码加 ```` ```html ```` 围栏，模板没有剥围栏逻辑，围栏会原样进入最终页面。

### 坑7 · Resolution 非必填（F3）
表单里尺寸字段没标 required，用户不选时生图节点收到空 size（不同生图后端行为不一，OpenAI 会回落默认，其他后端可能报错）。

## 🟢 低危（设计缺陷）
### 坑8 · 无错误网
三个 Agent + 生图 + 上传全部裸奔，任何一环失败用户只看到 n8n 错误页，没有友好的失败表单响应。

### 坑9 · Agent2 无 system message
三平台文案的风格约束全靠 text 表达式里一句话，LLM 自由发挥空间大，输出格式漂移概率高（直接放大坑1）。