## 第1步： 新建一个仓库（ Repository）
如果会新建仓库，请无视本步骤，直接跳到第二步。

#### 1） 进入Github首页

#### 2） 点击New repository或start a project
创建一个repository,可以有两种方式：
（1）左栏右上的绿色按钮New 来 new一个repository,
或者（2）中间栏 中下位置右边的大按钮 “Start a project”.

#### 3）填写信息
Repository name: 仓库名称
Description(可选): 仓库描述介绍,不是必填项目。~~建议填写上哦！
Public, Private : 仓库权限。public 公开共享，但是是免费的。private 私有或指定合作者,需付费使用。
Initialize this repository with a README: 添加一个README.md
gitignore: 不需要进行版本管理的仓库类型，对应生成文件.gitignore
license: 证书类型，对应生成文件LICENSE

#### 4）点击 create repository
至此，仓库（ Repository）新建完毕！


## 第2步： Clone or dowload仓库地址

来到新建好的repository，点击右侧中部的Clone or dowload会出现一个地址，copy这个地址备用。

## 第3步： 上传文件夹至github
以下为本地操作。

#### 1）新建空文件夹
本地电脑新建空文件夹。（新建空文件夹不是文件夹上传github唯一的或必须的或最优的办法，但是我个人觉得会省去很多各种出错的麻烦事儿，新手适用。个人观点，仅供参考。）

#### 2）克隆新仓库
进入文件夹，右键会出现2个选项： Git Gui Here,Git Bash Here。 选择Git Bash Here，然后把github上面新建的仓库克隆到本地。（这里会用到第2步复制的那个仓库地址。）

克隆仓库，代码如下：git clone + 你的仓库地址
（这里我的仓库地址是https://github.com/jojowei/mySpiders.git）

#### 3）上传前准备
上个步骤完成之后，本地新建的文件夹下面就会多出个文件夹。该文件夹名即为你github上面新建的那个Repository 的名字。我们把要上传的文件夹里面的内容全部复制到这个本地的Repository同名的文件夹下，然后cd进入克隆的这个文件夹： cd + 文件夹名字

#### 4) 依次输入以下代码：

git init
git add .
git commit -m “你的提交信息”
git push

依次输入以上代码后，进入github查看：文件夹已上传github, 上传成功。
(本例中我的仓库是mySpiders, 我要上传的文件夹是scrapy_douban.)

#### 【注意】：
“ git init ” git初始化
“ git add . ” 提交到缓存区。这里的"add"和“ . ”中间有个空格。如果不写空格，git无法识别。“add .” 这里就是添加所有文件的意思。

" git commit -m “你的提交信息” "是将暂存区里的改动给提交到本地的版本库。 这里“你的提交信息”是你自己想要提交的信息哦，比如，“create a project”, “first commit”.

“git push ”是将本地版本库的分支推送到远程服务器上对应的分支,提交到远程的github仓库,。（此操作目的是把本地仓库push到github上面，此步骤可能需要你输入帐号和密码）

如果" git push "不成功, " git push -u origin master "也不能解决问题，如果是因为github中的README.md文件不在本地代码目录中， 可以通过如下命令进行代码合并
　　git pull --rebase origin master
这里，pull=fetch+merge。合并后再用“git push”就可以上传了。

https://blog.csdn.net/jojowei/article/details/89008657
