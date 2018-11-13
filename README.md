# Tools
- sublime text3 添加 python 交互式输入
```
Ctrl+Shift+P 打开 package install，输入 SublimeREPL 并安装
在Tools -> Browse Packages -> SublimeREPL -> config -> Main.sublime-menu.template修改 cmd下的python的路径(带python.exe)

在 Tools -> Packages settings -> User 下修改如下：
{
"default_extend_env": {"PATH":"D:\\Python\\python3\\python3.exe"},
}

安装Chain of command，并在 Tools -> Key bindings 下添加:
[
	{
        "keys":["f5"],
        "caption": "SublimeREPL: Python - RUN current file",
        "command": "chain", 
        "args":
        {   "commands":[
                ["save"],
                ["run_existing_window_command", {"id": "repl_python_run", "file": "config/Python/Main.sublime-menu"}]
            ]
        },
    },

]

```

- sublime text3 conda 环境配置
```
# reference http://damnwidget.github.io/anaconda/
# Ctrl + Shift + P, 并输入 package install 
# 输入 anaconda，点击安装
# 在 Preferences -> Package setting -> Anaconda -> setting -default 下：
"python_interpreter": "D:\\Miniconda3\\python.exe",

# setting -User 下:
{
    "python_interpreter": "D:\\Miniconda3\\python.exe",
    "suppress_word_completions": true,
    "suppress_explicit_completions": true,
    "complete_parameters": true,
    "swallow_startup_errors": true,
    "anaconda_linting": false,
}

# 在 Tools -> Build system -> New build Sytem:
{
	"cmd": ["python3.exe", "-u", "$file"],
	"path": "D:\\Miniconda3\\python.exe;C:\\Program Files\\NVIDIA GPU Computing Toolkit\\CUDA\\v9.0\\bin;C:\\Program Files\\NVIDIA GPU Computing Toolkit\\CUDA\\v9.0\\libnvvp", 
	"file_regex": "^[ ]*File \"(...*?)\", line ([0-9]*)",
	"selector": "source.python",
	"encoding": "cp936"
}

并保存为 Anaconda.sublime-build

```

- conda 更新源

```
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
conda config --set show_channel_urls yes
```


- ngrok 配置步骤
```
启动 ngrokd
/opt/ngrok/bin/ngrokd -tlsKey=server.key -tlsCrt=server.crt -domain="tlcloud.top" -httpAddr=":8081" -httpsAddr=":8082" -tunnelAddr=":80"

```

