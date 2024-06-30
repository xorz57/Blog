+++
title = "Setting up Visual Studio Code for C++ Development"
date = 2024-04-01
draft = false

[taxonomies]
categories = [ "programming" ]
tags = [ "cpp", "vscode" ]

[extra]
lang = "en"
toc = true
comment = true
copy = true
math = false
mermaid = false
outdate_alert = false
outdate_alert_days = 120
display_tags = true
truncate_summary = false
featured = false
+++

![Screenshot](https://github.com/xorz57/Blog/assets/84932056/bbad6fe9-1e10-4b35-87fc-f3bb9819db85)

## Fonts

- [Fira Code](https://github.com/tonsky/FiraCode)
- [Iosevka](https://github.com/be5invis/Iosevka)
- [JetBrains Mono](https://www.jetbrains.com/lp/mono/)

## Extensions

- [C/C++ Extension Pack](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools-extension-pack)
- [Gruvbox Concoctis](https://marketplace.visualstudio.com/items?itemName=wheredoesyourmindgo.gruvbox-concoctis)
- [Material Product Icons](https://marketplace.visualstudio.com/items?itemName=PKief.material-product-icons)

## Package Manager

1. Open `Powershell`

2. Copy & Paste the following

```ps1
git clone https://github.com/microsoft/vcpkg.git C:/vcpkg
C:/vcpkg/bootstrap-vcpkg.bat
C:/vcpkg/vcpkg.exe integrate install
```

## User Settings (JSON)

1. Press `Ctrl` + `Shift` + `P`

2. Type `User Settings (JSON)`

3. Press `Enter`

4. Copy & Paste the following

```json
{
    "editor.fontFamily": "Iosevka, Consolas, 'Courier New', monospace",
    "editor.fontLigatures": true,
    "editor.guides.bracketPairs": "active",
    "editor.mouseWheelZoom": true,
    "editor.renderWhitespace": "all",
    "explorer.sortOrder": "type",
    "git.autofetch": true,
    "workbench.editor.enablePreview": false,
    "workbench.colorTheme": "gruvboxConcoctis dark hard",
    "workbench.productIconTheme": "material-product-icons",
    "cmake.configureArgs": [
        "-DCMAKE_TOOLCHAIN_FILE=C:/vcpkg/scripts/buildsystems/vcpkg.cmake",
        "--profiling-format=google-trace",
        "--profiling-output=cmake.json"
    ],
    "cmake.configureOnOpen": true,
    "cmake.generator": "Ninja",
    "cmake.pinnedCommands": [
        "workbench.action.tasks.configureTaskRunner",
        "workbench.action.tasks.runTask"
    ],
    "cmake.options.statusBarVisibility": "visible",
    "cmake.showOptionsMovedNotification": false
}
```

5. Press `Ctrl` + `S`
