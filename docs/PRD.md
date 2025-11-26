# DeepSeek Desktop - 产品需求文档 (PRD)

> 版本: 1.0.0  
> 更新日期: 2024-11-27  
> 状态: 待开发

---

## 1. 产品概述

### 1.1 产品名称
**DeepSeek Desktop** - 一键部署本地 AI 助手

### 1.2 产品定位
为 Windows 用户提供一键式本地部署 DeepSeek 大语言模型的解决方案，集成 Ollama 运行时和 Chatbox 客户端，实现开箱即用的本地 AI 对话体验。

### 1.3 目标用户
- 希望在本地运行 AI 模型保护隐私的用户
- 不熟悉命令行操作的普通用户
- 需要离线使用 AI 助手的用户

### 1.4 核心价值
- **一键安装**: 无需技术背景，自动完成所有配置
- **隐私保护**: 数据完全本地化，无需联网使用
- **开箱即用**: 安装即可开始 AI 对话

---

## 2. 技术架构

### 2.1 技术选型

| 组件 | 技术方案 | 说明 |
|------|----------|------|
| 桌面框架 | Wails v2 | Go + Web 前端，轻量级跨平台 |
| 前端 | React + TypeScript + TailwindCSS | 现代化 UI |
| 模型运行时 | Ollama | 本地 LLM 运行引擎 |
| 模型 | DeepSeek (deepseek-r1:1.5b/7b/8b) | 根据用户硬件选择 |
| 对话客户端 | Chatbox | 开源 AI 对话客户端 |
| 安装打包 | NSIS | Windows 安装程序 |

### 2.2 系统架构图

```
┌─────────────────────────────────────────────────────────┐
│                  DeepSeek Desktop                        │
│  ┌─────────────────────────────────────────────────┐    │
│  │              Wails 应用 (Go + React)             │    │
│  │  ┌─────────────┐  ┌─────────────┐  ┌─────────┐  │    │
│  │  │  安装向导   │  │  状态监控   │  │ 启动器  │  │    │
│  │  └─────────────┘  └─────────────┘  └─────────┘  │    │
│  └─────────────────────────────────────────────────┘    │
│                          │                               │
│         ┌────────────────┼────────────────┐             │
│         ▼                ▼                ▼             │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐     │
│  │   Ollama    │  │  DeepSeek   │  │   Chatbox   │     │
│  │  (运行时)   │◄─┤   (模型)    │  │  (客户端)   │     │
│  └─────────────┘  └─────────────┘  └─────────────┘     │
└─────────────────────────────────────────────────────────┘
```

### 2.3 系统要求

| 项目 | 最低要求 | 推荐配置 |
|------|----------|----------|
| 操作系统 | Windows 10 64位 | Windows 11 64位 |
| 内存 | 8GB | 16GB+ |
| 磁盘空间 | 10GB | 20GB+ |
| GPU | 无 (CPU 运行) | NVIDIA 6GB+ 显存 |

---

## 3. 功能模块

### 3.1 安装向导模块

#### 3.1.1 环境检测
- 检测 Windows 版本（最低 Windows 10 64位）
- 检测系统内存（建议 16GB+）
- 检测磁盘空间（需要 10GB+）
- 检测 GPU（可选，NVIDIA GPU 可加速）
- 检测是否已安装 Ollama / Chatbox

#### 3.1.2 组件安装流程

| 步骤 | 操作 | 说明 |
|------|------|------|
| 1 | 下载 Ollama | 从官方源下载 Windows 版本 |
| 2 | 安装 Ollama | 静默安装到指定目录 |
| 3 | 启动 Ollama | 启动 Ollama 服务 |
| 4 | 拉取模型 | `ollama pull deepseek-r1:8b` |
| 5 | 下载 Chatbox | 从官方源下载 |
| 6 | 安装 Chatbox | 解压到指定目录 |
| 7 | 配置 Chatbox | 自动配置连接本地 Ollama |

#### 3.1.3 模型选择
根据用户硬件自动推荐：

