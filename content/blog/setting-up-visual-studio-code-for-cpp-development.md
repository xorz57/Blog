+++
title = "Setting up Visual Studio Code for C++ Development"
date = 2024-04-01
draft = false

[taxonomies]
categories = ["programming"]
tags = ["cpp", "vscode"]

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

## Fonts

- [Fira Code](https://github.com/tonsky/FiraCode)
- [Iosevka](https://github.com/be5invis/Iosevka)
- [JetBrains Mono](https://www.jetbrains.com/lp/mono/)

## Extensions

- [C/C++ Extension Pack](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools-extension-pack)
- [Monokai Pro](https://marketplace.visualstudio.com/items?itemName=monokai.theme-monokai-pro-vscode)
- [One Dark Pro](https://marketplace.visualstudio.com/items?itemName=zhuangtongfa.Material-theme)

## User Settings (JSON)

1. Press `Ctrl` + `Shift` + `P`
2. Type `User Settings (JSON)`
3. Press `Enter`
4. Replace the contents of `settings.json` with the following

```json
{
    "editor.fontFamily": "'JetBrains Mono', Consolas, 'Courier New', monospace",
    "editor.fontLigatures": true,
    "editor.guides.bracketPairs": "active",
    "editor.mouseWheelZoom": true,
    "editor.renderWhitespace": "all",
    "explorer.sortOrder": "type",
    "git.autofetch": true,
    "workbench.editor.enablePreview": false,
    "workbench.colorTheme": "Monokai Pro (Filter Spectrum)",
    "workbench.iconTheme": "Monokai Pro (Filter Spectrum) Icons",
}
```
