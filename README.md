# Seeds of Story — 网站说明

## 这个网站是怎么"用工具"做出来的——给新手看

整个过程没用任何前端框架。从头到尾就一句话：**让 AI 帮你写代码、让浏览器帮你渲染、让 git 帮你存历史。** 下面按时间顺序讲清楚每一步用了什么工具、干了什么。

---

### 第 1 步:抓原网站

老站在 Google Sites(`sites.google.com/view/seedsofstory`),先要把内容搬下来。

- **用什么:** Claude Code(命令行 AI 助手) + macOS 自带的 Chrome 浏览器
- **怎么干:** 让 Claude 启动一个"无头模式"的 Chrome(看不见窗口的浏览器),把 5 个页面一个个打开,把页面上的文字、图片、链接都抓下来,整理成本地的 5 个 `.html` 文件,图片下载到 `assets/images/` 目录
- **为什么不用 curl 直接下载:** Google 的图片有防盗链,浏览器能看到,curl 拿不到——只能借浏览器的"身份"去拿

---

### 第 2 步:把脏代码洗干净

抓下来的 HTML 全是 Google Sites 自动生成的乱码(一堆 `jsaction="..."`、`class="aJHbb"` 之类没意义的属性),还混着 JavaScript 会把页面跳转回 Google。

- **让 Claude:**
  1. 删掉所有 `<script>` 脚本(避免跳转)
  2. 删掉所有 Google 内部属性(jsaction、jscontroller 等)
  3. 把样式从内嵌移到独立 `.css` 文件
  4. 用 `js-beautify` 这个小工具把压缩成一行的 HTML 重新缩进

效果:每个页面从 60KB+ 缩到 30KB,能看懂了。

---

### 第 3 步:重写成"语义化"的干净页面

光洗还不够——Google Sites 嵌了几百层 `<div>` 套娃。

- **让 Claude:** 完全推倒重写。每个页面只保留有意义的标签:
  - `<header>` 放导航
  - `<main>` 放主内容
  - `<section>` 分区块
  - `<article>` 包每个团队成员卡 / 合作机构卡
  - `<footer>` 放底部
- **同时新建一个共享的 `assets/css/site.css`**,所有 5 个页面用同一个样式表

结果:HTML 总体积 326 KB → 33 KB(缩了 10 倍),结构清晰。

---

### 第 4 步:给图片起人话名字

抓下来的图片名字都是 `eb46644706524ac1.jpg` 这种哈希。

- **让 Claude:** 一张张看图片是谁,重命名成 `team-xiaoxia-huang.jpg`、`collab-most.jpg` 这种能看懂的名字,并同步更新 HTML 引用
- **工具:** `git mv` 来重命名(这样 git 历史能跟踪到"这张图是从哪个旧名字改过来的")

---

### 第 5 步:换风格

参考另一个 Google Sites 站点的视觉感觉(粗砂色调、手写字体、照片拼贴 hero),让 Claude 改 `site.css`:

- 换调色板:森林绿 → 暖奶油色 + 苔绿 + 烧橙
- 加手写字体(Google Fonts 上免费的 Caveat、Caveat Brush)
- 给团队照片加圆头像 + 拍立得感的卡片
- 首页大图区改成照片拼贴 mosaic

---

### 第 6 步:用浏览器"画"了一张手写大标题

想要"儿童粉笔手写体"作为首页大标题。直接用 CSS 字体不够"粉笔"。

- **让 Claude 写了一个小脚本:**
  1. 临时生成一个 HTML:里面只有 "Seeds of Story" 几个字,用 Caveat Brush 字体,叠一层 SVG 噪点滤镜(模拟粉笔颗粒边缘)
  2. 启动无头 Chrome 打开这个 HTML
  3. **截图保存成透明背景的 PNG**
  4. 把这个 PNG 放在网页上当大标题

**这就是"用浏览器当画图工具"。** 任何浏览器能渲染的东西,都能这样截下来变成图片。

---

### 第 7 步:54 张照片拼成 1 张大图

首页那张"很多照片拼起来"的 hero 图——不是手动用 Photoshop 拼的。

- **让 Claude 写了另一个脚本:**
  1. 读取 `assets/images/hero/` 下 54 张照片
  2. 用算法(Flickr 的 justified gallery 思路)把它们排成错落有致的 5 行,每张图大小不一,紧贴无缝
  3. 同样用无头 Chrome 渲染这个排版
  4. **截图保存成一张 JPG**(约 750 KB)
- 网页里只放 `<img src="hero-mosaic.jpg">` 一张图,浏览器秒加载

---

### 第 8 步:本地预览 + git 存档

- **预览:** `python3 -m http.server` 一句话起本地服务器,浏览器开 `http://localhost:8000` 就能看
- **存档:** 每改完一件事就 `git commit` 一次,加 tag。出错能随时回滚

---

## 一句话总结

> **告诉 Claude 你想要什么 → Claude 写代码或者让浏览器渲染 → 截图或保存成文件 → git 记录每一步。**

整个网站做下来用到的"工具"其实就 4 个:

1. **Claude Code**(AI 助手,写代码 / 抓内容 / 改样式)
2. **Chrome 浏览器**(既是预览器,也是图片生成器)
3. **Python 自带的 HTTP 服务器**(本地预览)
4. **git**(存历史)

没有 Webpack、Vite、React、Tailwind、Node 框架……都是浏览器原生能跑的 HTML + CSS + 图片。**适合任何不写代码的人慢慢学着改自己的网站。**