| 模型 | 参数量 | 内存要求 | 显存要求 | 适用场景 |
|------|--------|----------|----------|----------|
| deepseek-r1:1.5b | 1.5B | 4GB+ | 2GB+ | 轻量级，快速响应 |
| deepseek-r1:7b | 7B | 8GB+ | 6GB+ | 平衡性能与效果 |
| deepseek-r1:8b | 8B | 16GB+ | 8GB+ | 最佳效果 |

### 3.2 主控制台模块

#### 3.2.1 状态监控
- Ollama 服务状态（运行中/已停止）
- 已安装模型列表及大小
- 系统资源占用（CPU/内存/GPU）
- Chatbox 运行状态

#### 3.2.2 快捷操作
- **打开对话**: 一键启动 Chatbox 开始对话
- **启动/停止服务**: 控制 Ollama 服务
- **模型管理**: 下载/删除模型
- **重启服务**: 重启 Ollama

### 3.3 模型管理模块

#### 3.3.1 功能列表
- 查看已安装模型
- 下载新模型（带进度显示）
- 删除已安装模型
- 模型信息查看（大小、参数量等）

#### 3.3.2 支持的模型
- deepseek-r1 系列
- deepseek-coder 系列（可选）
- 其他 Ollama 支持的模型

### 3.4 设置模块

#### 3.4.1 基础设置
- Ollama 安装路径
- Chatbox 安装路径
- 模型存储路径
- 开机自启动

#### 3.4.2 网络设置
- 代理配置（HTTP/SOCKS5）
- 下载镜像源选择

#### 3.4.3 高级设置
- Ollama 服务端口
- GPU 加速开关
- 内存限制

### 3.5 卸载模块

#### 3.5.1 卸载入口
- **Windows 控制面板**: 标准的"添加或删除程序"
- **开始菜单**: 卸载快捷方式
- **应用内**: 设置 → 卸载程序

#### 3.5.2 卸载选项

| 选项 | 删除内容 | 保留内容 |
|------|----------|----------|
| 仅卸载主程序 | DeepSeek Desktop | Ollama、模型、Chatbox |
| 完全卸载 | 所有组件和数据 | 无 |
| 自定义卸载 | 用户选择 | 用户选择 |

#### 3.5.3 卸载确认
- 显示将删除的内容和占用空间
- 二次确认防止误操作
- 卸载进度显示

---

## 4. 用户界面设计

### 4.1 安装向导界面

```
┌──────────────────────────────────────────────────────┐
│  DeepSeek Desktop 安装向导                      [X]  │
├──────────────────────────────────────────────────────┤
│                                                      │
│  ┌────────────────────────────────────────────────┐  │
│  │  ● 环境检测  ○ 组件安装  ○ 模型下载  ○ 完成   │  │
│  └────────────────────────────────────────────────┘  │
│                                                      │
│  系统环境检测                                        │
│  ┌────────────────────────────────────────────────┐  │
│  │  ✓ Windows 11 64位                             │  │
│  │  ✓ 内存: 32GB (推荐 16GB+)                     │  │
│  │  ✓ 磁盘空间: 256GB 可用                        │  │
│  │  ✓ GPU: NVIDIA RTX 3060 12GB                   │  │
│  │  ○ Ollama: 未安装                              │  │
│  │  ○ Chatbox: 未安装                             │  │
│  └────────────────────────────────────────────────┘  │
│                                                      │
│  推荐模型: deepseek-r1:8b (基于您的硬件配置)        │
│                                                      │
│                              [上一步]  [开始安装]    │
└──────────────────────────────────────────────────────┘
```

### 4.2 安装进度界面

