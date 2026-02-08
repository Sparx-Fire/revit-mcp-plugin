# revit-mcp-plugin

English | [简体中文](README_zh.md)

## Introduction

revit-mcp-plugin is a Revit plugin based on the MCP protocol, enabling AI to interact with Revit.

This project is part of the revit-mcp project (receives messages, loads command sets, operates Revit), and needs to be used in conjunction with [revit-mcp](https://github.com/revit-mcp/revit-mcp) (provides tools to AI) and [revit-mcp-commandset](https://github.com/revit-mcp/revit-mcp-commandset) (specific feature implementations).

## Environment Requirements

- Revit 2019~2026

## Usage Instructions

### Register Plugin

A `.addin` manifest file is included in the project at `revit-mcp-plugin/revit-mcp.addin`. This file tells Revit how to load the plugin. To register the plugin:

1. **Build the project** to produce `revit-mcp-plugin.dll`.

2. **Copy the `.addin` file** (`revit-mcp.addin`) to one of the following Revit add-in directories, where `<version>` is your Revit version year (e.g., `2024`, `2025`, `2026`):

   - **All users:** `C:\ProgramData\Autodesk\Revit\Addins\<version>\`
   - **Current user only:** `%APPDATA%\Autodesk\Revit\Addins\<version>\`

3. **Edit the `<Assembly>` path** inside the copied `.addin` file to point to the full path of your compiled DLL:

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

4. **Restart Revit** for the plugin to be loaded.

### Configure Commands

Add-in Modules -> Revit MCP Plugin -> Settings

This interface is used to configure the commands to be loaded into Revit. Click OpenCommandSetFolder to open the folder storing command sets. A typical command set folder structure looks like this:

```
CommandSetName/
├── 2019/                                # Compatible executable files for different versions
├── 2020/
├── 2021/
├── 2022/
├── 2023/
├── 2024/
├── 2025/
├── 2026/
└── command.json                         # Configuration file
```

Successfully identified commands need to be checked to be loaded and used.

### Enable Service

Add-in -> Revit MCP Plugin -> Revit MCP Switch

Open the service to allow AI to discover your Revit program. Now AI can control your Revit!

> Note: If you modify the configured commands after enabling the service, you may need to restart REVIT for the configuration to take effect. This is related to whether the command has already been registered.

## Custom Commands

You can refer to the [revit-mcp-commandset](https://github.com/revit-mcp/revit-mcp-commandset) project to develop custom commands.

## Project File Organization

```
revit-mcp-plugin/
├── revit-mcp-plugin.csproj                   # C# project file
├── revit-mcp.addin                           # Revit plugin registration manifest
│
├── Configuration/                            # Configuration management related classes
│   ├── CommandConfig.cs                      # Command configuration
│   ├── ConfigurationManager.cs               # Configuration manager
│   ├── DeveloperInfo.cs                      # Developer information
│   ├── FrameworkConfig.cs                    # Framework configuration
│   └── ServiceSettings.cs                    # Service settings
│
├── Core/                                     # Program entry and core functionality
│   ├── Application.cs                        # Application entry point
│   ├── CommandExecutor.cs                    # Command executor
│   ├── CommandManager.cs                     # Command manager
│   ├── ExternalEventManager.cs               # External event manager
│   ├── MCPServiceConnection.cs               # MCP service connection
│   ├── RevitCommandRegistry.cs               # Revit command registration
│   ├── Settings.cs                           # Application settings
│   ├── SocketService.cs                      # Socket service implementation
│   └── Ressources/                           # Embedded resource assets
│       ├── icon-16.png                       # Plugin icon (16x16)
│       ├── icon-32.png                       # Plugin icon (32x32)
│       ├── settings-16.png                   # Settings icon (16x16)
│       └── settings-32.png                   # Settings icon (32x32)
│
├── Properties/                               # Assembly metadata
│   └── AssemblyInfo.cs                       # Assembly information
│
├── UI/                                       # WPF form interfaces
│   ├── CommandSetSettingsPage.xaml            # Command set settings UI layout
│   ├── CommandSetSettingsPage.xaml.cs         # Command set settings code-behind
│   ├── SettingsWindow.xaml                    # Settings window UI layout
│   └── SettingsWindow.xaml.cs                # Settings window code-behind
│
└── Utils/                                    # Utility classes
    ├── Logger.cs                             # Logging utility
    └── PathManager.cs                        # Path management utility
```

### Configuration Directory
Responsible for managing various configuration information for the plugin:

- CommandConfig.cs: Defines command-related configuration
- ConfigurationManager.cs: Manages loading, saving, and accessing configurations
- DeveloperInfo.cs: Stores developer-related information
- FrameworkConfig.cs: Framework-level configuration settings
- ServiceSettings.cs: Service-related settings

### Core Directory
Contains the core functionality and entry point of the plugin:

- Application.cs: Application entry point, responsible for initializing the plugin
- CommandExecutor.cs: Core component responsible for executing Revit commands
- CommandManager.cs: Manages and dispatches various commands in the plugin
- ExternalEventManager.cs: Manages Revit external events
- MCPServiceConnection.cs: MCP service connection
- RevitCommandRegistry.cs: Registers and manages available Revit commands
- Settings.cs: Triggers the display of the settings interface
- SocketService.cs: Implements Socket communication with external clients
- Ressources/: Contains embedded icon assets for the plugin ribbon buttons

### Properties Directory
Contains assembly-level metadata (AssemblyInfo.cs).

### UI Directory
Contains user interface components implemented using the WPF framework:

- CommandSetSettingsPage.xaml/.cs: Settings page for configuring command sets
- SettingsWindow.xaml/.cs: Main settings window

### Utils Directory
Provides various auxiliary tools:

- Logger.cs: Logging tool for debugging and error tracking
- PathManager.cs: Project-related file path management
