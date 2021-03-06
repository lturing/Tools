# Tools

- **[github 代理](https://hub.fastgit.org/)**

---------------------------

- **[Audio Volume Manipulation](https://trac.ffmpeg.org/wiki/AudioVolume)** [issue](https://github.com/mozilla/common-voice/issues/336)

---------------------------

- maven helloworld
```
mvn archetype:generate
```

-------------------

- ubuntu 18关闭终端的bell音
```
编辑 /etc/inputrc，找到#set bell-style none这一行，去掉前面的注释符号
```
--------------

- alternatives mobaxterm for ubuntu
```
sudo apt install sshfs
mkdir ~/sshfs
sshfs username@host:/home/username $HOME/sshfs
#host:/home/username会mount在$HOME/sshfs

# fix transport endpoint is not connected
fusermount -u $mountpoint
```

------------------------------

- open website on server through ssh 
```
tensorboard --logdir='path_to_checkpoint_log' --p 6006 # on server 
ssh -L 16006:127.0.0.1:6006 shengleyuan@lab.yiwise.com -p 60005 # on client 
open http://127.0.0.1:16006 with chrome on client 
```

----------

- vim 编辑python后直接运行代码
```
:w !python
```
--------------------

- vim自动回到上次编辑的位置(.vimrc)
```
if has("autocmd")
  au BufReadPost * if line("'\"") > 1 && line("'\"") <= line("$") | exe "normal! g'\"" | endif
endif
```
-------------------------

- [arxiv](https://arxiv.org/)[中科大镜像](http://xxx.itp.ac.cn)[ref](https://blog.csdn.net/seasermy/article/details/95176357)
```
中科大镜像: http://xxx.itp.ac.cn
例如要访问的论文arxiv地址为: https://arxiv.org/pdf/1810.07217.pdf
修改为中科大镜像地址为: http://xxx.itp.ac.cn/pdf/1810.07217.pdf
大大加快arxiv上的论文的加载速度
```

-------------------------
- 树莓派cmdline.txt 备份

```
ip=192.168.137.6 dwc_otg.lpm_enable=0 console=serial0,115200 console=tty1 root=PARTUUID=dfe43a0d-02 rootfstype=ext4 elevator=deadline fsck.repair=yes rootwait
```

* /boot/cmdline.txt

------------------------------

- 解决ubuntu 18 蓝牙无法连接(已配对)
```
sudo apt-get install pulseaudio-module-bluetooth
pactl load-module module-bluetooth-discover
```

------------------

- 解决ubuntu 18 没有声音
```
#alsamixer
sudo apt-get remove alsa-base pulseaudio
sudo apt-get install alsa-base pulseaudio
sudo alsa force-reload
sudo reboot now
#alsamixer
```
------------------------------

- 解决ubuntu Terminal 中文乱码(????????????)
```
sudo locale-gen zh_CN.UTF-8
# sudo dpkg-reconfigure locales
# sudo reboot now
```

-----------------------------------------------

- intellij `go to` (intellij cannot find declaration to go to)
* Right-click src folder
* Mark Directory as > Sources Root

-----------------------

- Linux 常用的命令
```
sudo apt remove -y purge 软件名称 # 
watch -n 1 "ifconfig eth0" #
mvn dependency:tree -Dverbose -Dincludes=${plugin_name} #mvn dependency:tree -Dverbose -Dincludes=log4j #查看指定包的版本冲突问题
sudo dmidecode -t memory #ubuntu查看内存信息
```

---------------------------

- ubuntu show all dir(root dir) in gui
```
sudo nautilus
```

---------------------------------------

- 将视频转化为图片
```
ffmpeg -i demo.mp4 -vsync 0 out%4d.png
ffmpeg -i demo.mp4 -vf fps=30,select='between(t,2,6)+between(t,15,24)' -vsync 0 out%4d.png # 将视频中第2秒到第6秒和第15秒到第24秒的视频转化为图片
ffmpeg -i demo.mp4 -vf fps=1/60 out%04d.png 
ffmpeg -i demo.mp4 -vf fps=30 out%04d.png
```


----------------------

- sublime text 3添加桌面快捷(add to my favorite/dock)
```
# 从sublime text官网下载linux版的sublime text
tar -xvf sublime_text_3_build_3211_x64.tar.bz2
cd sublime_text_3
cp sublime_text.desktop ~/.local/share/applications # for 当前用户
# cp sublime_text.desktop /usr/share/applications # for all users
chmod a+x sublime_text.desktop # vim sublime.desktop 检查exec的路径是否正确
```
> desktop文件中的Icon如果是png格式的，需要将图片复制到/usr/share/icons目录下

-----------------------------------------------------------

- 动态查看gpu的使用效率
```
watch -n 0.1 -d nvidia-smi
```

--------------------

- shell command
```
for file in `hdfs --cluster zjyprc-hadoop dfs -ls -R mazhen1/recall_by_category_6 | awk '$2!="-" && $5!=0 {print $8}'`; do hdfs --cluster zjyprc-hadoop dfs -cat $file | hdfs --cluster zjyprc-hadoop dfs -appendToFile - recall_category_7;done
```

-------------------------------

- conda 创建虚拟环境并安装tensorflow-gpu以及cuda，cudnn等
```
conda create --name tf-py36 python=3.6.5 tensorflow-gpu=1.14.0 # tf_gpu 为创建的虚拟环境的名称
```
------------------------------

- linux 查看当前文件夹大小
du -sh 

---------------------------

- mv: cannot stat No such file or directory in shell script

```
mv ${cwd}/${folder}'/data/*' ${cwd}/${folder}
The quotes (') there prevent the shell from doing globbing. The * is being passed literally to the mv command, which fails since it doesn't find files called * in the directories indicated.

Change this to:

mv "${cwd}/${folder}/data"/* "${cwd}/${folder}"
(Double quotes to prevent problems if you ever have a directory name with spaces in it. * outside the quotes.)

You'll still get the errors for the empty directories though. (Same sort of reason: if the file doesn't find a match for the pattern, it passes the pattern itself as an argument to the command.)
```
-----------------------------

- relay 无密码登录
adding the following lines to ~/.ssh/config
```
Host *
     ControlPersist yes
     ControlMaster auto
     ControlPath ~/.ssh/master-%r@%h:%p
     Compression yes

```
adding the following lines to ~/.bashrc
alias relay='ssh yourname@domain.com'

--------------------------------------

- shell 命令
```
grep -rn "data.csv" `find . -name '*.py'` #在当前文件夹下递归地查找以".py"为后缀的文件，并打印这些匹配出的文件中包含"data.csv"的行
```

-------------------------------------

- sublime text 3 in linux "Package Control: Error downloading package. HTTP exception InvalidCertificateException"
```
adding the following to Preferences > Package Settings > Package Control > Settings - User:
(urllib and wget, wasn't getting it done, so leave them out)

"downloader_precedence":
	{
		"linux":
		[
			"curl"
		]
	},
```

- sublime text 3 插件
	* A File Icon # 美化icon
	* ayu # theme

-------------------------------------

- git 命令
## 库管理

### 删除分支
```bash
git branch -a # 查看所有的分支(远程)(git branch 查看本地分支)
  develop
* master
  remotes/origin/feature_dependency_version
  remotes/origin/feature_ncn

git push origin :feature_ncn # 删除远程分支 feature_ncn，推送一个空的分支到远程feature_ncn,相当于删除远程分支feature_ncn
or 
git push origin --delete feature_ncn 

git branch -d branch_name # 删除本地分支
```

### 克隆库

```bash
git clone https://github.com/php/php-src.git
git clone --depth=1 https://github.com/php/php-src.git # 只抓取最近的一次 commit
```

## 历史管理

### 查看历史

```bash
git log --pretty=oneline filename # 一行显示
git show xxxx # 查看某次修改
```

### 标签功能

```bash
git tag # 显示所有标签
git tag -l 'v1.4.2.*' # 显示 1.4.2 开头标签
git tag v1.3 # 简单打标签
git tag -a v1.2 9fceb02 # 后期加注标签
git tag -a v1.4 -m 'my version 1.4' # 增加标签并注释， -a 为 annotated 缩写
git show v1.4 # 看某一标签详情
git push origin v1.5 # 分享某个标签
git push origin --tags # 分享所有标签
```

### 回滚操作

```bash
git reset 9fceb02 # 保留修改
git reset 9fceb02 --hard # 删除之后的修改
```

### 取消文件的修改

```bash
git checkout -- a.php #  取消单个文件
git checkout -- # 取消所有文件的修改
```

### 删除文件

```bash
git rm a.php  # 直接删除文件
git rm --cached a.php # 删除文件暂存状态
```

### 移动文件

```bash
git mv a.php ./test/a.php
```

### 查看文件修改

```bash
git diff          # 查看未暂存的文件更新
git diff --cached # 查看已暂存文件的更新
```

### 暂存和恢复当前staging

```bash
git stash # 暂存当前分支的修改
git stash apply # 恢复最近一次暂存
git stash list # 查看暂存内容
git stash apply stash@{2} # 指定恢复某次暂存内容
git stash drop stash@{0} # 删除某次暂存内容
```

### 修改 commit 历史纪录

```bash
git rebase -i 0580eab8
```

## 分支管理

### 创建分支

```bash
git branch develop # 只创建分支
git checkout -b master develop # 创建并切换到 develop 分支
```

### 合并分支

```bash
git checkout master # 切换到 master 分支
git merge --no-ff develop # 把 develop 合并到 master 分支，no-ff 选项的作用是保留原分支记录
git rebase develop # rebase 当前分支到 develop
git branch -d develop # 删除 develop 分支
git rebase origin master # 和远程 master 分支 rebase
```

### 克隆远程分支

```bash
git branch -r # 显示所有分支，包含远程分支
git checkout origin/android
```

### 修复develop上的合并错误

1. 将merge前的commit创建一个分之，保留merge后代码
2. 将develop `reset --force`到merge前，然后`push --force`
3. 在分支中rebase develop
4. 将分支push到服务器上重新merge

### 强制更新到远程分支最新版本

```bash
git reset --hard origin/master
git submodule update --remote -f
```

### 删除已经 merge 的分支

```bash
git branch --merged | egrep -v "(^\*|master|dev)" | xargs git branch -d
```

## Submodule使用

### 克隆带submodule的库

```bash
git clone --recursive https://github.com/chaconinc/MainProject
```

### clone主库后再去clone submodule

```bash
git clone https://github.com/chaconinc/MainProject
git submodule init
git submodule update
```

## Git设置

Git的全局设置在`~/.gitconfig`中，单独设置在`project/.git/config`下。

忽略设置全局在`~/.gitignore_global`中，单独设置在`project/.gitignore`下。

### 设置 commit 的用户和邮箱

```bash
git config user.name "xx"
git config user.email "xx@xx.com"
```

或者直接修改config文件

```bash
[user]
    name = xxx
    email = xxx@xxx.com
```

### 查看设置项

```bash
git config --list
```

### 设置git终端颜色

```bash
git config --global color.diff auto
git config --global color.status auto
git config --global color.branch auto
```

-------------------------------------------------

- vim 相关的命令
```
粘贴之前输入 
:set paste 

粘贴完后恢复 
:set nopaste

关闭和开启行号
关闭 
:set nonu 

开启 
:set number


在.vimrc中添加以下代码后，重启vim即可实现按TAB产生4个空格：
set ts=4  (注：ts是tabstop的缩写，设TAB宽4个空格)
set expandtab

------

set nu
set ts=4
set expandtab
set mouse=r
set hlsearch



对于已保存的文件，可以使用下面的方法进行空格和TAB的替换：
TAB替换为空格：
:set ts=4
:set expandtab
:%retab!

空格替换为TAB：
:set ts=4
:set noexpandtab
:%retab!

```

-----------------------

- git 命令
```
- 查看有哪些分支
	- git branch -a 
- 切换分支 
	- git checkout -b demo origin/demo # git branch -a 中的输出中有remotes/origin/demo

```

--------------------------------------

- change command ls result color (directory, file, etc)

```bash
#add the following color to ~/.bashrc
LS_COLORS=$LS_COLORS:'tw=01;35:ow=01;35:'

# then
source ~/.bashrc
```

* more details
```
To change your directory colors, open up your ~/.bashrc file with your editor

nano ~/.bashrc
and make the following entry at the end of the file:

LS_COLORS=$LS_COLORS:'di=0;35:' ; export LS_COLORS
Some nice color choices (in this case 0;35 it is purple) are:

Blue = 34
Green = 32
Light Green = 1;32
Cyan = 36
Red = 31
Purple = 35
Brown = 33
Yellow = 1;33
Bold White = 1;37
Light Grey = 0;37
Black = 30
Dark Grey= 1;30
The first number is the style (1=bold), followed by a semicolon, and then the actual number of the color, possible styles (effects) are:

0   = default colour
1   = bold
4   = underlined
5   = flashing text (disabled on some terminals)
7   = reverse field (exchange foreground and background color)
8   = concealed (invisible)
The possible backgrounds:

40  = black background
41  = red background
42  = green background
43  = orange background
44  = blue background
45  = purple background
46  = cyan background
47  = grey background
100 = dark grey background
101 = light red background
102 = light green background
103 = yellow background
104 = light blue background
105 = light purple background
106 = turquoise background
107 = white background
All possible colors:

31  = red
32  = green
33  = orange
34  = blue
35  = purple
36  = cyan
37  = grey
90  = dark grey
91  = light red
92  = light green
93  = yellow
94  = light blue
95  = light purple
96  = turquoise
97  = white
These can even be combined, so that a parameter like:

di=1;4;31;42
in your LS_COLORS variable would make directories appear in bold underlined red text with a green background!

To test all these colors and styles in your terminal, you can use one of:

for i in 00{2..8} {0{3,4,9},10}{0..7}
do echo -e "$i \e[0;${i}mSubdermatoglyphic text\e[00m  \e[1;${i}mSubdermatoglyphic text\e[00m"
done

for i in 00{2..8} {0{3,4,9},10}{0..7}
do for j in 0 1
   do echo -e "$j;$i \e[$j;${i}mSubdermatoglyphic text\e[00m"
   done
done
You can also change other kinds of files when using the ls command by defining each kind with:

bd = (BLOCK, BLK)   Block device (buffered) special file
cd = (CHAR, CHR)    Character device (unbuffered) special file
di = (DIR)  Directory
do = (DOOR) [Door][1]
ex = (EXEC) Executable file (ie. has 'x' set in permissions)
fi = (FILE) Normal file
ln = (SYMLINK, LINK, LNK)   Symbolic link. If you set this to ‘target’ instead of a numerical value, the color is as for the file pointed to.
mi = (MISSING)  Non-existent file pointed to by a symbolic link (visible when you type ls -l)
no = (NORMAL, NORM) Normal (non-filename) text. Global default, although everything should be something
or = (ORPHAN)   Symbolic link pointing to an orphaned non-existent file
ow = (OTHER_WRITABLE)   Directory that is other-writable (o+w) and not sticky
pi = (FIFO, PIPE)   Named pipe (fifo file)
sg = (SETGID)   File that is setgid (g+s)
so = (SOCK) Socket file
st = (STICKY)   Directory with the sticky bit set (+t) and not other-writable
su = (SETUID)   File that is setuid (u+s)
tw = (STICKY_OTHER_WRITABLE)    Directory that is sticky and other-writable (+t,o+w)
*.extension =   Every file using this extension e.g. *.rpm = files with the ending .rpm
A more complete list is available at Bigsoft - Configuring LS_COLORS.

On some distributions, you might also want to change the background color for ow "(OTHER_WRITABLE) whose default is non-readable" for example to non-bold blue text on green background.

You could use for instance LS_COLORS="$LS_COLORS:di=1;33" at the end of your .bashrc file, to get a nice readable bold orange text on black background.

After you alter your .bashrc file, to put the changes in effect you will have to restart your shell or run source ~/.bashrc.

Note: You can combine more commands with a colon, for example

LS_COLORS=$LS_COLORS:'di=1;33:ln=36' ; export LS_COLORS; ls
Source:

COLORS Lscolors - Linux StepByStep
Geek Gumbo - Changing the Directory Color in Bash
```

[ref](https://askubuntu.com/questions/466198/how-do-i-change-the-color-for-directories-with-ls-in-the-console)

-----------------------

- linux cmd
```bash
- restart wlan0
sudo ip link set wlan0 down
sudo ip link set wlan0 up
or
sudo ifup wlan0
sudo ifdown wlan0
or 
sudo ifconfig wlan0 up
sudo ifconfig wlan0 down
```

------------------------

- 更新linux软件源后的问题(包括树莓派)
```
1, The following signatures couldn't be verified because the public key is not available: NO_PUBKEY xxxxxxxx（'公钥编码'）
解决方法：
apt-key adv --keyserver keyserver.ubuntu.com --recv-keys '公钥编码'
```
[ref](https://www.jianshu.com/p/a026d08ce5a2)

-------------------------------

- python ping
```python
import os

exit_code = os.system('ping -c 1 www.baidu.com &>/dev/null')

if exit_code:
    print('fail')
else:
    print('sucess')

```

--------------------------------

- window 下合并视频

copy /b *.ts face_plus.mp4 

----------------------------


- linux 创建用户

```bash
sudo useradd git # 新建用户 git
sudo usermod -aG sudo git # 将新建用户git加入sudo组

The a in ‘-aG’ is very important. Without it they’ll be removed from all other groups. You will need to either restart your shell/terminal or log out and back in for this to take effect. 
```

[ref](https://askubuntu.com/questions/2214/how-do-i-add-a-user-to-the-sudo-group)

--------------------------------------------------------

- jupyter 

```
conda install jupyter
conda install nb_conda
conda install ipykernel

# 生成配置文件，即 ~/.jupyter/jupyter_notebook_config.py
jupyter notebook --generate-config
jupyter notebook --generate-config --allow-config # for root user when run as root 
# 生成密码，即 ~/.jupyter/jupyter_notebook_config.json
jupyter notebook password # 输入密码
# 然后把里面的密码复制，密码的形式：sha1:67c9e60bb8b6:9ffede0825894254b2e042ea597d771089e11aed
# 配置任何主机可访问，修改 ~/.jupyter/jupyter_notebook_config.py(并去掉该行最前面的# ，#代表注释)
c.NotebookApp.ip='0.0.0.0' # 任意主机都可以访问
c.NotebookApp.password = '刚刚复制的密码(本代码部分的第11行)'
c.NotebookApp.open_browser = False
c.NotebookApp.port = 1024 #端口，随便设置，只要不冲突

# theme https://github.com/dunovank/jupyter-themes
pip install jupyterthemes or pip install --upgrade jupyterthemes
# 列出所有的主题
jt -l

# 启用某个主题
jt -t gruvboxl # 护眼色
jt -r 

mkdir notebook && cd notebook && jupyter notebook # 创建 notebook目录，并以此目录为jupyter的文件保存路径，启动jupyter

#交互式
%matplotlib notebook

# 设置jupyter页面为英文
LANGUAGE="" LANG=en_US.UTF-8 LC_ALL=en_US.UTF-8 jupyter notebook

```

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
3，共享无线网络给有线以太网
4，ipconfig 查看以太网的ip
5, 在cmd中输入 arp -a 验证 192.168.137.1 下是否有 192.168.137.9
6，在sd卡根目录下创建 ssh文件 #默认下rapsberry ssh没有开启，通过向sd卡加入ssh文件开启
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


实例(stretch)
1.修改/etc/apt/source.list为以下内容:
#deb http://raspbian.raspberrypi.org/raspbian/ stretch main contrib non-free rpi
deb http://mirrors.ustc.edu.cn/raspbian/raspbian/ stretch main contrib non-free rpi
# Uncomment line below then 'apt-get update' to enable 'apt-get source'
#deb-src http://raspbian.raspberrypi.org/raspbian/ stretch main contrib non-free rpi
deb-src http://mirrors.ustc.edu.cn/raspbian/raspbian/ stretch main contrib non-free rpi

2, 修改/etc/apt/sources.list.d/raspi.list为以下内容
#deb http://archive.raspberrypi.org/debian/ stretch main ui
deb http://mirrors.ustc.edu.cn/archive.raspberrypi.org/debian/ stretch main ui
# Uncomment line below then 'apt-get update' to enable 'apt-get source'
#deb-src http://mirrors.ustc.edu.cn/debian/ stretch main ui
deb-src http://mirrors.ustc.edu.cn/archive.raspberrypi.org/debian/ stretch main ui
#deb-src http://archive.raspberrypi.org/debian/ stretch main ui


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
sudo vim /etc/vim/vimrc # 在