```
┌──────────────────────────────────────────────────────┐
│  DeepSeek Desktop 安装向导                      [X]  │
├──────────────────────────────────────────────────────┤
│                                                      │
│  ┌────────────────────────────────────────────────┐  │
│  │  ✓ 环境检测  ● 组件安装  ○ 模型下载  ○ 完成   │  │
│  └────────────────────────────────────────────────┘  │
│                                                      │
│  正在安装组件...                                     │
│                                                      │
│  ┌────────────────────────────────────────────────┐  │
│  │  ✓ 下载 Ollama                    完成         │  │
│  │  ✓ 安装 Ollama                    完成         │  │
│  │  ● 下载 deepseek-r1:8b            45%          │  │
│  │    ████████████░░░░░░░░░░░░░░░░   2.1GB/4.7GB  │  │
│  │  ○ 下载 Chatbox                   等待中       │  │
│  │  ○ 配置 Chatbox                   等待中       │  │
│  └────────────────────────────────────────────────┘  │
│                                                      │
│  预计剩余时间: 5 分钟                                │
│                                                      │
│                                          [取消安装]  │
└──────────────────────────────────────────────────────┘
```

### 4.3 主控制台界面

```
┌──────────────────────────────────────────────────────┐
│  DeepSeek Desktop                           [─][□][X]│
├──────────────────────────────────────────────────────┤
│                                                      │
│         🤖 DeepSeek 本地 AI 助手                     │
│                                                      │
│    ┌──────────────────────────────────────────┐     │
│    │  🟢 Ollama 服务运行中                     │     │
│    │  📦 已安装模型: deepseek-r1:8b (4.7GB)   │     │
│    │  💻 http://localhost:11434               │     │
│    └──────────────────────────────────────────┘     │
│                                                      │
│    ┌────────────────┐  ┌────────────────┐           │
│    │                │  │                │           │
│    │  💬 打开对话   │  │  📦 模型管理   │           │
│    │                │  │                │           │
│    └────────────────┘  └────────────────┘           │
│                                                      │
│    ┌────────────────┐  ┌────────────────┐           │
│    │                │  │                │           │
│    │  🔄 重启服务   │  │  ⚙️ 设置       │           │
│    │                │  │                │           │
│    └────────────────┘  └────────────────┘           │
│                                                      │
├──────────────────────────────────────────────────────┤
│  CPU: 15%  |  内存: 8.2GB/32GB  |  GPU: 45%         │
└──────────────────────────────────────────────────────┘
```

### 4.4 模型管理界面

```
┌──────────────────────────────────────────────────────┐
│  模型管理                                       [←]  │
├──────────────────────────────────────────────────────┤
│                                                      │
│  已安装模型                                          │
│  ┌────────────────────────────────────────────────┐  │
│  │  deepseek-r1:8b                                │  │
│  │  大小: 4.7GB  |  参数: 8B  |  状态: 可用       │  │
│  │                                    [删除]      │  │
│  └────────────────────────────────────────────────┘  │
│                                                      │
│  可下载模型                                          │
│  ┌────────────────────────────────────────────────┐  │
│  │  deepseek-r1:1.5b                              │  │
│  │  大小: 1.1GB  |  参数: 1.5B  |  轻量快速       │  │
│  │                                    [下载]      │  │
│  ├────────────────────────────────────────────────┤  │
│  │  deepseek-r1:7b                                │  │
│  │  大小: 4.1GB  |  参数: 7B  |  平衡之选         │  │
│  │                                    [下载]      │  │
│  ├────────────────────────────────────────────────┤  │
│  │  deepseek-coder:6.7b                           │  │
│  │  大小: 3.8GB  |  参数: 6.7B  |  代码专用       │  │
│  │                                    [下载]      │  │
│  └────────────────────────────────────────────────┘  │
│                                                      │
└──────────────────────────────────────────────────────┘
```

### 4.5 设置界面

