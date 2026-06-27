# 网盘资源分享站

一个可直接部署到 Cloudflare Pages 的纯静态网站，用于分享网盘链接。数据全部来自 `data/links.json` 和 `data/config.json`，通过 GitHub 修改后 Cloudflare 会自动重新部署。

## 文件结构

```
netdisk-share/
├── index.html        # 主页面（纯展示，无管理后台）
├── data/
│   ├── links.json    # 所有分享链接
│   └── config.json   # 站点名称、标题、描述
├── _headers          # Cloudflare Pages 安全响应头
├── _redirects        # Cloudflare Pages 重定向规则
└── README.md         # 说明文档
```

## 数据格式

### `data/links.json`

```json
[
  {
    "id": "1",
    "title": "Python 教程合集",
    "category": "编程",
    "url": "https://pan.baidu.com/s/xxxxx",
    "code": "abcd",
    "size": "2.3 GB",
    "desc": "从入门到进阶的 Python 视频教程"
  }
]
```

字段说明：
- `id`：唯一标识（任意字符串）
- `title`：资源名称
- `category`：分类名称，相同名称会自动归类
- `url`：网盘分享链接
- `code`：提取码（没有可留空）
- `size`：文件大小（可选，可留空）
- `desc`：资源说明（可选，可留空）

### `data/config.json`

```json
{
  "siteName": "资源分享站",
  "heroTitle": "我的网盘资源分享",
  "heroDesc": "精选资源一键直达，点击卡片即可跳转到网盘下载",
  "contact": {
    "wechat": "",
    "qq": "",
    "email": "",
    "telegram": "",
    "twitter": "",
    "github": "",
    "note": ""
  }
}
```

字段说明：
- `siteName`：网站标题（浏览器标签）
- `heroTitle`：首页大标题
- `heroDesc`：首页副标题
- `contact`：联系方式，有内容的字段才会显示；留空或删除则不显示
  - `wechat`：微信号，点击后自动复制到剪贴板
  - `qq`：QQ 号，点击打开临时会话窗口，同时复制 QQ 号
  - `email`：邮箱地址，点击打开邮件客户端
  - `telegram`：Telegram 用户名或完整链接，点击跳转
  - `twitter`：Twitter 用户名或完整链接，点击跳转
  - `github`：GitHub 用户名或完整链接，点击跳转
  - `note`：补充说明文字，会显示在联系方式列表下方

## 网站功能

- **链接卡片展示**：自动识别网盘类型，显示 SVG 图标、提取码、大小、说明
- **分类筛选**：自动从 `links.json` 的分类字段生成筛选标签
- **实时搜索**：搜索资源名称、描述和分类
- **一键跳转**：点击「前往网盘」直接打开分享链接，并自动追加提取码参数
- **自动复制提取码**：点击按钮时自动把提取码复制到剪贴板，方便粘贴
- **联系方式板块**：在页面底部展示微信、QQ、邮箱、Telegram、Twitter、GitHub 等联系方式
- **分页浏览**：资源过多时自动分页，默认每页 12 条，支持上一页/下一页和页码跳转

## 支持的网盘与提取码自动填充

自动识别并显示对应图标；链接会自动拼接提取码参数：

- 百度网盘：`?pwd=xxxx`（可自动填充）
- 夸克网盘：`?pwd=xxxx`（可自动填充）
- 阿里云盘 / 阿里盘：`?pwd=xxxx`（尝试自动填充）
- 115网盘：`?password=xxxx`（尝试自动填充）
- 天翼云盘：`?pwd=xxxx`（尝试自动填充）
- 蓝奏云：`?pwd=xxxx`（尝试自动填充）
- 迅雷网盘：`?pwd=xxxx`（尝试自动填充）
- OneDrive、Google Drive、微云、奶牛快传：无需提取码

> 注意：部分网盘官方不支持 URL 参数自动填充，此时提取码已自动复制到剪贴板，可在网盘页面粘贴。

## 部署到 Cloudflare Pages

1. 在 GitHub 新建一个仓库，上传整个 `netdisk-share` 目录
2. 打开 [Cloudflare Pages](https://pages.cloudflare.com/)，点击「Create a project」
3. 连接你的 GitHub 仓库，选择 `main` 分支
4. 构建配置：
   - **Framework preset**: None
   - **Build command**: （留空）
   - **Build output directory**: `/`（根目录）
5. 点击「Save and Deploy」

## 更新资源

每次修改资源只需编辑 `data/links.json` 或 `data/config.json`，然后提交到 GitHub：

```bash
git add .
git commit -m "更新资源"
git push
```

Cloudflare Pages 检测到提交后会自动重新部署，通常 10-30 秒内即可生效。

> **注意**：如果部署后浏览器仍显示旧内容，请按 `Ctrl + F5`（Windows）或 `Cmd + Shift + R`（Mac）强制刷新。站点已为 `data/*` 文件设置了不长期缓存，避免更新后显示旧数据。

## 本地预览

不要直接双击 `index.html` 打开，因为浏览器 `file://` 协议不允许通过 `fetch` 加载 JSON 文件。请使用本地 HTTP 服务器：

### 方式一：Python 临时服务器

```bash
cd netdisk-share
python -m http.server 8000
```

然后浏览器访问 http://localhost:8000

### 方式二：Node.js 临时服务器

```bash
cd netdisk-share
npx serve .
```

### 方式三：VS Code

安装 **Live Server** 插件，右键 `index.html` →「Open with Live Server」。

## 批量添加小技巧

如果你有很多链接，可以：
1. 用 Excel 或 Notion 整理资源
2. 使用在线工具 / AI 生成标准 JSON 格式
3. 直接粘贴到 `data/links.json`

JSON 格式校验：提交前可以用 [jsonlint.com](https://jsonlint.com/) 检查语法。

## 安全说明

- 没有客户端管理后台，不存在密码泄露或被绕过的风险
- 所有数据都是公开可见的，不适合存放敏感或隐私内容
- 建议在 GitHub 仓库中只保存公开资源链接

## 修改站点名称/标题

编辑 `data/config.json` 里的 `siteName`、`heroTitle`、`heroDesc`，提交后自动生效。
