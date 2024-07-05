# VS Code 配置文件

```json title="setting.json"
{
    "terminal.integrated.defaultProfile.osx": "zsh",
    "editor.fontSize": 13,
    "files.autoSave": "afterDelay",
    "files.autoGuessEncoding": true,
    "editor.minimap.enabled": false,
    "editor.maxTokenizationLineLength": 100000,
    "workbench.iconTheme": "vscode-icons",
    "workbench.editor.enablePreview": false,
    "[python]": {
        "editor.formatOnType": true,
        "editor.formatOnSave": true,
        "editor.defaultFormatter": "ms-python.black-formatter",
        "editor.codeActionsOnSave": {
            "source.convertImportFormat": "explicit"
        }
    },
    "isort.args": [
        "--profile",
        "black"
    ],
    "terminal.integrated.shell.osx": "/bin/zsh",
    "terminal.integrated.shellArgs.osx": [],
    "terminal.integrated.profiles.osx": {
        "bash": {
            "path": "bash",
            "args": [
                "-l"
            ],
            "icon": "terminal-bash"
        },
        "zsh": {
            "path": "zsh",
            "args": [
                "-l"
            ]
        },
        "fish": {
            "path": "fish",
            "args": [
                "-l"
            ]
        },
        "tmux": {
            "path": "tmux",
            "icon": "terminal-tmux"
        },
        "pwsh": {
            "path": "pwsh",
            "icon": "terminal-powershell"
        }
    }
}
```