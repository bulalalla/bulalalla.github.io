---
title: "Typora配置Github图床"
author: Winter.W
date: 2025-02-28
categories: [其它]
tags: [typora, images upload, github]
math: true
---





---

Typora是一款非常方便的文本编辑器，用来编写博客非常方便。在使用过程中你可能经常遇到这样的问题：博客中使用到的图片，有没有很好的存储方式？

一般有以下几种方式：

- 存储在本地：当把 `.md` 文件复制到其它电脑时，就无法显示，虽然可以导出pdf，但我认为这样还不是很方便
- 存储到图床：
  - 网络上的免费图床：不敢保证服务器持有者能够维护多久
  - Github图床：非常推荐！

本文记录了我Typora上配置Github图床的过程。

### 1. **准备工作**
- **Typora**: 确保已安装最新版本的 Typora，笔者当前为：`1.5.10`。
- **PicGo**: 下载并安装 PicGo（推荐使用 PicGo-Core）。
- **GitHub 仓库**: 创建一个公开的 GitHub 仓库，用于存放图片。
- **GitHub Token**: 生成一个 GitHub 的 Personal Access Token（PAT），用于授权 PicGo 上传图片。

---

### 2. **创建 GitHub 仓库并生成 Token**
1. **创建仓库**：
   - 在 GitHub 上新建一个仓库（如 `my-image-repo`），确保仓库是公开的（Public）。
   - 在仓库中创建一个 `assets` 目录，用于存放图片。

2. **生成 Token**：
   - 登录 GitHub，进入 **Settings** -> **Developer settings** -> **Personal access tokens**。
   - 点击 **Generate new token**，勾选 `repo` 权限。
   - 生成 Token 后，复制并保存（Token 只会显示一次）。

---

### 3. **配置 PicGo**

#### 3.1 桌面端PicGo配置

