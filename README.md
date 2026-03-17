# -HGNMOBOT
<img width="978" height="1018" alt="{E87B6D51-F089-4B01-9C0F-F9B0AF2BD918}" src="https://github.com/user-attachments/assets/d714b699-0ec9-4c36-b734-702fdcc8372e" />
成品展示：@HGNMOBOT

电报机器人代码，带主菜单和键盘按钮
这个脚本是一个Telegram 群管 + 会员 + 充值 + 返佣 + 积分商城 + 验证视频 等功能非常完整的机器人，底层使用 Google Apps Script（简称 GAS）实现，部署方式就是典型的 GAS → Web App → Telegram setWebhook 模式。
准备工作（必须提前完成）

拥有一个 Google 账号（Gmail 即可）
已经创建好 Telegram Bot 并拿到 Token
在 Telegram 搜索 @BotFather
输入 /newbot → 按提示设置 bot 名字和用户名（用户名必须以 bot 结尾）
拿到一串很长的 Token，例如：7123456789:AAHxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx

准备好 TRC20 USDT 收款地址（脚本里要填）
准备一个 Telegram 群/频道作为“客服转接群”（把 bot 加进去并设为管理员）

步骤一：创建并粘贴代码到 Google Apps Script

打开浏览器，访问：
https://script.google.com/home
（或搜索“Google Apps Script”进入）
点击左上角 + 新建项目
把你提供的整个代码（从 const TOKEN = 'YOUR_BOT_TOKEN'; 开始到最后 } 结束）全部复制粘贴替换掉编辑器里的默认内容。
必须修改的几个关键配置（在代码最上方）：JavaScriptconst TOKEN = '7123456789:AAHxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx';  // ← 改成你从 BotFather 拿到的真实 Token
const BOT_ID = '7123456789';                                     // ← 改成 Token 前半段的数字部分（冒号前面的数字）

const USDT_ADDRESS = 'TKxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx';     // ← 改成你真实的 TRC20 USDT 收款地址

const CUSTOMER_SERVICE_GROUP_ID = '-1002249454747';              // ← 改成你的客服转接群/频道的 chat id（负数）
// 如何拿到群 id？把 bot 加到群里 → 让某人发一条消息 → 用 /id 命令查询（或让 bot 自己记录）其他可选项（建议也看一眼）：
REFERRAL_REBATE_PERCENT = 10;  ← 返佣比例，已默认 10%
BOT_USERNAME = 'HGNMOBOT';  ← 改成你 bot 的真实用户名（不带 @）

点击左上角磁盘图标（或 Ctrl+S）保存项目，建议改个名字，例如 “TG-红粉机器人-2026”

步骤二：部署为 Web 应用（最关键一步）

点击右上角蓝色按钮 部署 → 管理部署
如果是第一次，会看到“没有部署”，点击 新建部署
部署类型选择：网页应用（Web app）
配置如下（非常重要）：项目选择 / 填写内容说明描述可写 “TG Bot Webhook”随便写执行身份我（你的 Google 邮箱）必须选“我”谁有访问权限任何人（Anyone）Telegram 必须能公开访问！版本默认（或新建版本）—
点击 部署
