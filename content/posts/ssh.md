+++
title = 'SSH 学习笔记'
date = "2025-11-02T16:46:00+08:00"
draft = false
tags = ["ssh", "网络协议", "远程登录"]
categories = ["工程技术"]
summary = "SSH 学习笔记，涵盖基本概念、密钥生成、远程登录、文件传输及配置优化等内容，适合开发者和运维人员参考。"
+++
### 1. 什么是ssh

SSH (Secure Shell) 是一种加密网络协议，用于在不安全的网络中安全地进行远程登录和命令执行。

### 2. 为什么要学习ssh

*   **解决日常问题**：GitHub代码推送/拉取、远程服务器管理。
*   **安全**：所有传输数据都经过加密，比明文协议（如Telnet、FTP）安全得多。
*   **高效**：通过密钥登录，无需每次输入密码，可轻松实现自动化脚本。
*   **必备技能**：是程序员、运维、网络安全人员的核心基本功。

### 3. 要学到什么程度

开发工程师

*   **核心**：熟练使用**密钥对**进行登录，完全替代密码。
*   **常用命令**：掌握 `ssh` (登录)、`scp` (文件传输)、`sftp` (交互式文件传输) 的基本用法。
*   **配置优化**：会使用 `~/.ssh/config` 文件管理多个服务器，简化连接。
*   **了解即可**：知道端口转发（`-L`, `-R`）是什么，能在需要时查到并使用。

---

### 4. 具体学习笔记

#### 4.1 密钥对生成

* **命令**：

  ```bash
  # 推荐使用 ed25519 算法，更安全更快速
  ssh-keygen -t ed25519 -C "你的邮箱@example.com"
  # 或者使用传统的 RSA 算法
  ssh-keygen -t rsa -b 4096 -C "你的邮箱@example.com"
  ```

* **说明**：

  *   `-t` 指定密钥类型。
  *   `-C` 添加注释，通常是邮箱，方便识别。
  *   `passphrase` 为该密钥设置的密码。日常可跳过,生产环境建议设置。
  *   默认生成在 `~/.ssh/` 目录下。
  *   `id_ed25519` (或 `id_rsa`) 是**私钥**，必须保密！
  *   `id_ed25519.pub` (或 `id_rsa.pub`) 是**公钥**，可以公开。

#### 4.2 复制公钥到服务器/GitHub

* **方法一：自动复制 (仅限Linux/macOS服务器)**

  ```bash
  ssh-copy-id user@hostname
  ```

  这会自动把公钥添加到服务器的 `~/.ssh/authorized_keys` 文件中。

* **方法二：手动复制 (适用于GitHub等)**

  1. **复制公钥内容**：

     ```bash
     # Linux (Windows Git Bash)
     cat ~/.ssh/id_ed25519.pub
     # 然后手动复制终端输出的全部内容
     ```

  2. **粘贴**：登录到GitHub/GitLab/服务器的设置页面，找到SSH Keys选项，粘贴进去。

#### 4.3 基本远程登录

* **标准登录**：

  ```bash
  ssh user@hostname
  ```

* **指定端口登录**：

  ```bash
  ssh -p 2222 user@hostname
  ```

#### 4.4 文件传输

* **`scp` (Secure Copy)**：一次性文件传输。

  ```bash
  # 上传本地文件到服务器
  scp /path/to/local_file user@hostname:/path/to/remote_directory
  
  # 从服务器下载文件到本地
  scp user@hostname:/path/to/remote_file /path/to/local_directory
  
  # 上传整个文件夹 (加 -r 参数)
  scp -r /path/to/local_folder user@hostname:/path/to/remote_directory
  ```

* **`sftp` (SSH File Transfer Protocol)**：交互式文件传输。

  ```bash
  sftp user@hostname
  ```

  进入后，类似FTP，可以使用 `get` (下载), `put` (上传), `ls`, `cd` 等命令。

#### 4.5 配置文件 (`~/.ssh/config`)

* **作用**：为常用服务器创建别名，简化登录命令，管理不同密钥。

* **示例**：

  ```
  # GitHub
  Host github.com
      HostName github.com
      User git
      IdentityFile ~/.ssh/id_ed25519_github
  
  # 我的阿里云服务器
  Host aliyun
      HostName 120.27.241.88
      User root
      Port 22
      IdentityFile ~/.ssh/id_ed25519_aliyun
  ```

* **使用**：配置后，原来复杂的命令 `ssh -i ~/.ssh/id_ed25519_aliyun root@120.27.241.88` 变为 `ssh aliyun`。

---

### 5. 具体实践 (以GitHub为例)

**目标**：实现 `git clone`, `git push` 等操作无需输入密码。

1. **生成新密钥** (如果还没有)：

   ```bash
   ssh-keygen -t ed25519 -C "你的GitHub注册邮箱"
   ```

2. **将公钥添加到GitHub**：

   *   复制公钥：`cat ~/.ssh/id_ed25519.pub`
   *   登录GitHub -> `Settings` -> `SSH and GPG keys` -> `New SSH key`。
   *   粘贴公钥内容，保存。

3. **测试连接**：

   ```bash
   ssh -T git@github.com
   ```

   看到 `Hi xxx! You've successfully authenticated...` 即为成功。

4. **使用SSH方式克隆仓库**：

   ```bash
   # 仓库地址选择 SSH 类型
   git clone git@github.com:用户名/仓库名.git
   ```

   之后的所有 `git push/pull` 操作都将通过SSH密钥自动认证。



### FAQ

**Q1：WSL支持自动集成SSH密钥吗？**

> 不会“自动继承”。在 WSL 里，Linux 子系统有自己独立的 `~/.ssh` 与 ssh 配置；Windows 里的密钥/配置（通常在 `C:\Users\xx\.ssh`）不会被自动使用。可在Linux下的`~/.ssh/config`中将`IdentityFile `改为`/mnt/c/Users/xx/.ssh/id_ed25519`