```
┌──────────────────────────────────────────────────────┐
│  设置                                           [←]  │
├──────────────────────────────────────────────────────┤
│                                                      │
│  基础设置                                            │
│  ┌────────────────────────────────────────────────┐  │
│  │  开机自启动 Ollama 服务          [  开关  ]    │  │
│  │  最小化到系统托盘                [  开关  ]    │  │
│  └────────────────────────────────────────────────┘  │
│                                                      │
│  路径设置                                            │
│  ┌────────────────────────────────────────────────┐  │
│  │  Ollama 路径: C:\Users\xxx\AppData\Local\...   │  │
│  │  Chatbox 路径: C:\Program Files\Chatbox        │  │
│  │  模型存储: C:\Users\xxx\.ollama\models         │  │
│  └────────────────────────────────────────────────┘  │
│                                                      │
│  网络设置                                            │
│  ┌────────────────────────────────────────────────┐  │
│  │  HTTP 代理: [                              ]   │  │
│  │  Ollama 端口: [ 11434 ]                        │  │
│  └────────────────────────────────────────────────┘  │
│                                                      │
│  ┌────────────────────────────────────────────────┐  │
│  │  🗑️ 卸载程序                                   │  │
│  └────────────────────────────────────────────────┘  │
│                                                      │
│  版本: 1.0.0                    [检查更新]          │
└──────────────────────────────────────────────────────┘
```

### 4.6 卸载确认界面

```
┌──────────────────────────────────────────────────────┐
│  卸载 DeepSeek Desktop                          [X]  │
├──────────────────────────────────────────────────────┤
│                                                      │
│  ⚠️ 请选择卸载方式                                   │
│                                                      │
│  ┌────────────────────────────────────────────────┐  │
│  │  ○ 仅卸载主程序                                │  │
│  │    保留 Ollama、模型和 Chatbox                 │  │
│  │    释放空间: 50MB                              │  │
│  ├────────────────────────────────────────────────┤  │
│  │  ● 完全卸载                                    │  │
│  │    删除所有组件和模型数据                      │  │
│  │    释放空间: 5.2GB                             │  │
│  ├────────────────────────────────────────────────┤  │
│  │  ○ 自定义卸载                                  │  │
│  │    选择要删除的组件                            │  │
│  └────────────────────────────────────────────────┘  │
│                                                      │
│  ⚠️ 此操作不可恢复，请确认后继续                     │
│                                                      │
│                        [取消]  [确认卸载]            │
└──────────────────────────────────────────────────────┘
```

---

## 5. 安装流程

### 5.1 完整安装流程图

```
用户双击安装包
       │
       ▼
┌─────────────────┐
│   欢迎界面      │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│   许可协议      │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  选择安装路径   │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│   环境检测      │──── 不满足 ──→ 提示升级硬件
└────────┬────────┘
         │ 满足
         ▼
┌─────────────────┐
│  选择模型大小   │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  下载 Ollama    │──── 失败 ──→ 重试/手动安装
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  安装 Ollama    │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  下载模型       │──── 进度显示 (支持断点续传)
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  下载 Chatbox   │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  配置 Chatbox   │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  创建快捷方式   │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  安装完成       │──→ 启动程序
└─────────────────┘
```

### 5.2 卸载流程图

```
用户触发卸载
       │
       ▼
┌─────────────────┐
│  卸载确认界面   │──── 取消 ──→ 返回
└────────┬────────┘
         │ 确认
         ▼
┌─────────────────┐
│  选择卸载方式   │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  停止运行服务   │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  删除选定组件   │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  清理注册表     │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  删除快捷方式   │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  卸载完成       │
└─────────────────┘
```

---

## 6. 项目结构

