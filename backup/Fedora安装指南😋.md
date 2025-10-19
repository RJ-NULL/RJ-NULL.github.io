# Fedora 安装指南（Fedora Workstation）

## 一、准备工作

### 1. 下载 Fedora 镜像
- 访问官方下载页面：[https://getfedora.org/](https://getfedora.org/)
- 也可在其他镜像站下载：[https://mirrors.tuna.tsinghua.edu.cn/fedora/releases/](https://mirrors.tuna.tsinghua.edu.cn/fedora/releases/)
- 选择 **Workstation** 版本（桌面用户推荐）
- 点击“Download Now”下载 ISO 文件（通常为 `.iso` 格式，约 2–3 GB）

### 2. 验证镜像（可选但推荐）
- 在下载页面找到对应的 **SHA256 校验值**
- 在终端中运行（Linux/macOS）：
  ```bash
  sha256sum ~/Downloads/Fedora-Workstation-Live-x86_64-*.iso
  ```
- 比对输出是否与官网一致，确保文件完整无损。

### 3. 制作可启动 U 盘
#### Windows 用户：
- 使用 [Rufus](https://rufus.ie/)（推荐）或 [Balena Etcher](https://www.balena.io/etcher/)
- 插入 U 盘（≥8GB）
- 选择下载的 ISO 文件，写入方式选择 **DD 模式**（Rufus 中）或默认设置（Etcher）
- 点击“开始”制作启动盘

#### Linux/macOS 用户：
- 使用 `dd` 命令（谨慎操作）：
  ```bash
  sudo dd if=~/Downloads/Fedora-Workstation-Live-*.iso of=/dev/sdX bs=4M status=progress oflag=sync
  ```
  > ⚠️ 注意：`/dev/sdX` 是你的 U 盘设备（如 `/dev/sdb`），请用 `lsblk` 确认，避免误写系统盘！

---

## 二、安装 Fedora

### 1. 从 U 盘启动
- 插入制作好的启动 U 盘
- 重启电脑，在启动时按 **启动菜单键**（通常是 F2、F12、Esc 或 Del，具体取决于主板）
- 选择 U 盘设备（如 “USB: SanDisk”）启动

### 2. 进入 Live 环境
- 系统会加载 Fedora Live 桌面（无需安装即可试用）
- 点击桌面图标 **“Install to Hard Drive”** 开始安装

### 3. 安装向导步骤

#### 步骤 1：选择语言
- 选择你偏好的语言（如“简体中文”），点击“继续”

#### 步骤 2：键盘布局
- 默认为英文，可点击“+”添加中文输入法（如“Chinese (Intelligent Pinyin)”）
- 点击“继续”

#### 步骤 3：安装目标（分区）
- **推荐新手选择“自动分区”**：
  - 点击“安装目标”
  - 选择你的硬盘（通常自动选中）
  - 勾选“自动分区” → 点击“完成”
- 如需手动分区（高级用户）：
  - 选择“我要配置分区”
  - 建议至少创建：
    - `/`（根分区）：ext4，建议 ≥30GB
    - `swap`：建议为内存大小或 2–8GB（若内存 ≥8GB 可省略）
    - `/home`（可选）：存放用户数据，ext4

> 💡 提示：如果双系统（如与 Windows 共存），请提前在 Windows 中压缩卷腾出未分配空间。

#### 步骤 4：网络与主机名（可选）
- 可在此设置主机名（如 `my-fedora`）
- 连接 Wi-Fi（如有需要）

#### 步骤 5：设置 root 密码和用户
- **Root 密码**：建议设置强密码（但日常使用不推荐用 root 登录）
- **创建用户**：
  - 填写全名、用户名（如 `alice`）
  - 设置登录密码
  - 勾选“将此用户设为管理员”（赋予 sudo 权限）

#### 步骤 6：开始安装
- 点击右上角“开始安装”
- 安装过程约 5–15 分钟，期间可继续设置用户信息

---

## 三、首次启动

1. 安装完成后，点击“重启系统”
2. 拔出 U 盘
3. 系统将从硬盘启动，进入登录界面
4. 使用你创建的用户名和密码登录
5. 其他选项就不细讲了，注意一定要开启“第三方软件源”

---

## 四、初始配置（推荐）

### 1. 更新系统
打开终端（Terminal），运行：
```bash
sudo dnf upgrade --refresh
```

### 2. 安装常用软件
```bash
# 安装多媒体编解码器（如 MP3、H.264）
sudo dnf groupupdate multimedia --setop="install_weak_deps=False" --exclude=PackageKit-gstreamer-plugin

# 安装 RPM Fusion 仓库（第三方软件源）
sudo dnf install https://mirrors.rpmfusion.org/free/fedora/rpmfusion-free-release-$(rpm -E %fedora).noarch.rpm https://mirrors.rpmfusion.org/nonfree/fedora/rpmfusion-nonfree-release-$(rpm -E %fedora).noarch.rpm

# 安装常用工具
sudo dnf install gnome-tweaks vlc git htop neofetch
```

### 3. 启用 Flatpak（可选）
Fedora 默认支持 Flatpak，可从 GNOME 软件中心和Flatpak官网安装更多应用：
```bash
flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo
```

---

## 五、常见问题

- **无法启动？** 检查 BIOS/UEFI 设置，确保启用 UEFI 模式（非 Legacy）
- **Wi-Fi 不工作？** 可能需要安装专有驱动（如 Broadcom），通过 RPM Fusion 安装
- **忘记密码？** 可通过 GRUB 启动菜单重置 root 密码

---

## 六、后续学习资源

- 官方文档：[https://docs.fedoraproject.org/](https://docs.fedoraproject.org/)
- 中文社区：[https://fedoracommunity.org/](https://fedoracommunity.org/)（英文为主，但有活跃论坛）
- DNF 包管理命令速查：`man dnf`

---

后续我应该会写一个gnome美化指南，敬请期待吧🎉