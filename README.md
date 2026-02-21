# 汤小米.疯狂八点 (Crazy Eights)

一个经典的疯狂八点纸牌游戏，使用 React + Vite + Tailwind CSS 构建。

## 部署到 Vercel 指南

为了将此项目部署到 Vercel 并与 GitHub 同步，请按照以下步骤操作：

### 1. 推送到 GitHub
1. 在 GitHub 上创建一个新的仓库。
2. 在本地终端运行以下命令（确保你已经安装了 git）：
   ```bash
   git init
   git add .
   git commit -m "Initial commit"
   git branch -M main
   git remote add origin <你的仓库URL>
   git push -u origin main
   ```

### 2. 连接到 Vercel
1. 登录 [Vercel](https://vercel.com)。
2. 点击 **"Add New"** -> **"Project"**。
3. 导入你刚刚创建的 GitHub 仓库。
4. 在 **"Build & Development Settings"** 中，Vercel 会自动识别这是一个 Vite 项目。
5. 如果你的项目使用了 Gemini API，请在 **"Environment Variables"** 中添加 `GEMINI_API_KEY`。
6. 点击 **"Deploy"**。

### 3. 运行环境说明
- **框架**: React 19
- **构建工具**: Vite 6
- **样式**: Tailwind CSS 4
- **动画**: Motion (Framer Motion)

## 游戏规则
- **发牌**: 玩家和 AI 各 8 张牌。
- **出牌**: 必须匹配弃牌堆顶牌的花色或点数。
- **数字 8**: 万能牌，可以随时打出并指定新花色。
- **摸牌**: 无牌可出时需从牌堆摸一张。
- **胜利**: 最先清空手牌的一方获胜。
