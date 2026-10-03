# 动手练习（含答案）

## 练习 1：找出静默失败点
模板里 LLM 若把 `twitter (x)` 输出成 `twitter`，哪个环节第一次出现数据丢失？为什么没有任何报错？

<details><summary>答案</summary>
Output Parsing Agent（解析器）按 schema example 的键提取，提取不到就产出 undefined。n8n 的结构化解析器默认不校验「缺失必填键」，所以静默通过；下游表达式拿到 undefined 也不报错（JS 宽松性），直到最终 HTML 出现 "undefined" 字样才肉眼可见。修法：schema 键改 `twitter_x` + 解析后加断言节点。
</details>

## 练习 2：修掉 Drive 403
用户点结果页的「check image」按钮报 403，说出根因和两种修法。

<details><summary>答案</summary>
根因：Google Drive 上传的文件默认只有上传者可见，webViewLink 未配共享权限。修法：① 上传后调 Drive permissions API 设 anyoneWithLink=reader；② 换成本地/国内图床托管（本课替身方案），彻底绕开 OAuth 与权限模型。
</details>

## 练习 3：设计防 XSS 过滤器
在 Display Final Result 之前加一个 Code 节点，写出剥除 `<script>`、`<iframe>` 和 `on*=` 事件属性的最小实现。

<details><summary>答案</summary>
```js
let html = $json.output['html file code'] || '';
html = html
  .replace(/<script[\s\S]*?<\/script>/gi, '')
  .replace(/<iframe[\s\S]*?<\/iframe>/gi, '')
  .replace(/\son\w+\s*=\s*(["']).*?\1/gi, '')
  .replace(/\son\w+\s*=\s*[^\s>]+/gi, '');
return [{ json: { output: { 'html file code': html } } }];
```
注意正则方案是教学级最小实现，生产建议用 DOMPurify 类白名单库。
</details>