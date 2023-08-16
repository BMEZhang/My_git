什么是 Git LFS？
Git 是分布式 版本控制系统，这意味着在克隆过程中会将仓库的整个历史记录传输到客户端。对于包涵大文件（尤其是经常被修改的大文件）的项目，初始克隆需要大量时间，因为客户端会下载每个文件的每个版本。Git LFS（Large File Storage）是由 Atlassian, GitHub 以及其他开源贡献者开发的 Git 扩展，它通过延迟地（lazily）下载大文件的相关版本来减少大文件在仓库中的影响，具体来说，大文件是在 checkout 的过程中下载的，而不是 clone 或 fetch 过程中下载的（这意味着你在后台定时 fetch 远端仓库内容到本地时，并不会下载大文件内容，而是在你 checkout 到工作区的时候才会真正去下载大文件的内容）。

Git LFS 通过将仓库中的大文件替换为微小的指针（pointer） 文件来做到这一点。在正常使用期间，你将永远不会看到这些指针文件，因为它们是由 Git LFS 自动处理的：

1. 当你添加（执行 git add 命令）一个文件到你的仓库时，Git LFS 用一个指针替换其内容，并将文件内容存储在本地 Git LFS 缓存中（本地 Git LFS 缓存位于仓库的.git/lfs/objects 目录中）。
2. 当你推送新的提交到服务器时，新推送的提交引用的所有 Git LFS 文件都会从本地 Git LFS 缓存传输到绑定到 Git 仓库的远程 Git LFS 存储（即 LFS 文件内容会直接从本地 Git LFS 缓存传输到远程 Git LFS 存储服务器）。
3. 当你 checkout 一个包含 Git LFS 指针的提交时，指针文件将替换为本地 Git LFS 缓存中的文件，或者从远端 Git LFS 存储区下载。

关于 LFS 的指针文件：

LFS 的指针文件是一个文本文件，存储在 Git 仓库中，对应大文件的内容存储在 LFS 服务器里，而不是 Git 仓库中，下面为一个图片 LFS 文件的指针文件内容：

version https://git-lfs.github.com/spec/v1
oid sha256:5b62e134d2478ae0bbded57f6be8f048d8d916cb876f0656a8a6d1363716d999
size 285
 

指针文件很小，小于 1KB。其格式为 key-value 格式，第一行为指针文件规范 URL，第二行为文件的对象 id，也即 LFS 文件的存储对象文件名，可以在.git/lfs/objects 目录中找到该文件的存储对象，第三行为文件的实际大小（单位为字节）。所有 LFS 指针文件都是这种格式。

Git LFS 是无缝的：在你的工作副本中，你只会看到实际的文件内容。这意味着你不需要更改现有的 Git 工作流程就可以使用 Git LFS。你只需按常规进行 git checkout、编辑文件、git add 和 git commit。git clone 和 git pull 将明显更快，因为你只下载实际检出的提交所引用的大文件版本，而不是曾经存在过的文件的每一个版本。

为了使用 Git LFS，你将需要一个支持 Git LFS 的托管服务器，例如Bitbucket Cloud或Bitbucket Server（GitHub、GitLab也都支持 Git LFS）。仓库用户将需要安装 Git LFS 命令行客户端（参考这里其实更好），或支持 Git LFS 的 GUI 客户端，例如Sourcetree。


安装 Git LFS
1. 有三种简单的方式来安装 Git LFS：

a. 用你最喜欢的软件包管理器来安装它。git-lfs 软件包在 Homebrew，MacPorts，dnf 和packagecloud中都是可用的；或者

b. 从项目网站下载并安装Git LFS；

c. 安装 Sourcetree，它是捆绑了 Git LFS 的一个免费的 Git GUI 客户端。

2. 一旦安装好了 Git LFS，请运行 git lfs install 来初始化 Git LFS（如果你安装了 Sourcetree，可以跳过此步骤）：
$ git lfs install
Git LFS initialized.