```
deepseek-desktop/
├── main.go                     # Wails 主入口
├── app.go                      # Go 后端逻辑
├── wails.json                  # Wails 配置
├── go.mod
├── go.sum
│
├── build/                      # 构建配置
│   ├── windows/
│   │   ├── icon.ico
│   │   ├── info.json
│   │   └── installer/
│   │       └── project.nsi     # NSIS 安装脚本
│   └── README.md
│
├── frontend/                   # React 前端
│   ├── package.json
│   ├── tsconfig.json
│   ├── vite.config.ts
│   ├── index.html
│   ├── tailwind.config.js
│   └── src/
│       ├── main.tsx
│       ├── App.tsx
│       ├── components/
│       │   ├── InstallWizard/
│       │   │   ├── index.tsx
│       │   │   ├── Welcome.tsx
│       │   │   ├── EnvCheck.tsx
│       │   │   ├── ModelSelect.tsx
│       │   │   ├── InstallProgress.tsx
│       │   │   └── Complete.tsx
│       │   ├── Dashboard/
│       │   │   ├── index.tsx
│       │   │   ├── StatusCard.tsx
│       │   │   ├── ActionButtons.tsx
│       │   │   └── ResourceMonitor.tsx
│       │   ├── ModelManager/
│       │   │   ├── index.tsx
│       │   │   ├── ModelList.tsx
│       │   │   └── DownloadProgress.tsx
│       │   ├── Settings/
│       │   │   ├── index.tsx
│       │   │   ├── GeneralSettings.tsx
│       │   │   ├── PathSettings.tsx
│       │   │   ├── NetworkSettings.tsx
│       │   │   └── UninstallSection.tsx
│       │   ├── Uninstall/
│       │   │   ├── index.tsx
│       │   │   └── UninstallConfirm.tsx
│       │   └── common/
│       │       ├── Button.tsx
│       │       ├── Progress.tsx
│       │       ├── Card.tsx
│       │       ├── Modal.tsx
│       │       └── Switch.tsx
│       ├── hooks/
│       │   ├── useInstaller.ts
│       │   ├── useOllama.ts
│       │   ├── useSystemInfo.ts
│       │   └── useConfig.ts
│       ├── services/
│       │   └── wailsApi.ts
│       ├── store/
│       │   └── appStore.ts
│       ├── types/
│       │   └── index.ts
│       └── styles/
│           └── globals.css
│
├── internal/                   # Go 内部包
│   ├── installer/
│   │   ├── ollama.go           # Ollama 安装逻辑
│   │   ├── chatbox.go          # Chatbox 安装逻辑
│   │   ├── model.go            # 模型下载逻辑
│   │   └── uninstall.go        # 卸载逻辑
│   ├── system/
│   │   ├── check.go            # 系统检测
│   │   ├── process.go          # 进程管理
│   │   └── resource.go         # 资源监控
│   ├── ollama/
│   │   ├── client.go           # Ollama API 客户端
│   │   └── service.go          # 服务管理
│   └── config/
│       └── config.go           # 配置管理
│
├── resources/                  # 资源文件
│   ├── icon.ico
│   ├── icon.png
│   └── banner.png
│
└── docs/                       # 文档
    ├── PRD.md                  # 产品需求文档
    ├── TECH.md                 # 技术设计文档
    └── CHANGELOG.md            # 更新日志
```

---

## 7. 核心 API 设计

### 7.1 Go 后端 API (暴露给前端)

```go
// ==================== 系统检测 ====================

// CheckSystem 检测系统环境
// 返回: 系统信息（OS版本、内存、磁盘、GPU等）
func (a *App) CheckSystem() SystemInfo

// CheckOllamaInstalled 检测 Ollama 是否已安装
func (a *App) CheckOllamaInstalled() bool

// CheckChatboxInstalled 检测 Chatbox 是否已安装
func (a *App) CheckChatboxInstalled() bool

// GetResourceUsage 获取系统资源使用情况
func (a *App) GetResourceUsage() ResourceInfo

// ==================== 安装相关 ====================

// DownloadOllama 下载 Ollama
// 通过事件推送下载进度
func (a *App) DownloadOllama() error

// InstallOllama 安装 Ollama
func (a *App) InstallOllama() error

// DownloadModel 下载指定模型
// modelName: 模型名称，如 "deepseek-r1:8b"
func (a *App) DownloadModel(modelName string) error

// DownloadChatbox 下载 Chatbox
func (a *App) DownloadChatbox() error

// InstallChatbox 安装 Chatbox
func (a *App) InstallChatbox() error

// ConfigureChatbox 配置 Chatbox 连接本地 Ollama
func (a *App) ConfigureChatbox() error

// ==================== 卸载相关 ====================

// Uninstall 执行卸载
// mode: "app_only" | "complete" | "custom"
// components: 自定义模式下要删除的组件列表
func (a *App) Uninstall(mode string, components []string) error

// GetUninstallInfo 获取卸载信息（各组件占用空间等）
func (a *App) GetUninstallInfo() UninstallInfo

// ==================== 服务管理 ====================

// StartOllama 启动 Ollama 服务
func (a *App) StartOllama() error

// StopOllama 停止 Ollama 服务
func (a *App) StopOllama() error

// RestartOllama 重启 Ollama 服务
func (a *App) RestartOllama() error

// GetOllamaStatus 获取 Ollama 服务状态
func (a *App) GetOllamaStatus() ServiceStatus

// ==================== 模型管理 ====================

// ListModels 列出已安装的模型
func (a *App) ListModels() []ModelInfo

// DeleteModel 删除指定模型
func (a *App) DeleteModel(modelName string) error

// GetAvailableModels 获取可下载的模型列表
func (a *App) GetAvailableModels() []ModelInfo

// ==================== 启动器 ====================

// OpenChatbox 打开 Chatbox
func (a *App) OpenChatbox() error

// OpenOllamaTerminal 打开 Ollama 终端
func (a *App) OpenOllamaTerminal() error

// ==================== 配置管理 ====================

// GetConfig 获取配置
func (a *App) GetConfig() Config

// SaveConfig 保存配置
func (a *App) SaveConfig(config Config) error

// SetAutoStart 设置开机自启动
func (a *App) SetAutoStart(enabled bool) error
```

