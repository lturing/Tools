# Tools

-----------------------------------

- vim 缩进等配置
```bash
sudo vim /etc/vim/vimrc # 在末尾添加以下:

set tabstop=4 "The width of a hard tabstop measured in "spaces" -- effectively the (maximum) width of an actual tab character.
set shiftwidth=4 " The size of an "indent". It's also measured in spaces, so if your code base indents with tab characters then you want shiftwidth to equal the number of tab characters times tabstop. This is also used by things like the =, > and < commands.
set softtabstop=4 " Setting this to a non-zero value other than tabstop will make the tab key (in insert mode) insert a combination of spaces (and possibly tabs) to simulate tab stops at this width.
set expandtab " Enabling this will make the tab key (in insert mode) insert spaces instead of tab characters. This also affects the behavior of the retab command.
set nu " 显示行号
set autoindent " 自动缩进(自动调整已有的缩进到指定 tab 的空格数？)

For more details on any of these see :help 'optionname' in vim (e.g. :help 'tabstop')
```
[ref](https://stackoverflow.com/questions/1878974/redefine-tab-as-4-spaces)

--------------------------------------------

- ubuntu mount disk
```bash
sudo fdisk -l # 查看硬盘的路径
sudo mount /dev/sda1 /media/disk2  # 将/dev/sda1 挂在到 目录 /media/disk2
```

-------------------------------------

- texlive + sublime text 3 + latexTools(sublime text plugin) + sumatrapdf [ref1  ](https://blog.csdn.net/Galoa/article/details/79587751)[  ref2](https://blog.hcl.moe/archives/1737)
```
 latexTools settings:
Preferences -> Package Settings -> LaTeXTools -> Settings – User
修改texpath的值为的TeXLive安装目录下bin下的win32目录的路径，例如 C:\texlive\bin\win32
distro 的值修改为 texlive
sumtra的值修改为安装的 SumatraPDF 的路径（dirname)
为了bibtex + latex
"builder": 项（默认为"traditional"）改为"script"
在"Builder_Settings"：下对应的系统中（windows系统为windows，MAC为OXS）添加下面代码：
	"script_commands":[
		"pdflatex -synctex=1 -interaction=nonstopmode",
		"bibtex",
		"pdflatex -synctex=1 -interaction=nonstopmode",
		"pdflatex -synctex=1 -interaction=nonstopmode"
	]
按下Ctrl+B后在下拉选项中选择Script builder方式进行编译，这样以后编译就默认使用script方式了。
```

- 给 texlive 安装中文字体(宋体、黑体、仿宋、楷体字体)
```cmd
tlmgr conf texmf OSFONTDIR "文件夹路径" # tlmgr conf texmf OSFONTDIR D:\latex_chinese_fonts\fonts
```
> 目录 D:\latex_chinese_fonts\fonts 下有 AdobeFangsongStd-Regular.otf, AdobeHeitiStd-Regular.otf, AdobeKaitiStd-Regular.otf, AdobeSongStd-Light.otf

[LaTeX如何调用Adobe Fonts下载好的字体？](https://www.zhihu.com/question/309633709/answer/577464468)    
[模板1](https://github.com/raytaylorlin/hust-graduation-thesis-pandoc)      
[模板2](https://github.com/hust-latex/hustthesis)       
[字体](https://github.com/lturing/Tools/tree/master/fonts)        

----------------------------------

- docker
[deepo](https://github.com/ufoym/deepo)
```
sudo usermod -aG docker $USER # add current user to group docker
sudo systemctl restart docker # 当docker ps -a 容器不见后，可以重启docekr； 守护进程重启   sudo systemctl daemon-reload
docker ps -a # list all containers (stoped and running)
docker ps # list all running containers
docker images # list all images
docker pull image_name
docker run -it -d --name gived_a_name image_name # create a new containers 
docker run -it -d --name spurs_gpu --runtime=nvidia ufoym/deepo /bin/bash
docker run -it -d --name spurs-gpu -v /media/disk2/spurs:/media/disk2/spurs --runtime=nvidia ufoym/deepo /bin/bash # docker 与 宿主共享文件夹/media/disk2/spurs
docker exec -it container_id /bin/bash
docker inspect container_name 
docker restart/start/stop/kill container_name or container_id 
docker rm -f 容器id # (docker ps -a)
docker rmi 镜像id # (docker images)
```


--------------------------------

- teamviewer on linux
```
teamviewer help # list all available commands
teamviewer info # show teamviewer id
sudo teamviewer passwd # set password
sudo teamviewer setup # assign device to account
sudo teamviewer --daemon restart # restart deamon
sudo teamviewer --setup console #设置启动方式为控制台启动
```
> If you assign a device to your account, you don't need to set a password. Just follow the setup assistent and your done. The device will then show up in your Computers & Contacts.
An example installation and setup would look like this:
```
$ teamviewer setup
Please enter your e-mail: mail@example.org
Please enter your password: ******
You have successfully added this computer to your Computers & Contacts.
```

----------------------------

- conda 基本命令
```
# https://poweruphosting.com/blog/install-anaconda-python-ubuntu-16-04/
# 进入和退出 conda prompt 环境
conda activate # 会进入 base 环境
conda deactivate # 退出

# conda 列出所有的创建的环境
conda env list
or 
conda inf --env

# 更新 conda
conda update conda

# 更新 包
conda update package_name # conda update numpy 

# 更新 conda base 下的python 版本
conda install python=3.5

# 卸载
conda install anaconda-clean

# 新建一个虚拟环境
conda create --name python python=3 # 默认版本
or
conda create -n python python=3
conda create --name python python=3.6 # 指定版本

# 删除某个环境
conda remove --name python --all
or 
conda remove -n python --all

```

--------------------------

- conda 更新源

```
# reference https://blog.csdn.net/sxf1061926959/article/details/54091748 
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
conda config --set show_channel_urls yes
```

----------------------

- pip 更换源
```
# reference https://blog.csdn.net/sxf1061926959/article/details/54091748 
Linux下： 
修改 ~/.pip/pip.conf (没有就创建一个)， 修改 index-url至tuna，内容如下：
[global]
index-url = https://pypi.tuna.tsinghua.edu.cn/simple

windows下: 
直接在user目录中创建一个pip目录，如：C:\Users\xxxx\pip，新建文件pip.ini，内容如下
[global]
index-url = https://pypi.tuna.tsinghua.edu.cn/simple
```

----------------------------

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

-------------------------------

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

# 在 Tools -> Build system -> New build Sytem:("env": ... can solve gbk error )
{
	"cmd": ["D:\\Miniconda3\\python.exe", "-u", "$file"],
	"path": "C:\\Program Files\\NVIDIA GPU Computing Toolkit\\CUDA\\v9.0\\bin;C:\\Program Files\\NVIDIA GPU Computing Toolkit\\CUDA\\v9.0\\libnvvp", 
	"file_regex": "^[ ]*File \"(...*?)\", line ([0-9]*)",
	"selector": "source.python",
	//"encoding": "cp936",
	"env": { "PYTHONIOENCODING": "utf8" },
}

并保存为 Anaconda.sublime-build

```

----------------------


- ngrok 配置步骤
```
启动 ngrokd
/opt/ngrok/bin/ngrokd -tlsKey=server.key -tlsCrt=server.crt -domain="tlcloud.top" -httpAddr=":8081" -httpsAddr=":8082" -tunnelAddr=":80"

```

------------------------------------

- ffmpeg 
```
ffmpeg -i input.mp4 -i subtitle.srt -map 0 -map 1 -c copy -c:v libx264 -crf 23 -preset veryfast output.mp4
```