你只需要运行 git lfs install 一次。为你的系统初始化后，当你克隆包含 Git LFS 内容的仓库时，Git LFS 将自动进行自我引导启用。

创建一个新的 Git LFS 仓库
要创建一个新的支持 Git LFS 的仓库，你需要在创建仓库后运行 git lfs install：

这将在你的仓库中安装一个特殊的 pre-push Git 钩子，该钩子将在你执行 git push 的时候传输 Git LFS 文件到服务器上。

所有Bitbucket Cloud仓库已自动启用 Git LFS 。对于Bitbucket Server，你需要在仓库设置中启用 Git LFS：

当你的仓库初始化了 Git LFS 后，你可以通过使用 git lfs track 来指定要跟踪的文件。

克隆现有的 Git LFS 仓库
安装 Git LFS 后，你可以像往常一样使用 git clone 命令来克隆 Git LFS 仓库。在克隆过程的结尾，Git 将检出默认分支（通常是 master），并且将自动为你下载完成检出过程所需的所有 Git LFS 文件。例如：
$ git clone git@bitbucket.org:tpettersen/Atlasteroids.gitCloning into 'Atlasteroids'...
remote: Counting objects: 156, done.
remote: Compressing objects: 100% (154/154), done.
remote: Total 156 (delta 87), reused 0 (delta 0)
Receiving objects: 100% (156/156), 54.04 KiB | 31.00 KiB/s, done.
Resolving deltas: 100% (87/87), done.
Checking connectivity... done.
Downloading Assets/Sprites/projectiles-spritesheet.png (21.14 KB)
Downloading Assets/Sprites/productlogos_cmyk-spritesheet.png (301.96 KB)
Downloading Assets/Sprites/shuttle2.png (1.62 KB)
Downloading Assets/Sprites/space1.png (1.11 MB)
Checking out files: 100% (81/81), done

仓库里有 4 个 PNG 文件被 Git LFS 跟踪。执行 git clone 命令时，在从仓库中检出指针文件的时候，Git LFS 文件被一个一个下载下来。

加快克隆速度
如果你正在克隆包含大量 LFS 文件的仓库，显式使用 git lfs clone 命令可提供更好的性能：

加快克隆速度
如果你正在克隆包含大量 LFS 文件的仓库，显式使用 git lfs clone 命令可提供更好的性能：

复制代码
$ git lfs clone git@bitbucket.org:tpettersen/Atlasteroids.git
Cloning into 'Atlasteroids'...
remote: Counting objects: 156, done.
remote: Compressing objects: 100% (154/154), done.
remote: Total 156 (delta 87), reused 0 (delta 0)
Receiving objects: 100% (156/156), 54.04 KiB | 0 bytes/s, done.
Resolving deltas: 100% (87/87), done.
Checking connectivity... done.
Git LFS: (4 of 4 files) 1.14 MB / 1.15 MB
复制代码
 

git lfs clone 命令不会一次下载一个 Git LFS 文件，而是等到检出（checkout）完成后再批量下载所有必需的 Git LFS 文件。这利用了并行下载的优势，并显著减少了产生的 HTTP 请求和进程的数量（这对于提高 Windows 的性能尤为重要）。

拉取并检出
就像克隆一样，你可以使用常规的 git pull 命令拉取 Git LFS 仓库。拉取完成后，所有需要的 Git LFS 文件都会作为自动检出过程的一部分而被下载。

复制代码
$ git pull
Updating 4784e9d..7039f0a
Downloading Assets/Sprites/powerup.png (21.14 KB)
Fast-forward
Assets/Sprites/powerup.png | 3 +
Assets/Sprites/powerup.png.meta | 4133 ++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
2 files changed, 4136 insertions(+)
create mode 100644 Assets/Sprites/projectiles-spritesheet.png
create mode 100644 Assets/Sprites/projectiles-spritesheet.png.meta
复制代码
 

不需要显式的命令即可获取 Git LFS 内容。然而，如果检出因为意外原因而失败，你可以通过使用 git lfs pull 命令来下载当前提交的所有丢失的 Git LFS 内容：

