---
title: 开源神器_一个AI终端搞定SSH+SFTP+运维_太香了
tags: [github, 技术, 网络, wiki]
category: article
url: https://mp.weixin.qq.com/s?__biz=MzU2MTI4MjI0MQ==&mid=2247543795&idx=1&sn=75f23015277cae1b046d394370db6b21&chksm=fd47afa850e019fd281239825d91015751c7c3b1d70deb62193431b5fa9f5570e6323f1fffd8#rd
origin: 微信
cover: https://res.cloudinary.com/dqvoyeo3x/image/upload/v1785492652/mpclipper/2026/07/31/a18d7ce525c490a6a4e157287730a51b_640.png
author: 极客之家
published_date: 2026-06-06 11:30:00
createTime: 2026-07-31 18:10:56
---



> 字数 1902，阅读大约需 10 分钟


Termius 的 Pro 订阅到期，一直弹窗，搜替代品的时候翻到了 Netcatty，macOS、Windows、Linux 全平台支持，目前 GitHub 上有2.1k Star。 

技术栈 Electron 40 + React 19 + TypeScript + xterm.js 5 + Tailwind CSS 4。 

界面不像是「开源=UI 随便糊弄」的画风。 

![](https://res.cloudinary.com/dqvoyeo3x/image/upload/v1785492652/mpclipper/2026/07/31/a18d7ce525c490a6a4e157287730a51b_640.png)

# 主机管理 

SSH 客户端第一件事：你怎么看到你连的那堆机器。 

PuTTY 的做法是根本不帮你管，一台机器一个窗口，你自己记 IP。Termius 把这东西叫 Vault，给你一个主机库，分组、搜索、标签都有。免费版限 50 台，Pro 不限。 

Netcatty 也管这个叫 Vault，给了三种视图。 

![](https://res.cloudinary.com/dqvoyeo3x/image/upload/v1785492651/mpclipper/2026/07/31/c92eeb7d8e1290e3c384ca5476091c8d_640.png)

**网格视图：**  机器不多的时候好用，图标大，一眼认出哪台是 Ubuntu 哪台是 CentOS。它自动识别系统版本然后把对应发行版的图标挂上去，Ubuntu 就显示 Ubuntu 的 Logo，Debian 就显示 Debian 的。 

![](https://res.cloudinary.com/dqvoyeo3x/image/upload/v1785492651/mpclipper/2026/07/31/7f70c74eede7584987bbeaf866a8099e_640.png)

**树形视图：**  机器多了以后我用这个。开发环境丢一个组，生产环境丢另一个组，中间件单独一个组。脑子不用记，眼睛扫一眼就知道哪台在哪。Termius 也有分组，但它不让你在树形视图下拖拽移动。Netcatty 可以。 

列表视图也有，适合搜索。但我用得少，不截图了。 

Termius 管 50 台以上收你钱，Netcatty 不管多少台都不收。 

# 终端和分屏 

分屏这东西属于典型的「没用过觉得花哨，用过以后回不去」。 

![](https://res.cloudinary.com/dqvoyeo3x/image/upload/v1785492651/mpclipper/2026/07/31/e638641c769b30b1cb600c5e05af3045_640.png)

我现在写代码的常态：左边 `docker compose logs -f`  盯着后端日志，右边 vim 改配置，下面再开个小窗跑 `htop` 。一个窗口全搞定。不用 Alt+Tab 在三个窗口之间切到手指抽筋。 

Termius 也有分屏，但它那个分屏只能在同一个连接里拆。Netcatty 的分屏可以跨连接。左边连 A 服务器，右边连 B 服务器，同一个窗口并排看。这对经常比对着两台机器排查问题的人来说是刚需。 

**Broadcast 广播模式也算实用。**  选了五台机器，敲一次命令，五台同时执行。做批量更新或者检查集群状态的时候省大事。 

**关键词高亮。**  这功能 PuTTY 没有，Termius 免费版也没有。在 Netcatty 里设个规则， `ERROR`  自动标红加粗， `WARNING`  标黄。日志刷屏的时候你不用瞪大眼睛找，红色自己跳出来。 

五十多套内置主题，懒得搞的直接挑。我用的 Dracula，紫底灰字，看着不累。 

说个细节： 

> 会话断了它自动重连。半夜跑长任务，SSH 超时断掉很正常。Termius Pro 有这功能，免费版没有，Netcatty 直接给。


# SFTP和文件编辑 

运维最烦的操作，改个 nginx 配置排前三。 

流程是这样的：scp 把 `/etc/nginx/nginx.conf`  从服务器拽下来，本地改一行 `worker_connections 1024` ，再 scp 传回去。就一行配置，折腾三分钟。有时候网络慢，传个几 KB 的文件要十秒，你就在那干等。 

Netcatty 搞了个双面板。左边你本机文件，右边远程服务器文件，拖拽上传下载。更关键的是右键能直接编辑远程文件，改完 Ctrl+S，自动写回服务器。编辑器不是 VS Code 级别的，但改个 yaml、json、conf 绰绰有余。语法高亮有，缩进对齐有。 

有个细节我很满意：sudo 提权。普通用户登录，要改 `/etc/nginx/nginx.conf` ，它弹窗让你输 sudo 密码，提完权直接存。省掉了退出重连 root 或者 `sudo vim`  进去改的步骤。Termius 也有这功能，但藏在 Pro 里。 

说实话，这功能省的时间比 AI Agent 还多。毕竟我改配置的频率比让 AI 替我排查问题的频率高得多。 

# AI Agent 

我对「AI 终端」这三个字有心理阴影。 

Warp 的 AI 我用过。就是一个嵌在终端里的聊天框，你问它命令，它生成，你复制粘贴自己跑。跟我切到浏览器问 Claude Code 有什么区别？就是少按了一次 Alt+Tab。 

Netcatty 这个叫 Catty 的 Agent 不是聊天框。它是一个能自己 ssh 到服务器上执行命令的东西。 

我跟它说「帮我看看这台机器内存占用前三的进程」，它自己跑 `ps aux --sort=-%mem | head -4` ，结果返回来，顺便告诉你哪个进程该杀。不是我复制命令，是它自己执行完把结果给我。 

多机操作更狠，你连着三台机器，说「把这三台组个 Docker Swarm 集群」。它自己在 manager 上 `docker swarm init` ，拿 join token，切到 worker 上执行 join。全程你看着就行。作者在 GitHub README 里放了个录屏，两分钟搭完一个 Swarm。 

安全这块也做得比较好，涉及 `rm` 、 `kill` 、改文件这类操作，会弹确认框。而且 Agent 模式默认关闭，你得自己去设置里打开。AI 帮你干活，锅还是你自己背，这个逻辑对。 

支持所有主流模型，OpenAI、Anthropic Claude、OpenRouter、Codex CLI，随便。对数据敏感的，本地跑 Ollama 也行。不绑模型，不搞自己的付费 API。这一点比 Warp 体面。Warp 的 AI 免费额度用完就催你充钱。 

说实话 AI 也有蠢的时候。我让它排查一个网络问题，它在 `ping`  和 `traceroute`  之间来回跳了三次。复杂诊断它还差点火候。但这项目才几个月大，AI 搞到这个程度我已经不好意思骂了。 

# 云同步 

Termius 收钱的最大头就是云同步。免费版你连了五十台主机，换台电脑打开，全没了。想多设备同步？先付 $15/月。 

Netcatty 给了六种同步后端：GitHub Gist、S3、WebDAV、Google Drive、OneDrive，还有本地文件。端到端加密，主机配置、SSH 密钥、主题全给你同步。 

对国内用户最友好的是 WebDAV。自己 NAS 搭一个，或者挂坚果云，数据自己手里拿着，不经过第三方服务器。不用注册 Netcatty 账号，不用绑邮箱，不用收验证码，装完就能用。 

我一开始开了实时同步，结果切个标签页就触发一次，硬盘灯闪得我难受。改成手动以后清净了。同步速度也还行，一百多台主机的配置几秒搞定。 

# 差点意思的地方 

好话说了不少，该骂的也得骂。 

**Electron 通病：**  日常 300 到 500MB 内存，开七八个会话飙到 600MB 以上。PuTTY 开十个才几十 MB。一个终端客户端比我 IDE 还吃内存。但怎么说呢，一个人三周写出来的东西，Electron 是比较务实的选型。用 Rust 重写一遍再用 GTK 画 UI，一个人干一年差不多。 

**没有移动端：**  Termius 的 iOS 和 Android 客户端是它最大的护城河。半夜服务器报警，掏出手机连上去看，这个体验 Netcatty 目前给不了。 

**sz/rz 不支持：**  习惯了在终端里敲 `sz filename`  拽文件的人，得等。走 SFTP 面板传可以，但方式不一样。有些老派运维就对 sz/rz 有执念，Netcatty 目前还不支持。 

**macOS 第一次打开会提示「无法验证开发者」：** `xattr -cr /Applications/Netcatty.app`  一行命令的事。这不是 Netcatty 的锅，Apple 对未签名应用的统一待遇。作者在 README 里说正在搞代码签名和公证，估计下几个版本能解决。 

---

最后，给个客观评价：吃内存、没有移动端，剩下的对于正常后端开发的人来说，足够用！ 

**点击下方卡片，关注极客之家** 

这个公众号曾分享过许多有趣的开源项目。如果你不想逐篇翻阅历史文章，也可以直接关注微信公众号“极客之家”，通过后台留言与我们互动交流 

![](https://res.cloudinary.com/dqvoyeo3x/image/upload/v1785492651/mpclipper/2026/07/31/a8aeb77ad7fc05415fc8ecf07cdb68c8_640.webp)
