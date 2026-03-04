# 🛍️ 承品MEMBER'S DAY 淘货网 (全栈购物平台)

欢迎来到 **承品MEMBER'S DAY 淘货网**！
这是一个极具现代感、基于**前后端分离架构**的全栈电商系统。

我们彻底抛弃了繁重的传统关系型数据库配置，利用前沿的 **Turso (Edge SQL)** 数据库打造了极速的数据交互体验；同时完美对接了 **Google Sheets API** 实现了现代化的、轻量级的后台订单自动化管理系统。

---

## 🌟 核心技术栈与特性

- **前端 (Frontend)**
  - **React 18 + Vite**: 闪电般的构建速度与极速页面渲染。
  - **原生响应式 CSS**: 不依赖笨重的 UI 库，实现定制化的毛玻璃与平滑过渡动画体验。
  - **React Markdown + rehypeSanitize**: 支持商品详情与全站公告的富文本 Markdown 编写，且自带军工级 XSS 防护。
  
- **后端 (Backend)**
  - **Node.js + Express**: 轻量级 RESTful API 服务，自带进程崩溃守护 (Crash Protection)。
  - **Turso Database (@libsql/client)**: 彻底告别繁琐的本地数据库环境。云端边缘 SQLite，极致响应，免部署维护，并且天然免疫 SQL 注入攻击。

- **核心业务工作流**
  - **纯云端图床管理**: 彻底拥抱 Serverless，商品图片全部采用 URL 挂载模式，极大地节省了服务器贷款和存储成本。
  - **自动化订单履约**: 顾客在前台下单后，后端会自动将包含购买明细、联络信息的订单信息实时写入您的专属 **Google Sheet** 表格。您的客服团队可以直接在 Excel 中处理所有订单！

---

## 🚀 本地开发指南 (Getting Started)

系统分为 `backend` (后端 Node 服务) 和 `frontend` (前端 React 页面) 两部分。

### 1️⃣ 环境准备与云端密钥配置

1. 确保电脑已安装 [Node.js](https://nodejs.org/) (建议 v18+)。
2. 获取你的 **Turso 数据库链接与 Token**，并记录下来。
3. 获取您的 **Google Sheets 服务账号密钥 (`credentials.json`)** 并存入 `backend/` 目录下。
4. 将该 Google 机器人的 `client_email` 添加进您的目标 Spreadsheet(表格)并赋予“编辑者”权限。

### 2️⃣ 启动后端 (Backend)

进入后端目录并配置环境变量：
```bash
cd backend
npm install
```

在 `backend` 下创建 `.env`，填入您的密钥：
```env
PORT=5000
JWT_SECRET=your_super_secret_jwt_key_here
TURSO_DATABASE_URL=libsql://your-db-url.turso.io
TURSO_AUTH_TOKEN=your_turso_auth_token
SPREADSHEET_ID=您的_GOOGLE_SHEET_ID_填写在这里
```

初始化数据表与管理员账号，并启动服务器：
```bash
npm run dev
```

### 3️⃣ 启动前端 (Frontend)

打开一个新终端，进入前端目录：
```bash
cd frontend
npm install
npm run dev
```

终端会输出 `http://localhost:5173`。在浏览器打开，尽情体验流畅的购物流程！

---

## 🏢 后台管理操作指南 (`/admin`)

默认的管理员账号（由系统首次启动时自动初始化注入 Turso）：
- **账户**: `admin@qbit.com`
- **密码**: `admin123`

登录 `/admin` 后台面板后：
1. **全局设定**: 支持修改首页顶部的【跑马灯公告】以及前台【网页主体背景图/品牌色】。
2. **商品管理**:
   - 上架商品支持极速【添加图片 URL】（建议配合 [ImgBB](https://imgbb.com/) 等免费图床获取商品照片链接粘贴至此）。
   - 商品详情介绍框完美支持 Markdown 排版语法，实时生效。

---

## ☁️ 生产环境部署建议 (Vercel 完整部署流程)

本项目的前后端完全能够零成本托管至 **Vercel** 平台。以下是完整的 Vercel 双端部署攻略。

### 第一步：准备代码并推送到 GitHub
由于您已经添加了本项目专属的 `.gitignore`，请在您的本地代码仓库执行以下命令，将代码推送到您个人的私有或公开 GitHub 仓库。
```bash
git add .
git commit -m "feat: complete project for vercel deployment"
git branch -M main
git push -u origin main
```

### 第二步：配置 Vite 前端项目部署
在 Vercel 中，我们建议把 `frontend` 和 `backend` 视为**两个互相独立的项目**分别部署。

1. 登录 Vercel 控制台，点击 **"Add New" > "Project"**。
2. 导入刚才推送到 GitHub 的代码仓库。
3. 在部署配置面板 (Configure Project)：
   - **Framework Preset**: 选择 `Vite`。
   - **Root Directory**: 点击 `Edit` 并选择 `frontend` 文件夹。
4. **Environment Variables (环境变量)**:
   - 由于前端通常需要知道后端的 API 地址。一旦您接下来把后端部署完毕，请回到前端，在 Vercel 环境变量中添加您的后端线上地址（例如 `VITE_API_URL=https://your-backend-api.vercel.app/api`）。
   - *(提示：如果需要，需要在 `frontend/src/` 中的 axios 统一加个 baseURL 引用此变量。)*
5. 点击 **Deploy**，静候几十秒编译完成。您的前端全球 CDN 就上线了！

### 第三步：配置 Express 后端服务部署 (Serverless API)
您的 Node.js 代码可以通过 Vercel 自动无缝转化为 Serverless API。

1. **核心配置准备**：
   在开始之前，确保您的 `backend` 根目录下有一个名为 `vercel.json` 的配置文件，用以指挥 Vercel 把请求正确交给 Express 入口，内容应当类似：
   ```json
   {
      "version": 2,
      "builds": [
          { "src": "src/server.ts", "use": "@vercel/node" }
      ],
      "routes": [
          { "src": "/(.*)", "dest": "/src/server.ts" }
      ]
   }
   ```
2. 在 Vercel 控制台重新点击 **"Add New" > "Project"**。
3. 导入**同一个 Github 仓库**。
4. 在部署配置面板 (Configure Project)：
   - **Framework Preset**: 选择 `Other`。
   - **Root Directory**: 点击 `Edit` 并选择 `backend` 文件夹。
   - **Build Command**: 覆盖为 `npm run build` 或者留空（只要保证 `vercel.json` 能够工作）。
5. **Environment Variables (极为重要)**:
   必须在 Vercel 的环境变量框中输入以下全部内容，否则应用会由于无法连接云数据库而崩溃：
   - `JWT_SECRET` (您自定义的安全字符串)
   - `TURSO_DATABASE_URL` (例如：`libsql://...`)
   - `TURSO_AUTH_TOKEN`
   - `SPREADSHEET_ID`
   - 以及在 Google Cloud 下载的凭据 json 内容需做一些环境变量转换，或者直接在后端读取 base64 转化为对象格式注入 Google 认证，以避开无服务器环境下的物理 `.json` 读取。
6. 点击 **Deploy**。此时您的后端 Express 就变成了按请求按次数计费（免费额度极大）的 API 处理器！

**至此，您的前后端项目不仅永不丢失数据（因为拥抱了云组件 Turso 和图床），还完美结合了 Vercel 的高速加载与零维护成本体验！**