$ git lfs pull
Git LFS: (4 of 4 files) 1.14 MB / 1.15 MB
 

加快拉取速度
像 git lfs clone 命令一样，git lfs pull 命令批量下载 Git LFS 文件。如果你知道自上次拉取以来已经更改了大量文件，则不妨显式使用 git lfs pull 命令来批量下载 Git LFS 内容，而禁用在检出期间自动下载 Git LFS。这可以通过在调用 git pull 命令时使用-c 选项覆盖 Git 配置来完成：

$ git -c filter.lfs.smudge= -c filter.lfs.required=false pull && git lfs pull
 

由于输入的内容很多，你可能希望创建一个简单的Git 别名来为你执行批处理的 Git 和 Git LFS 拉取：

$ git config --global alias.plfs "\!git -c filter.lfs.smudge= -c filter.lfs.required=false pull && git lfs pull"
$ git plfs
 

当需要下载大量的 Git LFS 文件时，这将大大提高性能（同样，尤其是在 Windows 上）。

使用 Git LFS 跟踪文件
当向仓库中添加新的大文件类型时，你需要通过使用 git lfs track 命令指定一个模式来告诉 Git LFS 对其进行跟踪：

$ git lfs track "*.ogg"
Tracking *.ogg
 

请注意，"*.ogg"周围的引号很重要。省略它们将导致通配符被 shell 扩展，并将为当前目录中的每个.ogg 文件创建单独的条目：

# probably not what you want
$ git lfs track *.ogg
Tracking explode.ogg
Tracking music.ogg
Tracking phaser.ogg
 

Git LFS 支持的模式与.gitignore 支持的模式相同，例如：

复制代码
# track all .ogg files in any directory
$ git lfs track "*.ogg"
# track files named music.ogg in any directory
$ git lfs track "music.ogg"
# track all files in the Assets directory and all subdirectories
$ git lfs track "Assets/"
# track all files in the Assets directory but *not* subdirectories
$ git lfs track "Assets/*"
# track all ogg files in Assets/Audio
$ git lfs track "Assets/Audio/*.ogg"
# track all ogg files in any directory named Music
$ git lfs track "**/Music/*.ogg"
# track png files containing "xxhdpi" in their name, in any directory
$ git lfs track "*xxhdpi*.png
复制代码
 

这些模式是相对于你运行 git lfs track 命令的目录的。为了简单起见，最好是在仓库根目录运行 git lfs track。需要注意的是，Git LFS 不支持像.gitignore 那样的负模式（negative patterns）。

运行 git lfs track 后，你会在你的运行命令的仓库中发现名为.gitattributes 的新文件。.gitattributes 是一种 Git 机制，用于将特殊行为绑定到某些文件模式。Git LFS 自动创建或更新.gitattributes 文件，以将跟踪的文件模式绑定到 Git LFS 过滤器。但是，你需要将对.gitattributes 文件的任何更改自己提交到仓库：

复制代码
$ git lfs track "*.ogg"
Tracking *.ogg
$ git add .gitattributes
$ git diff --cached
diff --git a/.gitattributes b/.gitattributes
new file mode 100644
index 0000000..b6dd0bb
--- /dev/null
+++ b/.gitattributes
@@ -0,0 +1 @@
+*.ogg filter=lfs diff=lfs merge=lfs -text
$ git commit -m "Track ogg files with Git LFS"
复制代码
 

为了便于维护，通过始终从仓库的根目录运行 git lfs track，将所有 Git LFS 模式保持在单个.gitattributes 文件中是最简单的。然而，你可以通过调用不带参数的 git lfs track 命令来显示 Git LFS 当前正在跟踪的所有模式的列表（以及它们在其中定义的.gitattributes 文件）：

$ git lfs track
Listing tracked paths
*.stl (.gitattributes)
*.png (Assets/Sprites/.gitattributes)
*.ogg (Assets/Audio/.gitattributes)
 

你可以通过从.gitattributes 文件中删除相应的行，或者通过运行 git lfs untrack 命令来停止使用 Git LFS 跟踪特定模式：

