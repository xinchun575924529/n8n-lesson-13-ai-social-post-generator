# 03 验证报告：三层 51 断言全绿

## A · 单元级（24/24 ✅）
工作流 `L2Test199320001`（生成器 `build-l2-test-19932.py`）。
模板无 Code 节点，测「逻辑契约」忠实移植：
- P 组（3）：prompt schema 解析/多余键丢弃/缺键静默 undefined
- S 组（5）：三平台 schema 提取/`twitter (x)` 方括号键/键名漂移/emoji 换行转义
- H 组（3）：`html file code` 带空格键/围栏不剥/未转义引号崩
- F 组（4）：尺寸枚举透传/Seedream 映射/未选尺寸 undefined/未知尺寸兜底
- I 组（4）：HTML Agent 输入串四要素/图链引用/缺链 undefined/缺平台 undefined
- D 组（3）：responseText 回显/XSS 原样渲染/缺键 undefined
- G 组（2）：name=image 硬编码/时间戳文件名方案

## B · mock 全链（15/15 ✅）
工作流 `L2Mock199320001`（生成器 `build-l2-mock-19932.py`），8 节点保结构 mock：
外部服务（Groq×3/OpenAI/Drive）原位换 Code mock，验证跨节点引用链与全链数据守恒：
表单→提示词→生图（prompt+size 双透传）→托管链接→三文案→HTML 四要素→回显契约。

## C · 免费替身真跑（12/12 ✅，0 元）
工作流 `L2Cstm19932001`（生成器 `build-l2c-19932.py`）：
- DeepSeek deepseek-chat 真调 ×3（`response_format=json_object` 强制 schema 对齐）
- Seedream 真生图 1024x1536（产出 2.5MB 实拍级图片，视觉验收通过）
- 本地托管替代 Drive（时间戳文件名 + file:/// 链接）
- 产物：图片 + 6KB HTML 结果页（三文案+check image 按钮齐全）

## 结论
模板逻辑链完整可跑，所有坑都在「LLM 输出契约的静默失败」与「外部服务默认配置」两处，替身方案全部规避。