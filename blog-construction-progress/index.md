# 博客当前施工进展




# 静态网站生成器
>  static site generator / web template system

可选包括 Jekyll、Hexo、Pelican、Gridea、Hugo ……

前几日看到新闻说 [KDE.org 迁移到 Hugo](https://www.solidot.org/story?sid=65959)，所以我现在有些倾向于它。

## 践行[《Web 内容无障碍指南》](https://www.w3.org/Translations/WCAG21-zh)

对于一个没有前端开发经验的人来说自己从头做起未免步子迈太大，找个无障碍优化做得比较好的 [Hugo 主题](https://themes.gohugo.io/) 才是个现实的解决办法。根据 accessible/accessibility 标签筛选出了以下博客主题： [Zzo](https://themes.gohugo.io/hugo-theme-zzo)、[Color Your World](https://themes.gohugo.io/hugo-theme-color-your-world)、[Terrassa](https://themes.gohugo.io/hugo-terrassa-theme)、[Zen](https://themes.gohugo.io/hugo-theme-zen)、[Cupper](https://themes.gohugo.io/cupper-hugo-theme)、[Accessible Minimalism](https://themes.gohugo.io/accessible-minimalism-hugo-theme)

从 W3C 列出的 [Web Accessibility Evaluation Tools List](https://www.w3.org/WAI/ER/tools) 中选了一个在线测试工具 [WebAccessibility](https://www.webaccessibility.com) 进行测试，测试结果如下（测试进行于2020年11月9日，测试结果仅能代表此刻的情况）。

| theme name            | test url                                                     | number of violations | compliance score | personal evaluation              |
| --------------------- | ------------------------------------------------------------ | :------------------: | :--------------: | -------------------------------- |
| Zzo                   | https://themes.gohugo.io/theme/hugo-theme-zzo/               |          27          |       85%        | garish                           |
| Color Your World      | https://themes.gohugo.io/theme/hugo-theme-color-your-world/  |          0           |       91%        |                                  |
| Terrassa              | https://themes.gohugo.io/theme/hugo-terrassa-theme/          |          6           |       87%        | comfortable                      |
| Zen                   | https://themes.gohugo.io/theme/hugo-theme-zen/               |          1           |       88%        | basic black and white blog style |
| Cupper                | https://themes.gohugo.io/theme/cupper-hugo-theme/            |          0           |       91%        | black and white but too sharp    |
| Accessible Minimalism | https://themes.gohugo.io/theme/accessible-minimalism-hugo-theme/ |          0           |       91%        | too crude                        |

可是老实说上边这些我都不 100% 合我心意，就 Demo 的展示内容来说像走入了两种极端：极简或是花里胡哨。在 blog 标签下发现了这个功能和质感都恰到好处为 [Eureka](https://themes.gohugo.io/hugo-eureka) 的主题，虽然没有 accessible 标签但 [WebAccessibility](https://www.webaccessibility.com) 合规成绩 92%，更重要的是开发者是中文母语者——这个主题的中文显示效果不会差。

## Hugo 101

- 系统环境：Windows 10
- 终端：Windows PowerShell

### 安装

> https://gohugo.io/getting-started/installing/

按照官方文档中的 Quick Install 部分，在 [releases](https://github.com/gohugoio/hugo/releases) 页下载 `hugo_0.78.1_Windows-64bit.zip` 解压后的 `hugo.exe` 放入本机的某一路径如 `D:\Hugo\hugo_0.78.1` 。此时就能执行 Hugo 的命令了，可通过如下命令测试：

```powershell
D:\Hugo\hugo_0.78.1\hugo.exe help  # 输出 Hugo 的命令手册
# 为了省略前面的路径，接下来需要添加环境变量
```

将路径 `D:\Hugo\hugo_0.78.1` 添加入用户变量 `PATH` ，而后重启使其生效。接下来使用如下命令测试：

```powershell
hugo help  # 输出 Hugo 的命令手册
```

### 使用

```powershell
hugo  # 将当前工作目录作为输入，根据该路径下的 config 文件/文件夹生成静态网站内容至 .\public

# config 文件或是 config 文件夹是并列的两种配置方式，前者可以通过用 Hugo 创建站点来生成
hugo new site <your_site_name>
tree
.
└── <your_site_name>
    ├── archetypes
    │   └── default.md
    ├── config.toml
    ├── content
    ├── data
    ├── layouts
    ├── static
    └── themes
# 接下来对于站点 <your_site_name> 的操作都要在其根目录下进行
cd <your_site_name>
hugo new content/new_post.md  # 创建新文章

hugo server  # 生成一个本地测试站点，注意：所有文件头为 draft: true 的 markdown 文件都不会被处理与显示；
```

#### 应用主题

> https://www.wangchucheng.com/zh/docs/eureka/getting-started/

进入站点根目录，初始化一个 Hugo Module，名字可以自定义，以后永远不会用到。然后从 GitHub 下载 Eureka 主题的 [config 文件夹](https://github.com/wangchucheng/hugo-eureka/tree/master/exampleSite) 到站点根目录，修改 `<your_site_name>/config/_default/config.toml` 的配置，然后删除 `<your_site_name>/config.toml` 。接下来就可以生成测试站点了。

#### 客制化



### 部署

https://github.com/marketplace/actions/hugo-to-gh-pages

https://gist.github.com/lisez/41cebe4eb9190a5c5e879fee5933beb1
#### 处理 Google Search Console 验证

暂时无法通过 `Google Analytics（分析）` 的方式验证所有权，加上没有添加独立域名，导致唯一可用的验证方式是 `HTML 标记` —— 向网站根目录上传一个 Google 提供的 HTML 文件并一直保持。

尝试单独把这个 html 文件 commit 到 `xuchunqiu/xuchunqiu.github.io`，顺利通过验证；然而上面 GitHub Action 部署过程中默认的 `--cleanDestinationDir` 运行参数会使得仓库 `xuchunqiu/xuchunqiu.github.io` 中的原始内容在更新后被完全清空（包括单独 commit 的 HTML 文件）。

解决思路有二：修改 GitHub Action、让 Hugo 生成静态网页时把这个 HTML 加进去。

后者在 Hugo 的 [文档](https://gohugo.io/getting-started/directory-structure/) 中有特别提到，文件夹 `static` 适合用来存储 Google Search Console 的验证用 HTML：

> static: Stores all the static content: images, CSS, JavaScript, etc. When Hugo builds your site, all assets inside your static directory are copied over as-is. A good example of using the static folder is for verifying site ownership on Google Search Console, where you want Hugo to copy over a complete HTML file without modifying its content.

## TODO

- [ ] 寻找国内友好的图床

- [ ] 添加 Gitalk 评论区

- [ ] 熟悉发布博文到 GitHub Pages 的工作流

  - [ ] 统一 commit 样式（约定式提交？）

- [ ] 践行 [《中文排版方案指北》](https://github.com/sparanoid/chinese-copywriting-guidelines)

- [ ] 迁移 Dropbox 内的笔记

- [ ] 迁移 Notion 内的知识库