1. **安装 PicGo**：
   - 如果使用 PicGo 桌面版，从 [PicGo 官网](https://molunerfinn.com/PicGo/) 下载并安装。
   
2. **配置 GitHub 图床**：
   - 打开 PicGo，进入 **图床设置** -> **GitHub**。
   - 填写以下信息：
     - **仓库名**: `用户名/仓库名`（如 `your-username/my-image-repo`）。
     - **分支**: `main` 或 `master`。
     - **Token**: 粘贴之前生成的 GitHub Token。
     - **存储路径**: `assets/`（表示图片将上传到 `assets` 目录）。
     - **自定义域名**: 留空（默认使用 GitHub 的原始链接）。

3. **验证配置**：
   - 在 PicGo 中上传一张测试图片，检查是否成功上传到 GitHub 仓库的 `assets` 目录。

#### 3.2 PicCore配置

1. **安装 PicGo-Core**

   - 打开 Typora，进入 **文件** -> **偏好设置** -> **图像**。

   - 在 **上传服务** 中选择 **PicGo-Core(command line)**。

   - 点击 **下载或更新**，安装 PicGo-Core。

2. **配置 GitHub 图床**

- 打开 Typora 的配置文件：

  - 在 Typora 的图像设置中，点击 **打开配置文件**。

  - 这会打开一个 `picgo.json` 文件（如果没有，可以手动创建）。

  - 编辑 `picgo.json` 文件，添加 GitHub 图床的配置。以下是一个示例配置：

    ```json
    {
      "picBed": {
        "current": "github",
        "github": {
          "repo": "你的用户名/你的仓库名", // 例如：your-username/my-image-repo
          "branch": "main", // 或 master
          "token": "你的 GitHub Token", // 生成的 Personal Access Token
          "path": "assets/", // 图片上传到仓库的 assets 目录
          "customUrl": "" // 如果需要自定义域名，可以填写
        }
      },
      "picgoPlugins": {
        "github": true // 确保启用 GitHub 插件
      }
    }
    ```

  - **repo**: 你的 GitHub 仓库名，格式为 `用户名/仓库名`。
  - **branch**: 仓库的分支，通常是 `main` 或 `master`。
  - **token**: 你的 GitHub Personal Access Token（生成方法见下文）。
  - **path**: 图片上传的路径，例如 `assets/`。
  - **customUrl**: 如果需要自定义域名（如 CDN），可以填写。

- 保存 `picgo.json` 文件。

3. **生成 GitHub Token**

   - 登录 GitHub，进入 **Settings** -> **Developer settings** -> **Personal access tokens**。

   - 点击 **Generate new token**，勾选 `repo` 权限。

   - 生成 Token 后，复制并保存（Token 只会显示一次）。



4. **验证配置**

   - 在 Typora 的图像设置中，点击 **验证图片上传选项**。

   - 如果配置正确，会提示验证成功。

5. **使用 Typora 自动上传图片**

   - 在 Typora 中插入图片：

     - 直接粘贴图片，或通过 **格式** -> **图像** -> **插入图片** 选择本地图片。

     - 图片会自动上传到 GitHub 仓库的 `assets` 目录，并在 Markdown 中生成对应的在线链接（如 `https://raw.githubusercontent.com/your-username/my-image-repo/main/assets/your-image.jpg`）。

   - 已经插入到博客中的图片：
     - 右键图片 -> 上传图片：图片即会被自动上传

6. **常见问题**

   1. **避免上传图片时，仓库执行工作流**

      - 简单的方法，可以在 `仓库/.github/workflows/deploy.yml` 文件中，配置：

        ```yml
        on:
          push:
            paths:
              - '!assets/**'  # 忽略 assets 目录下的文件
        ```

        or

        ```yml
        on:
          push:
            paths-ignore:
              - 'assets/**'  # 忽略 assets 目录下的文件
        ```

        **注意：**`paths` 和 `paths-ignore` 只能使用其中的一个。

   2. **上传失败**

      - 检查 `picgo.json` 配置是否正确。

      - 确保 GitHub Token 有 `repo` 权限。

      - 确保仓库是公开的（Public）。


   2. **图片链接无法访问**

      - 确保仓库是公开的。

      - 检查图片路径是否正确。


   3. **PicGo-Core 未安装**

      - 在 Typora 中重新下载 PicGo-Core。

      - 确保 Typora 是最新版本。

---



### 4. **配置 Typora**

1. **打开 Typora**，进入 **文件** -> **偏好设置** -> **图像**。
2. **设置上传服务**：
   - 选择 **PicGo(app)** 或 **PicGo-Core(command line)**。
   - 如果使用 PicGo-Core，点击 **下载或更新** 安装 PicGo-Core。
3. **配置 PicGo 路径**：
   - 如果使用 PicGo 桌面版，填写 PicGo 的安装路径（如 `C:\Users\你的用户名\AppData\Local\Programs\PicGo\PicGo.exe`）。
4. **验证上传功能**：
   - 点击 **验证图片上传选项**，确保配置成功。

---

### 5. **使用 Typora 自动上传图片**
1. **插入图片**：
   - 在 Typora 中直接粘贴图片，或通过 **格式** -> **图像** -> **插入图片** 选择本地图片。
2. **自动上传**：
   - 图片会自动上传到 GitHub 仓库的 `assets` 目录，并在 Markdown 中生成对应的在线链接（如 `https://raw.githubusercontent.com/your-username/my-image-repo/main/assets/your-image.jpg`）。

---

### 6. **注意事项**
- **GitHub 仓库公开性**：确保仓库是公开的，否则图片链接无法访问。
- **Token 安全性**：GitHub Token 只显示一次，务必妥善保存。如果丢失，需要重新生成。
- **PicGo 端口问题**：如果使用 PicGo 桌面版，确保 Typora 和 PicGo 的端口一致（默认 `36677`）。如果端口变动，需在 Typora 中同步修改。

---

通过以上步骤，你可以实现 Typora 图片自动上传到 GitHub 仓库的 `assets` 目录，并生成在线链接。如果有问题，可以参考 [PicGo 官方文档](https://picgo.github.io/PicGo-Doc/zh/guide/) 或相关教程。