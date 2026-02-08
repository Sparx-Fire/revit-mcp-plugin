# revit-mcp-plugin

[English](README.md) | 简体中文

## 简介

revit-mcp-plugin 是一个revit插件，基于 MCP 协议制作，从而使AI可以对 Revit 进行交互。

本项目是revit-mcp项目中的一部分（接收信息，装载功能集，操作revit），还需要配合[revit-mcp](https://github.com/revit-mcp/revit-mcp)（向AI提供tools）以及[revit-mcp-commandset](https://github.com/revit-mcp/revit-mcp-commandset)（具体的功能实现）使用。

## 环境要求

- revit 2019~2026

## 使用方法

### 注册插件

项目中包含一个 `.addin` 清单文件，位于 `revit-mcp-plugin/revit-mcp.addin`。此文件告诉 Revit 如何加载插件。注册插件步骤如下：

1. **编译项目**，生成 `revit-mcp-plugin.dll`。

2. **复制 `.addin` 文件**（`revit-mcp.addin`）到以下 Revit 插件目录之一，其中 `<version>` 为你的 Revit 版本年份（如 `2024`、`2025`、`2026`）：

   - **所有用户：** `C:\ProgramData\Autodesk\Revit\Addins\<version>\`
   - **仅当前用户：** `%APPDATA%\Autodesk\Revit\Addins\<version>\`

3. **修改复制后的 `.addin` 文件中的 `<Assembly>` 路径**，指向编译生成的 DLL 完整路径：

   ```xml
   <?xml version="1.0" encoding="utf-8"?>
   <RevitAddIns>
     <AddIn Type="Application">
       <Name>revit-mcp</Name>
       <Assembly>C:\your\build\output\path\revit-mcp-plugin.dll</Assembly>
       <FullClassName>revit_mcp_plugin.Core.Application</FullClassName>
       <ClientId>090A4C8C-61DC-426D-87DF-E4BAE0F80EC1</ClientId>
       <VendorId>revit-mcp</VendorId>
       <VendorDescription>https://github.com/revit-mcp/revit-mcp-plugin</VendorDescription>
     </AddIn>
   </RevitAddIns>
   ```

4. **重启 Revit** 以加载插件。

### 配置命令

附加模块->Revit MCP Plugin->Settings

这个界面用于配置需要装载到revit中的命令，点击OpenCommandSetFolder打开存放命令集的文件夹，一个典型的命令集文件夹结构是这样的

```
命令集名称/
├── 2019/                                # 兼容不同版本的执行文件
├── 2020/
├── 2021/
├── 2022/
├── 2023/
├── 2024/
├── 2025/
├── 2026/
└── command.json                                # 配置文件
```

成功被识别的命令被勾选后，才会被加载和使用

### 启用服务

附加模块->Revit MCP Plugin->Revit MCP Switch

打开服务，让ai可以发现你的revit程序，现在ai可以操控你的revit了！

> 注意：如果启用服务后，修改了配置的命令，可能需要重启REVIT才能使配置生效，这与是否命令已注册相关

## 自定义命令

可以参考[revit-mcp-commandset](https://github.com/revit-mcp/revit-mcp-commandset)项目，开发自定义命令

## 项目文件组织结构

```
revit-mcp-plugin/
├── revit-mcp-plugin.csproj                   # C# 项目文件
├── revit-mcp.addin                           # Revit 插件注册清单
│
├── Configuration/                            # 配置管理相关类
│   ├── CommandConfig.cs                      # 命令配置
│   ├── ConfigurationManager.cs               # 配置管理器
│   ├── DeveloperInfo.cs                      # 开发者信息
│   ├── FrameworkConfig.cs                    # 框架配置
│   └── ServiceSettings.cs                    # 服务设置
│
├── Core/                                     # 程序入口和核心功能
│   ├── Application.cs                        # 应用程序入口点
│   ├── CommandExecutor.cs                    # 命令执行器
│   ├── CommandManager.cs                     # 命令管理器
│   ├── ExternalEventManager.cs               # 外部事件管理器
│   ├── MCPServiceConnection.cs               # MCP服务连接
│   ├── RevitCommandRegistry.cs               # Revit命令注册
│   ├── Settings.cs                           # 应用程序设置
│   ├── SocketService.cs                      # Socket服务实现
│   └── Ressources/                           # 嵌入式资源文件
│       ├── icon-16.png                       # 插件图标 (16x16)
│       ├── icon-32.png                       # 插件图标 (32x32)
│       ├── settings-16.png                   # 设置图标 (16x16)
│       └── settings-32.png                   # 设置图标 (32x32)
│
├── Properties/                               # 程序集元数据
│   └── AssemblyInfo.cs                       # 程序集信息
│
├── UI/                                       # WPF窗体界面
│   ├── CommandSetSettingsPage.xaml            # 命令集设置界面布局
│   ├── CommandSetSettingsPage.xaml.cs         # 命令集设置代码
│   ├── SettingsWindow.xaml                    # 设置窗口界面布局
│   └── SettingsWindow.xaml.cs                # 设置窗口代码
│
└── Utils/                                    # 工具类
    ├── Logger.cs                             # 日志工具
    └── PathManager.cs                        # 路径管理工具
```

### Configuration 目录
负责管理插件的各种配置信息：

- CommandConfig.cs: 定义命令相关配置
- ConfigurationManager.cs: 管理配置的加载、保存和访问
- DeveloperInfo.cs: 存储开发者相关信息
- FrameworkConfig.cs: 框架级别的配置设置
- ServiceSettings.cs: 服务相关设置

### Core 目录
包含插件的核心功能和入口点：

- Application.cs: 应用程序入口点，负责初始化插件
- CommandExecutor.cs: 负责执行Revit命令的核心组件
- CommandManager.cs: 管理和调度插件中的各种命令
- ExternalEventManager.cs: 管理Revit外部事件
- MCPServiceConnection.cs: MCP服务连接
- RevitCommandRegistry.cs: 注册和管理可用的Revit命令
- Settings.cs: 触发显示设置界面
- SocketService.cs: 实现与外部客户端的Socket通信
- Ressources/: 包含插件功能区按钮的嵌入式图标资源

### Properties 目录
包含程序集级别的元数据（AssemblyInfo.cs）。

### UI 目录
包含插件的用户界面相关组件，使用WPF框架实现：

- CommandSetSettingsPage.xaml/.cs: 命令集配置设置页面
- SettingsWindow.xaml/.cs: 主设置窗口

### Utils 目录
提供各种辅助工具：

- Logger.cs: 日志记录工具，用于调试和错误追踪
- PathManager.cs: 项目相关文件路径管理
