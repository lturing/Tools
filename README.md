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

- ngrok 配置步骤
```
启动 ngrokd
/opt/ngrok/bin/ngrokd -tlsKey=server.key -tlsCrt=server.crt -domain="tlcloud.top" -httpAddr=":8081" -httpsAddr=":8082" -tunnelAddr=":80"

```
