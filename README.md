# Tools

---------------------------

- nginx 分享目录
```
sudo apt install nginx 
cd /etc/nginx/site-availables
# 在 default 文件中添加以下内容，位置在location{}的后面
location /filesha {
          alias /home/pi/fileshare/; # 需要分享的目录地址
	  autoindex on;
}
# 新建需要分享的文件夹
mkdir /home/pi/fileshare/

# 重启nginx
sudo systemctl restart nginx
```

-----------------------------------

- 卸载 zsh的顺序                
1，su root && chsh -s /bin/bash YOUR_USERNAME (查看哪些用户使用zsh，通过 /etc/passwd)                            
2, sudo apt --purge remove zsh zsh-*                                     
3, ~/.oh-my-zsh/tools/uninstall.sh                                             

- 卸载 zsh 后，无法登录解决方法(卸载前没有执行 chsh -s /bin/bash)                         
1，sudo vim /etc/passwd ,将/bin/zsh 改为 /bin/bash                                        


-------------------------------------------

- pip
	- ModuleNotFoundError: No module named 'pip'
```
python -m ensurepip
python -m pip install update pip
```

---------------------------

- 视频下载工具
- aria2c
- youtube-dl

>youtube-dl ${url} --external-downloader aria2c --external-downloader-args "-x 16 -s 16 -k 1M" # 多线程下载         
对于B站的视频，直接copy所播放视频的页面地址(不是右键出来的地址，而是地址的栏的网址)


-----------------------------

- vim
```
enable mouse clip in vim environment
create ~/.vimrc and add the following line 
set mouse=r
source ./vimrc
```

----------------------------

- zsh
```
cat /etc/shells 
sudo apt install zsh
sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"

可能需要运行
chsh -s /bin/zsh # or
chsh -s $(which zsh) # or 
sudo usermod -s /bin/zsh spurs # spurs is current user

命令
d # 快速回到历史目录

修改默认主题
vim ~/.zhrc
ZSH_THEME="robbyrussel"

```


-----------------------------

- matplotlib 画图支持中文

```
1, 在win10下 C:\Windows\Fonts 下 搜索 yahei(在本人的电脑下有：Microsoft YaHei UI 常规、Microsoft YaHei UI 粗体、Microsoft YaHei UI 细体)，本人选了常规。
2, 由于上一步中的格式ttc，matplotlib字体的格式是ttf，故到 https://transfonter.org/ttc-unpack 转成ttf，结果会有(MicrosoftYaHei-01.ttf、MicrosoftYaHeiUI-02.ttf)
3，调用以下命令查看字体目录
	python3 -c "import matplotlib;print(matplotlib.matplotlib_fname())" 
	# 输出结果例如：
		/usr/local/lib/python3.5/dist-packages/matplotlib/mpl-data/matplotlibrc 
4, 执行以下命令
	cd /usr/local/lib/python3.5/dist-packages/matplotlib/mpl-data/ && cd ./fonts/ttf
	# 将 第2步中的转换后的结果复制到这个目录下
5，删除缓存(如果启动了jupyter，需要先关闭)
	rm -rf ~/.cache/matplotlib
6, 可能会遇到以下问题
   Font family ['sans-serif'] not found. Falling back to DejaVu Sans (prop.get_family(), self.defaultFamily[fontext]))
   运行以下命令：
   sudo apt-get install msttcorefonts -qq

```

测试
```python
import matplotlib as mpl
mpl.rcParams['font.family'] = 'Microsoft YaHei'
mpl.rcParams['font.sans-serif'] = ['Microsoft YaHei']
mpl.rcParams['font.size'] = 20
import matplotlib.pyplot as plt
a = [i for i in range(100)]
plt.plot(a)
plt.legend(['中文测试'])
plt.xlabel('索引值')
plt.ylabel('值')
plt.tight_layout() #自动调整布局
#plt.figsave('test.pdf', bbox_inches='tight') # 自动调整布局
plt.show()
```


------------------------

- 树莓派无显示器，无路由器安装系统
```
1，用balenaEther 将 raspberry lite 导入 sd卡中
2，在 sd 下 cmdline.txt文件，在其中写入ip=192.168.137.9 
3，修改 window下的以太网的ip4 的地址为 192.168.137.1 子网掩码255.255.255.0
4, 在cmd中输入 arp -a 验证 192.168.137.1 下是否有 192.168.137.9
5，在sd卡根目录下创建 ssh文件 #默认下rapsberry ssh没有开启，通过向sd卡加入ssh文件开启
```
-----------------------------------

- 树莓派网线认证上网(hust)
```
sudo apt-get install gcc libc6-dev m4 flex bison
wget http://www.tcpdump.org/release/libpcap-1.0.0.tar.gz
tar zxvf libpcap-1.0.0.tar.gz
cd libpcap-1.0.0/
./configure 
make 

cd 

wget https://storage.googleapis.com/google-code-archive-downloads/v2/code.google.com/mentohust/mentohust-0.3.1.tar.gz
sudo apt-get install build-essential bison flex zlib1g-dev libncurses5-dev subversion quilt intltool ruby fastjar unzip gawk 
tar zxvf mentohust-0.3.1.tar.gz
cd mentohust-0.3.1 
./configure --disable-encodepass --disable-arp --disable-notify --disable-nls --with-pcap=/home/pi/libpcap-1.0.0/libpcap.a
make 


# start
sudo /home/pi/mentohust-0.3.1/src/mentohust -uM201721213 -p111234 -neth0 -o0.0.0.0 -t8 -e30 -r15 -a0 -d1 -b2 -v4.10 -cdhclient


# stop
sudo /home/pi/mentohust-0.3.1/src/mentohust -k


# help
# /home/pi/mentohust-0.3.1/src/mentohust --help
```

-----------------------------------

- 树莓派软件源   
[http://www.raspbian.org/RaspbianMirrors](http://www.raspbian.org/RaspbianMirrors)
```
修改 
/etc/apt/sources.list.d/raspi.list  # 系统源
和 
/etc/apt/sources.list   # 软件源
对应的地址

sudo apt update 
sudo apt upgrade 
or
# sudo apt update && upgrade
```

- 树莓派命令
```
# https://picamera.readthedocs.io/en/release-1.13/recipes1.html
sudo raspi-config # 配置
raspistill -o test.jpg # 拍照
/etc/rc.local # 开机自启( 自启的命令放在 exit 0 之前）
```
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

update 
set tabstop=4
set shiftwidth=4
set softtabstop=4
set expandtab
set autoindent


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

ubuntu 全局
/etc/pip.conf
[global]
index-url = https://pypi.tuna.tsinghua.edu.cn/simple
[install]
trusted-host=mirrors.aliyun.com

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
ffmpeg -i D:\\movies\\Birds.mkv -filter_complex "[0:4]pan=1c|c0=FC" D:\\movies\\Birds_ch.wav # 提取5.1声道中的fc(front center)声道
ffmpeg -i Birds.mkv -map 0:3 -map_channel 0:3:2 Birds_ch.wav #等价于上面的命令
ffmpeg -i input.mp4 -vf subtitles=sample.srt out.mp4
ffmpeg -i demo.mp4 -vf ass=subtitle.ass output.mp4 
```

- 合并视频
```
1, 首先将需要合并的文件按照一定的格式写入到文件，例如新建文件 file.txt，并写入以下内容             
file '1540353_part1.flv'      
file '1540353_part2.flv'          
file '1540353_part3.flv'                      
...                          
                
2，合并命令                          
ffmpeg -f concat -safe 0 -i file.txt -c copy output.mp4                       
```

----------------------------------------------------------
