# -HGNMOBOT
<img width="978" height="1018" alt="{E87B6D51-F089-4B01-9C0F-F9B0AF2BD918}" src="https://github.com/user-attachments/assets/d714b699-0ec9-4c36-b734-702fdcc8372e" />
成品展示：[@HGNMOBOT](https://t.me/HGNMOBOT)

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

按钮是按照什么方式自动排列的：

机器人里按钮的自动排列主要依赖两个核心函数，根据不同场景（主菜单 vs 其他 inline 键盘 vs 底部键盘）采用不同的策略。核心思想是“视觉友好 + 尽量紧凑 + 避免过长挤压”。
下面按类型拆开解释排列规则（基于你提供的代码）：
1. 主菜单按钮（绿色 inline 键盘）
→ 使用 buildStyledInlineKeyboard(buttons, "success") 函数
排列逻辑（最智能、最常用的一种）：

先计算每个按钮的视觉长度（getButtonVisualLength 函数）：
中文汉字算 1 个长度
英文/数字/符号算 1
表情符号（emoji）通常算 2（因为占位宽）

贪心式逐行尝试塞入最多 3 个按钮：
如果当前按钮视觉长度 > 5 → 强制独占一行（不和别人挤）
否则先尝试塞 3 个（如果后两个也 ≤5）
塞不下 3 个 → 尝试塞 2 个（如果第二个 ≤5）
还不行 → 单个放一行


总结主菜单特点：

优先 3 列 → 2 列 → 1 列
很长的按钮（>5）会自动掉到单独一行
整体看起来整齐、绿色风格（success 样式）

2. 其他 inline 键盘（比如关键词回复、设置面板等）
→ 使用 buildInlineKeyboard(buttons, "primary") 函数
这个函数更固定、更主题化（很多硬编码的顺序）：

先把所有按钮放进一个“池子”（pool）
然后按预设的关键词分组顺序强制排版（从上到下）：
选妃频道（独占一行）
城市大区（北京上海天津... 尽量 5 个一行）
更多城市分组（广东广西... 5个、云南贵州... 5个 等）
全国可飞 / 东南亚 / 全球可飞（3个一行）
新人必看包养介绍（独占）
特殊标签（无套、SM、处女） → 优先挤进同一行（最多3个）
身份/类型（满月陪伴 老师 医生 → 2个一行）
更多类型（少妇 学妹、白虎 大胸 等 → 2个一行）
价格段（万内性价比、2万内精选 → 2个）
妹团交流群（独占）
剩余所有按钮 → 每 2 个一行（自动两列兜底）


总结：

前面大量是写死的优先级顺序（先大区 → 特殊标签 → 类型 → 价格 → 群组）
后面没匹配到的按钮自动 2 列 排列
目的是让重要/热门分类永远在最上面

3. 底部键盘（红色 reply keyboard）
→ 使用 buildStyledReplyKeyboard(rows)
排列逻辑（和主菜单有点像，但更宽松）：

输入的是二维数组（每行是一个数组）
对每行按钮：
1 个 → 独占一行
2 个 → 并排一行
2 个 → 尝试每行最多塞 4 个（但如果某个按钮视觉长度 >5 就强制换行）

整体支持 resize_keyboard: true（自适应大小）

示例（默认 DEFAULT_KEYBOARD_BUTTONS）：
text["频道导航", "AI智能搜索"],
["包养介绍", "推广赚钱"],
["个人中心", "人工客服"]
→ Telegram 底部会显示 2×3 的布局，红色风格。
快速对比总结

































位置函数优先级/策略最大列数特殊处理主菜单buildStyledInlineKeyboard贪心 3→2→1 列3>5 长度强制独占一行关键词/设置等buildInlineKeyboard硬编码主题顺序 + 最后2列兜底5/3/2/1城市/标签有固定分组顺序底部键盘buildStyledReplyKeyboard按输入行 + 每行最多4个4>5 长度强制换行
一句话概括：
主菜单最“聪明”（自动尽量3列），其他 inline 键盘更“主题化”（写死重要顺序），底部键盘最“听话”（基本按你给的 rows 排，但会微调避免太挤）。
如果你想修改排列，最直接的方式是：

主菜单 → 用 /setmainmenu 命令调整按钮顺序和文字长度
其他键盘 → 修改 buildInlineKeyboard 里的 addRow 调用顺序和关键词数组
底部 → 用 /setkeyboard 命令直接改 rows 布局
