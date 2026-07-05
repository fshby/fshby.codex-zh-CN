# Codex Desktop 简体中文语言包

> Windows 版 Codex Desktop 简体中文语言包，一键安装汉化。

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Release](https://img.shields.io/badge/release-v5.5.5-green.svg)](https://github.com/fshby/fshby.codex-zh-CN/codex-zh-CN-v5.5.5.zip)

一键将 [Codex Desktop](https://github.com/openai/codex) 界面切换为**简体中文**，面向 Windows 用户，操作简单。
本项目参考github开源库 [GitHub Issues](https://github.com/xqnode/codex-zh-CN/issues) ，原始版本大 佬已经不更新，所以手动适配了v5.5.5版本兼容codex**26.623.x**系列


## 快速开始

1. 下载 [Release v5.5.5](https://github.com/fshby/fshby.codex-zh-CN/codex-zh-CN-v5.5.5.zip) 的 **`codex-zh-CN-v5.5.5.zip`** 并解压，或克隆本仓库
2. **完全退出** Codex Desktop（任务栏右键退出，不要只关窗口）
3. 双击 `install-windows.bat`（会先弹出 **UAC 管理员授权**，请点击「是」）
4. 在交互菜单中选择 **[1] 安装汉化**（若 Codex 正在运行会先自动关闭，汉化完成后自动重启）
5. **Microsoft Store 版**：日常使用请在与 `install-windows.bat` **同一目录**双击 **「Codex 汉化版.bat」**（无 cmd 黑窗；不要用开始菜单里的 Store 原版快捷方式）

> Store 版 `WindowsApps` 目录受系统保护，无法原地修改。汉化会复制到 `%USERPROFILE%\.codex\zh-cn-patched\`，并在安装目录生成 `Codex 汉化版.bat` 与 `launch-codex-zh-cn.ps1`。

若界面仍是英文，打开 **Settings → General → Language**，选择 **中文（中国）**。

若提示 `app.asar 缺失`，请在 Microsoft Store 中对 Codex 执行 **修复/重置** 后重新安装汉化。

## 交互菜单

运行 `install-windows.bat` 后会先显示**环境检测报告**，再进入菜单：

| 选项 | 说明 |
|------|------|
| **1** | 安装汉化 |
| **2** | 恢复英文 / 重置 |
| **3** | 验证补丁结果 |
| **4** | 重新检测环境 |
| **5** | 手动指定 / 清除 Codex 安装路径 |
| **Q** | 退出 |

恢复英文：在同一菜单中选择 **[2] 恢复英文 / 重置**。

## 汉化范围

- 顶部菜单及子项（文件、编辑、查看、窗口、帮助）
- 主进程硬编码菜单与对话框文案
- Webview 缺失的中文词条补充（`codex.commandMenuTitle.*` 等 45 条）
- 内置插件显示名称与描述（Browser / Chrome / LaTeX / Sites / Computer Use 等）
- 语言配置：`localeOverride = "zh-CN"`

## 支持的 Codex 版本

| 版本 | 状态 | 说明 |
|------|------|------|
| 26.519.x | ✅ 已验证 | 旧版 `};export` 格式 |
| 26.601.x | ✅ 已验证 | 旧版 `};export` 格式 |
| **26.623.x** | ✅ **已修复** | 新版 IIFE 格式 `}))();export{n as default,t as greeting};`，需脚本 v0.1.2+ |

> Codex 大版本升级后若界面变回英文，请**重新运行** `install-windows.bat` → 选 `[1] 安装汉化` 即可。脚本会自动识别新版本的 bundle 导出格式并重新打补丁。

## 脚本会做什么

1. 自动查找 Codex 安装目录（见下方「安装目录识别」）
2. 备份 `resources/app.asar` 和 `Codex.exe`
3. 写入完整 `native-menu-locales/zh-CN.json`
4. 汉化主进程硬编码菜单
5. 补充 webview 中文词条
6. 汉化 `%USERPROFILE%\.codex` 中内置插件 metadata
7. 写入 `%USERPROFILE%\.codex\config.toml`：`localeOverride = "zh-CN"`
8. 同步 `Codex.exe` 内嵌的 asar 完整性哈希
9. 安装完成后尝试启动 Codex

## 更新 Codex 后

Codex 更新可能覆盖 `app.asar` 或插件缓存。若界面变回英文，**重新运行** `install-windows.bat` 即可。

## 项目结构

| 文件 | 说明 |
|------|------|
| `install-windows.bat` | 唯一入口：打开 PowerShell 交互菜单（安装 / 重置 / 验证等） |
| `uninstall-codex.bat` | 彻底卸载 Codex（Store 包 + 数据目录，需管理员，纯英文脚本） |
| `Codex 汉化版.bat` | 安装后生成于本目录，双击启动汉化版（入口） |
| `launch-codex-zh-cn.ps1` | PowerShell 启动脚本（写入语言配置并拉起 Codex） |
| `launchers/` | 启动脚本模板（安装时复制到项目根目录） |
| `scripts/install_windows.ps1` | 交互式安装器 |
| `scripts/patch-codex-zh-cn.mjs` | 补丁核心逻辑 |
| `resources/native-menu-zh-CN.json` | 原生菜单中文翻译 |
| `resources/menu-hardcoded-zh-CN.json` | 主进程硬编码菜单替换表 |
| `resources/bundled-plugins-zh-CN.json` | 内置插件显示名称与描述 |

## 安装目录识别

只要目录结构满足以下任一形式即可识别（与安装位置无关）：

| 布局 | 示例 |
|------|------|
| 便携版 / 解压版 | `...\resources\app.asar` |
| Microsoft Store (MSIX) | `...\OpenAI.Codex_*\app\resources\app.asar` |

自动检测顺序（由高到低）：

1. 命令行 `--codex-path` 或菜单 **[5] 手动指定**（会保存到 `%USERPROFILE%\.codex\codex-desktop-path.txt`）
2. 环境变量 `CODEX_DESKTOP_PATH` / `CODEX_ZH_CN_PATH`
3. 正在运行的 `Codex` / `codex` 进程路径（向上查找 `app.asar`）
4. Microsoft Store：`WindowsApps\OpenAI.Codex_*`（含 `Get-AppxPackage`）
5. 注册表卸载项中的 `InstallLocation`（显示名为 Codex 的官方安装）
6. 常见父目录下名为 `Codex*` / `OpenAI.Codex*` 的子目录：`%LOCALAPPDATA%\Programs`、`%ProgramFiles%\Codex`、`D:\soft`、`E:\soft`、桌面、下载目录等

自定义路径可填 **MSIX 包根目录**（含 `app` 子目录的那一层）或 **含 `resources\app.asar` 的 app 目录**。

## 命令行（可选）

```powershell
# 环境检测
powershell -File scripts\install_windows.ps1 -Action status