### 7.2 事件定义 (Go → 前端)

```go
// 下载进度事件
type DownloadProgress struct {
    Component   string  `json:"component"`   // "ollama" | "model" | "chatbox"
    Progress    float64 `json:"progress"`    // 0-100
    Downloaded  int64   `json:"downloaded"`  // 已下载字节
    Total       int64   `json:"total"`       // 总字节
    Speed       string  `json:"speed"`       // 下载速度
    ETA         string  `json:"eta"`         // 预计剩余时间
}

// 安装状态事件
type InstallStatus struct {
    Step    string `json:"step"`    // 当前步骤
    Status  string `json:"status"`  // "pending" | "running" | "done" | "error"
    Message string `json:"message"` // 状态消息
}

// 服务状态变更事件
type ServiceStatusChange struct {
    Service string `json:"service"` // "ollama"
    Status  string `json:"status"`  // "running" | "stopped" | "error"
}
```

### 7.3 数据结构定义

```go
// 系统信息
type SystemInfo struct {
    OS           string `json:"os"`           // 操作系统
    Arch         string `json:"arch"`         // 架构
    Memory       int64  `json:"memory"`       // 内存(字节)
    MemoryGB     string `json:"memoryGB"`     // 内存(GB)
    DiskFree     int64  `json:"diskFree"`     // 可用磁盘(字节)
    DiskFreeGB   string `json:"diskFreeGB"`   // 可用磁盘(GB)
    HasGPU       bool   `json:"hasGPU"`       // 是否有 GPU
    GPUName      string `json:"gpuName"`      // GPU 名称
    GPUMemory    int64  `json:"gpuMemory"`    // GPU 显存
    GPUMemoryGB  string `json:"gpuMemoryGB"`  // GPU 显存(GB)
    MeetsMinReq  bool   `json:"meetsMinReq"`  // 是否满足最低要求
    Recommended  string `json:"recommended"`  // 推荐模型
}

// 资源使用信息
type ResourceInfo struct {
    CPUPercent    float64 `json:"cpuPercent"`
    MemoryUsed    int64   `json:"memoryUsed"`
    MemoryTotal   int64   `json:"memoryTotal"`
    MemoryPercent float64 `json:"memoryPercent"`
    GPUPercent    float64 `json:"gpuPercent"`
    GPUMemoryUsed int64   `json:"gpuMemoryUsed"`
}

// 服务状态
type ServiceStatus struct {
    Running bool   `json:"running"`
    PID     int    `json:"pid"`
    URL     string `json:"url"`
    Error   string `json:"error"`
}

// 模型信息
type ModelInfo struct {
    Name        string `json:"name"`
    Size        int64  `json:"size"`
    SizeStr     string `json:"sizeStr"`
    Parameters  string `json:"parameters"`
    Description string `json:"description"`
    Installed   bool   `json:"installed"`
}

// 卸载信息
type UninstallInfo struct {
    AppSize      int64    `json:"appSize"`
    OllamaSize   int64    `json:"ollamaSize"`
    ModelsSize   int64    `json:"modelsSize"`
    ChatboxSize  int64    `json:"chatboxSize"`
    TotalSize    int64    `json:"totalSize"`
    Components   []string `json:"components"`
}

// 配置
type Config struct {
    OllamaPath     string `json:"ollamaPath"`
    ChatboxPath    string `json:"chatboxPath"`
    ModelsPath     string `json:"modelsPath"`
    AutoStart      bool   `json:"autoStart"`
    MinimizeToTray bool   `json:"minimizeToTray"`
    ProxyURL       string `json:"proxyURL"`
    OllamaPort     int    `json:"ollamaPort"`
}
```