复制代码
$ git lfs untrack "*.ogg"
Untracking *.ogg
$ git diff
diff --git a/.gitattributes b/.gitattributes
index b6dd0bb..e69de29 100644
--- a/.gitattributes
+++ b/.gitattributes
@@ -1 +0,0 @@
-*.ogg filter=lfs diff=lfs merge=lfs -text
复制代码
 

运行 git lfs untrack 命令后，你自己必须再次提交.gitattributes 文件的更改。

提交和推送
你可以按常规方式提交并推送到包含 Git LFS 内容的仓库。如果你已经提交了被 Git LFS 跟踪的文件的变更，则当 Git LFS 内容传输到服务器时，你会从 git push 中看到一些其他输出：

复制代码
$ git push
Git LFS: (3 of 3 files) 4.68 MB / 4.68 MB
Counting objects: 8, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (8/8), done.
Writing objects: 100% (8/8), 1.16 KiB | 0 bytes/s, done.
Total 8 (delta 1), reused 0 (delta 0)
To git@bitbucket.org:tpettersen/atlasteroids.git
7039f0a..b3684d3 master -> master
复制代码
 

如果由于某些原因传输 LFS 文件失败，推送将被终止，你可以放心地重试。与 Git 一样，Git LFS 存储也是内容寻址 的（而不是按文件名寻址）：内容是根据密钥存储的，该密钥是内容本身的 SHA-256 哈希。这意味着重新尝试将 Git LFS 文件传输到服务器总是安全的；你不可能用错误的版本意外覆盖 Git LFS 文件的内容。

在主机之间移动 Git LFS 仓库
要将 Git LFS 仓库从一个托管提供者迁移到另一个托管提供者序，你可以结合使用指定了-all 选项的 git lfs fetch 和 git lfs push 命令。

例如，要将所有 Git 和 Git LFS 仓库从名为github的远端移动到名为bitbucket 的远端：

复制代码
# create a bare clone of the GitHub repository
$ git clone --bare git@github.com:kannonboy/atlasteroids.git
$ cd atlasteroids
# set up named remotes for Bitbucket and GitHub
$ git remote add bitbucket git@bitbucket.org:tpettersen/atlasteroids.git
$ git remote add github git@github.com:kannonboy/atlasteroids.git
# fetch all Git LFS content from GitHub
$ git lfs fetch --all github
# push all Git and Git LFS content to Bitbucket
$ git push --mirror bitbucket
$ git lfs push --all bitbucket
复制代码
 

获取额外的 Git LFS 历史记录
Git LFS 通常仅下载你实际在本地检出的提交所需的文件。但是，你可以使用 git lfs fetch --recent 命令强制 Git LFS 为其他最近修改的分支下载额外的内容：

复制代码
$ git lfs fetch --recent
Fetching master
Git LFS: (0 of 0 files, 14 skipped) 0 B / 0 B, 2.83 MB skipped Fetching recent branches within 7 days
Fetching origin/power-ups
Git LFS: (8 of 8 files, 4 skipped) 408.42 KB / 408.42 KB, 2.81 MB skipped
Fetching origin/more-music
Git LFS: (1 of 1 files, 14 skipped) 1.68 MB / 1.68 MB, 2.83 MB skipped
复制代码
 

这对于在外出午餐时批量下载新的 Git LFS 内容很有用，或者如果你打算与队友一起审查工作，并且由于网络连接受限而无法在以后下载内容时，这将非常有用。 例如，你可能希望在上飞机之前先运行 git lfs fetch --recent！

Git LFS 会考虑包含最近提交超过 7 天的提交的任何分支或标签。 你可以通过设置 lfs.fetchrecentrefsdays 属性来配置被视为最近的天数：

# download Git LFS content for branches or tags updated in the last 10 days
$ git config lfs.fetchrecentrefsdays 10
 

默认情况下，git lfs fetch --recent 将仅在最近分支或标记的最新提交下载 Git LFS 内容。