# 非交互安装（便携目录或 MSIX 包根目录均可）
powershell -File scripts\install_windows.ps1 -Action install -CodexPath "D:\path\to\Codex-win-x64-xxx"
powershell -File scripts\install_windows.ps1 -Action install -CodexPath "C:\Program Files\WindowsApps\OpenAI.Codex_26.xxx_x64__2p2nqsd0c76g0"

# 环境变量（当前终端会话）
$env:CODEX_DESKTOP_PATH = "D:\tools\Codex-win-x64-xxx"

# 非交互重置
powershell -File scripts\install_windows.ps1 -Action uninstall -CodexPath "D:\path\to\Codex"
```

## 交流与反馈

使用中遇到问题、或有汉化遗漏，欢迎扫码加入交流群反馈：

<img width="992" height="895" alt="386e90c24af74f2d227f411e41832463" src="https://github.com/user-attachments/assets/f0e1c1e9-0130-46e8-8ceb-f081448d0852" />

<p align="center">微信扫码加入交流群</p>

## 本次修改记录（2026-07-05 / v0.1.2 + patch）

### 修复：`scripts/patch-codex-zh-cn.mjs` 中 `patchZhBundle` 函数

**问题**：在 Codex 26.623.x 上首次运行安装时，脚本在第 5 步报错「无法定位 zh-CN bundle 导出位置」。原因是新版将 webview zh-CN bundle 的导出结构从：
```js
,..."key":`value`};export{...}
```
改为了 IIFE 形式：
```js
,..."key":`value`}}))();export{n as default,t as greeting};
```

**修复**：将原有的 `text.lastIndexOf("};export")` 改为**双模式检测**（见 `patch-codex-zh-cn.mjs` 第 955-983 行）：

```js
// 新格式 (Codex 26.623+)
let insertAt = text.lastIndexOf("}))();export");
if (insertAt >= 0) {
  insertAt = insertAt - 1;   // n={...} 的最后一个 } 前插入
} else {
  // 旧格式 (Codex 26.519 / 26.601)
  insertAt = text.lastIndexOf("});export");
}
```

### 汉化成功的项目

- ✅ `native-menu-locales/zh-CN.json`：73 个原生菜单 key
- ✅ webview zh-CN bundle：45 个 `codex.commandMenuTitle.*` 词条补充
- ✅ 主进程菜单字符串替换：文件 / 编辑 / 视图 / 窗口 / 帮助 / 退出 / 撤销 / 重做 / 剪切 / 复制 / 粘贴 / 全选 / 最小化 / 关闭 等
- ✅ `config.toml`：`localeOverride = "zh-CN"`
- ✅ 插件 metadata：Browser(浏览器) / Chrome / LaTeX / Sites(站点) / Computer Use(计算机使用)
- ✅ Marketplace 分类："OpenAI 内置"
- ✅ 启动脚本：`Codex 汉化版.bat` / `launch-codex-zh-cn.bat` / `launch-codex-zh-cn.ps1`
- ✅ 备份：`app.asar` + `Codex.exe` 备份到 `%USERPROFILE%\.codex\zh-cn-install-backups\`

## 使用方法速查

```
【首次安装】
1. 完全退出 Codex（托盘右键 → 退出）
2. 双击 install-windows.bat → 允许 UAC 提权
3. 菜单选择 [1] 安装汉化
4. 完成后，双击同目录下「Codex 汉化版.bat」启动 Codex