---

## 8. 开发计划

### Phase 1: 项目初始化 (1天)
- [ ] Wails 项目创建
- [ ] 前端框架搭建 (React + TailwindCSS)
- [ ] 基础组件库
- [ ] 路由配置

### Phase 2: 系统检测模块 (1天)
- [ ] Windows 系统信息获取
- [ ] 内存/磁盘检测
- [ ] GPU 检测 (NVIDIA)
- [ ] 已安装组件检测

### Phase 3: 安装向导 (2天)
- [ ] 安装向导 UI
- [ ] Ollama 下载安装
- [ ] 模型下载（带进度）
- [ ] Chatbox 下载安装
- [ ] 自动配置

### Phase 4: 主控制台 (1天)
- [ ] 控制台 UI
- [ ] 服务状态监控
- [ ] 资源监控
- [ ] 快捷操作

### Phase 5: 模型管理 (1天)
- [ ] 模型列表展示
- [ ] 模型下载
- [ ] 模型删除

### Phase 6: 设置与卸载 (1天)
- [ ] 设置界面
- [ ] 配置持久化
- [ ] 卸载功能
- [ ] 开机自启动

### Phase 7: 打包发布 (1天)
- [ ] NSIS 安装脚本
- [ ] 图标资源
- [ ] 测试安装/卸载流程
- [ ] 生成最终安装包

**预计总工期: 8天**

---

## 9. 风险与应对

### 9.1 网络问题
| 风险 | 应对措施 |
|------|----------|
| 国内下载慢 | 提供代理设置、考虑镜像源 |
| 下载中断 | 支持断点续传 |
| 下载失败 | 重试机制、手动安装指引 |

### 9.2 权限问题
| 风险 | 应对措施 |
|------|----------|
| 需要管理员权限 | UAC 提权请求 |
| 杀毒软件拦截 | 提示添加白名单 |

### 9.3 兼容性问题
| 风险 | 应对措施 |
|------|----------|
| 旧版 Windows | 明确最低版本要求 |
| 缺少运行时 | 打包 VC++ 运行时 |

### 9.4 硬件限制
| 风险 | 应对措施 |
|------|----------|
| 内存不足 | 推荐小模型、提示升级 |
| 无 GPU | 支持 CPU 运行（较慢） |

---

## 10. 附录

### 10.1 下载源

| 组件 | 官方下载地址 |
|------|-------------|
| Ollama | https://ollama.com/download/windows |
| Chatbox | https://github.com/Bin-Huang/chatbox/releases |
| DeepSeek 模型 | 通过 Ollama 拉取 |

### 10.2 参考资料

- [Wails 官方文档](https://wails.io/docs/introduction)
- [Ollama 官方文档](https://ollama.com/docs)
- [Chatbox GitHub](https://github.com/Bin-Huang/chatbox)
- [DeepSeek 模型](https://ollama.com/library/deepseek-r1)

---

## 11. 版本历史

| 版本 | 日期 | 变更内容 |
|------|------|----------|
| 1.0.0 | 2024-11-27 | 初始版本，完整功能定义 |
