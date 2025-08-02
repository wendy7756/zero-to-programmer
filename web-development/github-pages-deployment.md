# 上传本地网页到GitHub网址完整详细步骤

> 发布时间：2020.04.28  
> 阅读量：11,722  
> 点赞：7  
> 收藏：46  
> 评论：2  

## 前言

无需服务器，无需虚拟机，无需域名，只要有GitHub账号就可以上传网页，再也不用担心把本地HTML网页发给别人之后打开是乱码啦。

**血与泪的经验**：本地网页上传到网站上才能保证所见即所得，手机上也能直接看到本地网页。网上教程质量参差不齐，试了好几个教程才上传网页成功，这里分享一下我的步骤。

## 准备工作

### 需要的工具
1. GitHub账号
2. Git软件
3. 本地HTML网页文件

### 优势说明
- **完全免费**：GitHub Pages提供免费托管
- **无需配置**：不需要复杂的服务器配置
- **访问稳定**：GitHub的服务器稳定可靠
- **移动端友好**：支持所有设备访问
- **自动部署**：推送代码后自动更新网站

## 第一步：下载安装配置Git

### 1. 下载Git

**官方下载地址**：https://git-scm.com/downloads

**下载技巧**：
- 使用迅雷下载可以达到几M每秒
- 如果使用浏览器下载速度会很慢，亲测有效

### 2. 安装Git

安装过程比较简单，基本一路Next即可：

```bash
# 安装完成后验证
git --version
```

### 3. 配置Git

安装完成后需要配置用户信息：

```bash
# 配置用户名
git config --global user.name "你的GitHub用户名"

# 配置邮箱
git config --global user.email "你的GitHub邮箱"

# 查看配置
git config --list
```

## 第二步：创建GitHub仓库

### 1. 登录GitHub

访问 https://github.com 并登录你的账号

### 2. 创建新仓库

点击右上角的 "+" 号，选择 "New repository"

### 3. 仓库设置

**重要配置**：
- **Repository name**：`你的用户名.github.io`（这个格式很重要！）
- **Description**：可选，填写仓库描述
- **Public**：必须选择Public（公开）
- **Initialize this repository with**：勾选"Add a README file"

**示例**：
如果你的GitHub用户名是 `zhangsan`，那么仓库名应该是 `zhangsan.github.io`

### 4. 创建仓库

点击 "Create repository" 完成创建

## 第三步：克隆仓库到本地

### 1. 获取仓库地址

在仓库页面点击绿色的 "Code" 按钮，复制HTTPS地址

### 2. 选择本地目录

在你想要存放项目的目录下右键，选择 "Git Bash Here"

### 3. 克隆仓库

```bash
# 克隆仓库到本地
git clone https://github.com/你的用户名/你的用户名.github.io.git

# 进入项目目录
cd 你的用户名.github.io
```

## 第四步：添加网页文件

### 1. 准备HTML文件

确保你的本地网页文件结构正确：

```
你的项目文件夹/
├── index.html          # 主页文件（必须）
├── css/
│   └── style.css      # 样式文件
├── js/
│   └── script.js      # JavaScript文件
├── images/
│   ├── logo.png       # 图片文件
│   └── background.jpg
└── other-pages/
    ├── about.html     # 其他页面
    └── contact.html
```

### 2. 复制文件到仓库

将你的所有网页文件复制到克隆下来的仓库文件夹中

**重要提醒**：
- 主页文件必须命名为 `index.html`
- 文件路径不能有中文
- 图片路径使用相对路径

### 3. 检查文件结构

```bash
# 查看文件列表
ls -la

# 确保index.html存在
ls index.html
```

## 第五步：上传到GitHub

### 1. 添加文件到Git

```bash
# 添加所有文件
git add .

# 或者添加特定文件
git add index.html
git add css/
git add js/
git add images/
```

### 2. 提交更改

```bash
# 提交文件，添加提交信息
git commit -m "Add my website files"
```

### 3. 推送到GitHub

```bash
# 推送到远程仓库
git push origin main
```

**可能遇到的问题**：
如果推送失败，可能是分支名称问题，尝试：

```bash
# 查看当前分支
git branch

# 如果是master分支，推送到master
git push origin master
```

## 第六步：启用GitHub Pages

### 1. 进入仓库设置

在GitHub仓库页面，点击 "Settings" 选项卡

### 2. 找到Pages设置

向下滚动找到 "Pages" 部分

### 3. 配置Pages

**Source设置**：
- 选择 "Deploy from a branch"
- Branch: 选择 "main" (或 "master")
- Folder: 选择 "/ (root)"