【日常启动】
Microsoft Store 版：     双击 项目根目录\Codex 汉化版.bat   （不要用 Store 原版快捷方式）
便携版 / 解压版：        Codex 主程序会就地中文启动

【Codex 大版本升级后】
重新双击 install-windows.bat → [1] 安装汉化  即可。

【恢复英文】
双击 install-windows.bat → [2] 恢复英文/重置
从备份中恢复 app.asar 与 Codex.exe，并清除 config.toml 中的 localeOverride。

【指定安装路径】
install-windows.bat → [5] 手动指定 Codex 安装目录
（路径需包含 resources\app.asar，或为 MSIX 包根目录）

【验证当前补丁状态】
install-windows.bat → [3] 验证补丁结果
或命令行： node scripts\patch-codex-zh-cn.mjs status --json

【环境检测】
install-windows.bat → 进入菜单后将显示环境检测报告
或命令行： node scripts\patch-codex-zh-cn.mjs status --json
```

## 文件结构速览

```
codex-zh-CN-v0.1.2/
├── install-windows.bat                ← 用户入口 (UAC 提权 → 打开 PowerShell 菜单)
├── Codex 汉化版.bat                   ← 安装后生成：Store 版汉化启动入口
├── launch-codex-zh-cn.bat             ← 备用启动入口（ASCII 文件名）
├── launch-codex-zh-cn.ps1             ← PowerShell 启动逻辑（写 config.toml + 拉起副本）
├── launchers/                         ← 启动脚本模板（安装时复制到项目根目录）
├── scripts/
│   ├── install_windows.ps1            ← PowerShell 交互安装器（调用 Node.js 脚本）
│   ├── patch-codex-zh-cn.mjs          ← ★ 补丁核心：asar 读写 / EXE 哈希同步 / 插件汉化
│   ├── verify-patch.mjs               ← 验证辅助脚本
│   ├── generate-resources.mjs         ← 资源生成工具
│   ├── find-exe-hash.mjs              ← 定位 EXE 内嵌完整性标记工具
│   ├── inspect-asar-menu.mjs          ← 检查 asar 菜单内容工具
│   └── uninstall-codex-store.ps1      ← Store 版卸载脚本
├── resources/
│   ├── native-menu-zh-CN.json         ← 原生菜单翻译（73 键）
│   ├── menu-hardcoded-zh-CN.json      ← 主进程硬编码字符串替换表
│   ├── bundled-plugins-zh-CN.json     ← 内置插件 displayName / description
│   └── release.json                   ← 发行信息与支持版本
└── README.md                          ← 本文档

运行时写入的目录：
  %USERPROFILE%\.codex\zh-cn-patched\<hash>\app\    ← Store 版汉化副本
  %USERPROFILE%\.codex\zh-cn-install-backups\<hash>\latest\  ← 原始文件备份
  %USERPROFILE%\.codex\config.toml                   ← localeOverride = "zh-CN"
  %USERPROFILE%\.codex\codex-desktop-path.txt        ← 手动指定的 Codex 路径
```

## 已知事项

- `scripts/patch-codex-zh-cn.mjs` 的 `isAsarLocalized()` 检测函数使用的是字符串 `关于 ${n.app.getName()}` 来判断 About 菜单是否汉化；新版 Codex 实际输出的是 `关于 {appName}`，因此 status 报告中 `asarLocalized` 可能显示为 `false`——这**不影响实际使用**，所有菜单文本均已正确替换为中文。
- Store 版 WindowsApps 目录受系统保护，**无法就地修改**——本项目采用「复制到用户目录再打补丁」的方案解决，启动时通过 `Codex 汉化版.bat` 拉起副本。
- 需要系统中已安装 **Node.js**（脚本用 `node` 命令做 asar 二进制文件读写；PowerShell 层会自动检测并提示）。

## 免责声明

本项目为**非官方**中文补丁，仅修改本机 Codex Desktop 的本地资源文件，与 OpenAI 无关。使用风险自负。

## License

[MIT](LICENSE)
