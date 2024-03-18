# HTML(HyperText Markup Language--超文本标记语言)

## `<meta>`The metadata element

Attribute(metadata type):

- charset `declares the document's character encoding.`*charset="utf-8"*
- [name & content](#name--content)
- [http-equiv](#http-equiv)
- itemprop

### [name & content](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/meta/name#standard_metadata_names_defined_in_the_html_specification)

- `application-name` *应用运行在网页的名称*
- `author` *文档作者名称*
- `description` *网页内容的简短准确摘要*
- `generator` *生成页面的软件的标识符*
- `keywords` *页面内容相关的字词,用逗号分隔*
- `referrer` *控制从本文档处发送HTTP请求的`Referer`请求头*
- `theme-color`
- `color-scheme`
- `viewport`

>example: `<meta name="viewport" content="">`
>
>width = device-width | 正整数 *定义窗口的宽度*
>
>height = device-height | 正整数 *定义窗口的高度*
>
>initial-scale = 0.0 <= 正数 <= 10.0 *定义设备宽度destination*
>
>maximum-scale = 0.0 <= 正数 <= 10.0 *定义可缩放的最大值*
>
>minimum-scale = 0.0 <= 正数 <= 10.0 *定义可缩放的最小值*
>
>user-scalable = yes | no *如果设置为"no",则用户无法放大网页.默认值为 yes.*
>
>viewport-fit = auto | contain | cover

```html
<!-- 不发送Referer HTTP请求头 -->
<meta name="referrer" content="no-referrer">

<!-- 发送文档的origin -->
<!-- 文档的origin通过URL的scheme(protocol),hostname(domain),port来定义,只有这些都相同时意味着它们是同源的 -->
<meta name="referrer" content="origin">

<!-- 当目标网页与当前网页的安全性至少一致时发送完整的URL HTTP(S)->HTTPS -->
<!-- 当安全性降级时禁止发送referrer请求头 HTTPS->HTTP -->
<!-- 这是默认行为 -->
<meta name="referrer" content="no-referrer-when-downgrade">

<!-- 同源时发送去掉参数的完整URL -->
<!-- 否则仅发送origin -->
<meta name="referrer" content="origin-when-cross-origin">

<!-- 同源时发送去掉参数的完整URL,跨源(Cross-origin)请求会禁止发送referrer请求头 -->
<meta name="referrer" content="same-origin">

<!-- 当目标网页与当前网页的安全性至少一致时发送origin HTTP(S)->HTTPS -->
<!-- 当安全性降级时禁止发送referrer请求头 HTTPS->HTTP -->
<meta name="referrer" content="strict-origin">

<!-- 同源时发送去掉参数的完整URL -->
<!-- 当目标网页与当前网页的安全性至少一致时发送origin HTTP(S)->HTTPS -->
<!-- 否则禁止发送referrer请求头 -->
<meta name="referrer" content="strict-origin-when-cross-origin">

<!-- 同源或者跨源时 发送去掉参数的完整URL -->
<meta name="referrer" content="unsafe-URL">
```

### http-equiv

>所有允许的值都是特定 HTTP 标头的名称

- `content-security-policy` *定义内容策略.内容策略主要指定允许的服务器源和脚本终结点,这有助于防范跨站点脚本攻击.*
- `content-type` *声明 MIME 类型和文档的字符编码.(如果指定)content属性的值必须为"text/html;charset=utf-8".这等效于`<meta>`指定了 charset 属性的元素,并且对文档中的位置具有相同的限制.注意:只能在以 text/html 提供的文档中使用,而不能在以 XML MIME 类型提供的文档中使用.*
- `default-style` *设置默认 CSS 样式表集的名称*
- `x-ua-compatible` *如果指定,则 content 属性的值必须为"IE=edge".用户代理需要忽略此编译指示.*
- `refresh`
  - *重新加载页面之前的秒数 - 仅当 content 属性包含非负整数时.*
  - *页面重定向到另一个页面之前的秒数 - 仅当 content 属性包含非负整数后跟字符串 ';url=',以及有效的 URL. example:`<meta http-equiv="refresh" content="3;url=https://www.mozilla.org" />`*

## `<script>` The Script element

>用于嵌入可执行代码或数据;这通常用于嵌入或引用 JavaScript 代码.该`<script>`元素还可以与其他语言一起使用,例如 WebGL 的 GLSL 着色器编程语言和 JSON.

### Attributes 属性

![The Difference Between Async & Defer](https://pagespeedchecklist.com/img/articles/async-vs-defer.svg)

#### `async`

>对于经典脚本,如果存在 async 属性,则经典脚本将在解析的同时获取,并在可用时立即对其进行评估.
>
>对于模块脚本(module scripts),如果存在 async 属性,则脚本及其所有依赖项将在解析时并行获取,并在可用时立即进行评估.
>
>此属性允许消除解析器阻塞(parser-blocking) JavaScript,浏览器在继续解析之前必须加载和评估脚本.在这种情况下,`defer` 具有类似的效果.
>
>这是一个布尔属性:元素上存在布尔属性表示真值,而缺少该属性表示假值.
>
>*设置此布尔属性，以指示浏览器如果可能的话，应异步执行脚本*

#### `defer`

>此 Boolean 属性设置为向浏览器指示脚本将在解析文档完成之后但是在触发 DOMContentLoaded 之前执行。
>
>具有 defer 属性的脚本将阻止触发 DOMContentLoaded 事件，直到脚本加载并完成计算。
>
>具有 defer 属性的脚本将按照它们在文档中的显示顺序执行。
>
>此属性允许消除解析器阻塞(parser-blocking) JavaScript，浏览器在继续解析之前必须加载和评估脚本。在这种情况下，`async`具有类似的效果。
>
>*此布尔属性被设置为向浏览器指示脚本在文档被解析后执行。*

注意
>[!WARNING]
>警告：如果 src 属性不存在（即对于内联脚本），则不得使用此属性，在这种情况下，它不会起作用。
>defer 属性对模块脚本没有影响 — 默认情况下，它们会延迟。