### 4. 保存设置

点击 "Save" 保存设置

### 5. 获取网站地址

设置完成后，GitHub会显示你的网站地址：
`https://你的用户名.github.io`

## 第七步：验证网站

### 1. 等待部署

GitHub Pages部署通常需要几分钟时间

### 2. 访问网站

在浏览器中访问 `https://你的用户名.github.io`

### 3. 移动端测试

用手机浏览器访问网站，确保显示正常

## 常见问题解决

### 1. 网站显示404错误

**可能原因**：
- 仓库名格式不正确
- 没有index.html文件
- GitHub Pages未启用

**解决方案**：
```bash
# 确保有index.html文件
ls index.html

# 确保文件已推送
git status
git push origin main
```

### 2. 样式和图片无法加载

**可能原因**：
- 路径使用了绝对路径
- 文件路径有中文
- 大小写不匹配

**解决方案**：
```html
<!-- 错误的路径 -->
<link rel="stylesheet" href="C:/Users/Desktop/style.css">
<img src="D:/images/logo.png">

<!-- 正确的相对路径 -->
<link rel="stylesheet" href="css/style.css">
<img src="images/logo.png">
```

### 3. 更新后网站没有变化

**可能原因**：
- 浏览器缓存
- GitHub Pages缓存
- 文件没有正确推送

**解决方案**：
```bash
# 强制刷新浏览器（Ctrl+F5）
# 或者清除浏览器缓存

# 检查文件是否推送成功
git log --oneline

# 重新推送
git add .
git commit -m "Update website"
git push origin main
```

### 4. 中文字符显示乱码

**解决方案**：
```html
<!-- 在HTML头部添加字符编码 -->
<meta charset="UTF-8">
```

## 高级配置

### 1. 自定义域名

如果你有自己的域名，可以配置自定义域名：

1. 在仓库根目录创建 `CNAME` 文件
2. 在文件中写入你的域名，如：`www.yourdomain.com`
3. 在域名服务商处配置DNS解析

### 2. HTTPS配置

GitHub Pages默认支持HTTPS，在设置中可以强制启用：
- 勾选 "Enforce HTTPS" 选项

### 3. 自定义404页面

创建 `404.html` 文件，用户访问不存在的页面时会显示此页面

```html
<!DOCTYPE html>
<html>
<head>
    <title>页面未找到</title>
    <meta charset="UTF-8">
</head>
<body>
    <h1>404 - 页面未找到</h1>
    <p>抱歉，您访问的页面不存在。</p>
    <a href="/">返回首页</a>
</body>
</html>
```

## 最佳实践

### 1. 代码管理

```bash
# 创建开发分支
git checkout -b develop

# 在开发分支上工作
# 完成后合并到主分支
git checkout main
git merge develop
```

### 2. 文件组织

```
项目根目录/
├── index.html          # 首页
├── about.html          # 关于页面
├── assets/             # 资源文件夹
│   ├── css/
│   ├── js/
│   └── images/
├── pages/              # 其他页面
├── README.md           # 项目说明
└── .gitignore         # Git忽略文件
```

### 3. SEO优化

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>网站标题</title>
    <meta name="description" content="网站描述">
    <meta name="keywords" content="关键词1,关键词2">
</head>
<body>
    <!-- 网页内容 -->
</body>
</html>
```

## 总结

使用GitHub Pages部署网站的优势：

### 优点
1. **完全免费**：无需任何费用
2. **操作简单**：几个步骤即可完成
3. **稳定可靠**：基于GitHub的基础设施
4. **版本控制**：自带Git版本管理
5. **自动部署**：推送代码自动更新网站

### 适用场景
- **个人作品展示**：作品集、简历网站
- **项目文档**：开源项目文档站点
- **静态博客**：技术博客、个人日记
- **活动页面**：会议、活动宣传页面

### 限制说明
- 只支持静态网站（HTML、CSS、JavaScript）
- 不支持服务器端语言（PHP、Python等）
- 仓库大小限制1GB
- 月流量限制100GB

通过这个教程，你应该能够成功将本地网页部署到GitHub Pages，让全世界都能访问你的网站！

---

**实践总结**：GitHub Pages是一个优秀的免费网站托管平台，掌握这个技能对于展示个人项目和作品集非常有价值。

## 相关资源

- **GitHub Pages官方文档**：https://pages.github.com/
- **Git官方教程**：https://git-scm.com/docs
- **HTML/CSS学习资源**：https://www.w3schools.com/