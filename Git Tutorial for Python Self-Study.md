# Git Tutorial for Python Self-Study

下面是把 **CS61B Java Git Tutorial** 改写成适合自学 Git 的 **Python 版本教程**。

这个版本保留原教程的学习路径：先配置 Git，再创建或克隆仓库，再写一个小 Python 项目，然后练习 `status -> add -> commit -> push -> pull` 的完整工作流。原教程主要围绕 Java libraries、IntelliJ、`Arithmetic.java`、Gradescope 提交展开；这里改成 Python 虚拟环境、VS Code/PyCharm、pytest、小型 Python 项目和 GitHub workflow。

---

## 0. 你要先理解：Git 到底在干什么？

Git 不是“自动保存软件”，而是一个**版本控制系统**。

你可以把它理解成：

> Git 会在你主动要求的时候，给整个项目文件夹拍一张“快照”。
> 以后如果代码写坏了，你可以回到之前的快照，也可以查看自己改过什么。

最核心的几个概念：

| 概念 | 含义 |
| --- | --- |
| repository / repo | 一个被 Git 管理的项目文件夹 |
| commit | 一次项目快照 |
| staging area | 临时准备区，表示“下一次 commit 要保存哪些文件” |
| remote | 远程仓库，例如 GitHub 上的仓库 |
| origin | 默认远程仓库名字 |
| branch | 分支，通常主分支叫 `main` |
| clone | 从 GitHub 下载一个仓库 |
| pull | 从 GitHub 拉取最新版本 |
| push | 把本地 commit 上传到 GitHub |

你最开始只需要熟练这条主线：

```bash
git status
git add .
git commit -m "message"
git push origin main
```

---

## 1. 安装和配置 Git

### 1.1 检查 Git 是否安装

打开 Terminal，输入：

```bash
git --version
```

如果能看到类似：

```bash
git version 2.xx.x
```

说明已经安装。

如果没有安装，macOS 可以先运行：

```bash
xcode-select --install
```

或者用 Homebrew：

```bash
brew install git
```

### 1.2 配置你的 Git 用户名和邮箱

这一步对应原教程里的：

```bash
git config --global user.name "<your name>"
git config --global user.email "<your email>"
```

你可以设置成：

```bash
git config --global user.name "Kita Ena"
git config --global user.email "chenyilun30@gmail.com"
```

这个信息会出现在你的 commit 记录里。

### 1.3 设置默认分支名为 main

```bash
git config --global init.defaultBranch main
```

现在 GitHub 默认主分支基本都叫 `main`，不要用老的 `master`。

### 1.4 设置 pull 策略

```bash
git config --global pull.rebase false
```

这一步的意思是：以后你运行 `git pull` 的时候，默认用 merge 方式合并远程更新。对初学者比较直观。

### 1.5 设置 Git 默认编辑器

原教程建议把默认编辑器从 vim 改成 nano，因为 vim 对初学者很不友好。这个建议仍然适用。

可以运行：

```bash
git config --global core.editor "nano"
```

以后如果 Git 需要你输入 commit message，它会打开 nano，而不是 vim。

---

## 2. 配置 GitHub SSH 登录

Git 可以通过 HTTPS 或 SSH 和 GitHub 通信。建议用 SSH。

### 2.1 检查是否已有 SSH key

```bash
ls ~/.ssh
```

如果看到类似：

```bash
id_ed25519
id_ed25519.pub
```

说明你可能已经有 SSH key。

如果没有，就生成一个：

```bash
ssh-keygen -t ed25519 -C "chenyilun30@gmail.com"
```

一路按 Enter 即可。

### 2.2 复制公钥

```bash
cat ~/.ssh/id_ed25519.pub
```

你会看到类似：

```bash
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAI... chenyilun30@gmail.com
```

把这一整行复制下来。

### 2.3 添加到 GitHub

进入 GitHub：

```text
GitHub -> Settings -> SSH and GPG keys -> New SSH key
```

Title 可以写：

```text
MacBook Air
```

Key type 选：

```text
Authentication Key
```

Key 里面粘贴刚才复制的内容。

### 2.4 测试 SSH 是否成功

```bash
ssh -T git@github.com
```

如果成功，会看到类似：

```bash
Hi USERNAME! You've successfully authenticated, but GitHub does not provide shell access.
```

原教程里也是用这个命令确认 GitHub SSH 认证是否成功。

---

## 3. 创建一个 Python Git 练习项目

原教程是 clone 一个 Java 课程仓库，再拉 skeleton code。这里改成你自己创建一个 Python 小项目。

### 3.1 建立学习文件夹

例如你可以建一个总文件夹：

```bash
mkdir ~/git-python-practice
cd ~/git-python-practice
```

然后创建项目：

```bash
mkdir arithmetic-python
cd arithmetic-python
```

初始化 Git：

```bash
git init
```

此时这个文件夹已经是一个 Git repository。

### 3.2 创建 Python 项目结构

运行：

```bash
mkdir src tests
touch src/arithmetic.py
touch tests/test_arithmetic.py
touch README.md
touch .gitignore
```

项目结构大概是：

```text
arithmetic-python/
├── README.md
├── .gitignore
├── src/
│   └── arithmetic.py
└── tests/
    └── test_arithmetic.py
```

### 3.3 写 `.gitignore`

打开 `.gitignore`，写入：

```gitignore
__pycache__/
*.pyc
.venv/
.env
.DS_Store
.pytest_cache/
```

意思是：这些缓存文件、虚拟环境、本地系统文件不要上传到 GitHub。

---

## 4. 写一个 Python 小程序

### 4.1 `src/arithmetic.py`

写入：

```python
def add(a, b):
    return a + b


def multiply(a, b):
    return a * b


def is_even(n):
    return n % 2 == 0


if __name__ == "__main__":
    x = int(input("Enter first number: "))
    y = int(input("Enter second number: "))

    print(f"{x} + {y} = {add(x, y)}")
    print(f"{x} * {y} = {multiply(x, y)}")
```

运行：

```bash
python src/arithmetic.py
```

如果你的 Mac 默认 `python` 不是 Python 3，可以用：

```bash
python3 src/arithmetic.py
```

---

## 5. 创建 Python 虚拟环境

Python 项目最好用虚拟环境隔离依赖。

在项目根目录运行：

```bash
python3 -m venv .venv
```

激活虚拟环境：

```bash
source .venv/bin/activate
```

成功后，终端前面通常会出现：

```bash
(.venv)
```

安装测试工具：

```bash
pip install pytest
```

把依赖写入 `requirements.txt`：

```bash
pip freeze > requirements.txt
```

现在项目结构变成：

```text
arithmetic-python/
├── README.md
├── .gitignore
├── requirements.txt
├── src/
│   └── arithmetic.py
└── tests/
    └── test_arithmetic.py
```

注意：`.venv/` 不要提交，因为 `.gitignore` 已经忽略了它。

---

## 6. 写 Python 测试

### 6.1 `tests/test_arithmetic.py`

写入：

```python
from src.arithmetic import add, multiply, is_even


def test_add():
    assert add(2, 3) == 5
    assert add(-1, 1) == 0


def test_multiply():
    assert multiply(3, 4) == 12
    assert multiply(-2, 5) == -10


def test_is_even():
    assert is_even(2) is True
    assert is_even(3) is False
```

运行测试：

```bash
pytest
```

如果看到类似：

```text
3 passed
```

说明测试通过。

---

## 7. 第一次使用 Git 保存项目

原教程里强调：改完代码后，先 `git status`，再 `git add`，再 `git commit`，最后 `git push`。这个流程是 Git 自学最重要的部分。

### 7.1 查看当前状态

```bash
git status
```

你会看到很多未追踪文件，例如：

```text
Untracked files:
  .gitignore
  README.md
  requirements.txt
  src/
  tests/
```

意思是：Git 发现这些文件存在，但还没有被纳入版本控制。

### 7.2 添加文件到 staging area

```bash
git add .
```

`.` 表示添加当前目录下所有应该被追踪的文件。

再次查看：

```bash
git status
```

你会看到类似：

```text
Changes to be committed:
  new file: .gitignore
  new file: README.md
  new file: requirements.txt
  new file: src/arithmetic.py
  new file: tests/test_arithmetic.py
```

### 7.3 创建第一次 commit

```bash
git commit -m "Initial Python arithmetic project"
```

这就相当于给项目拍了第一张快照。

---

## 8. 创建 GitHub 仓库并连接本地项目

### 8.1 在 GitHub 创建新仓库

进入 GitHub，点击：

```text
New repository
```

仓库名可以叫：

```text
arithmetic-python
```

注意：

- 不要勾选 Initialize with README
- 不要添加 `.gitignore`
- 不要添加 license

因为你本地已经有这些文件了。

### 8.2 连接远程仓库

GitHub 会给你一个 SSH 地址，类似：

```bash
git@github.com:chenyilun30/arithmetic-python.git
```

在本地项目里运行：

```bash
git remote add origin git@github.com:chenyilun30/arithmetic-python.git
```

检查：

```bash
git remote -v
```

应该看到：

```text
origin  git@github.com:chenyilun30/arithmetic-python.git (fetch)
origin  git@github.com:chenyilun30/arithmetic-python.git (push)
```

### 8.3 推送到 GitHub

```bash
git branch -M main
git push -u origin main
```

第一次 push 用 `-u`，意思是把本地 `main` 分支和远程 `origin/main` 建立默认关联。

之后你只需要：

```bash
git push
```

或者显式写：

```bash
git push origin main
```

---

## 9. 日常 Python 项目的 Git 工作流

以后你每次写代码，都应该按照这个节奏：

### 9.1 开始写代码前

先进入项目文件夹：

```bash
cd ~/git-python-practice/arithmetic-python
```

如果有虚拟环境：

```bash
source .venv/bin/activate
```

然后先拉取远程最新版：

```bash
git pull origin main
```

这对应原教程里“坐下来开始工作时先 pull，工作中经常 commit，结束时 push”的习惯。

### 9.2 写代码过程中

例如你修改了 `src/arithmetic.py`，加一个函数：

```python
def subtract(a, b):
    return a - b
```

然后更新测试：

```python
def test_subtract():
    assert subtract(5, 3) == 2
```

修改完以后运行：

```bash
pytest
```

确认测试通过。

### 9.3 查看改动

```bash
git status
```

再看具体改了什么：

```bash
git diff
```

`git diff` 会显示你这次相对于上一次 commit 改了哪些内容。

### 9.4 保存这次改动

```bash
git add src/arithmetic.py tests/test_arithmetic.py
git commit -m "Add subtract function and tests"
```

如果你确定所有修改都要保存，也可以：

```bash
git add .
git commit -m "Add subtract function and tests"
```

### 9.5 上传到 GitHub

```bash
git push origin main
```

---

## 10. 你需要真正记住的 Git 命令

### 查看状态

```bash
git status
```

这是最重要的 Git 命令之一。任何时候不确定发生了什么，先运行它。

### 查看具体修改

```bash
git diff
```

查看还没 add 的修改。

如果想看已经 staged 的修改：

```bash
git diff --staged
```

### 添加文件

```bash
git add filename.py
```

或者添加全部：

```bash
git add .
```

### 创建 commit

```bash
git commit -m "Describe what changed"
```

commit message 要具体，不要写：

```bash
git commit -m "update"
```

更好的写法：

```bash
git commit -m "Fix arithmetic edge case for negative numbers"
```

### 上传

```bash
git push origin main
```

### 下载远程更新

```bash
git pull origin main
```

### 查看历史

```bash
git log
```

更简洁一点：

```bash
git log --oneline
```

---

## 11. 练习：故意制造一个 bug，然后用 Git 找回

这是自学 Git 很重要的一步。

### 11.1 先确认当前干净

```bash
git status
```

如果看到：

```text
nothing to commit, working tree clean
```

说明当前没有未保存修改。

### 11.2 故意改坏代码

把：

```python
def add(a, b):
    return a + b
```

改成：

```python
def add(a, b):
    return a - b
```

运行测试：

```bash
pytest
```

你会看到测试失败。

### 11.3 查看哪里坏了

```bash
git diff
```

你会看到类似：

```diff
-    return a + b
+    return a - b
```

### 11.4 放弃这次未提交的修改

如果你确定这次修改不要了：

```bash
git restore src/arithmetic.py
```

再运行：

```bash
pytest
```

测试应该恢复通过。

这一步很重要：`git restore` 可以撤销**还没有 commit 的文件修改**。

---

## 12. 练习：创建分支做实验

分支的作用是：你可以在不破坏主线代码的情况下尝试新功能。

### 12.1 创建并切换到新分支

```bash
git switch -c add-division
```

或者老写法：

```bash
git checkout -b add-division
```

### 12.2 添加新函数

在 `src/arithmetic.py` 加：

```python
def divide(a, b):
    if b == 0:
        raise ValueError("Cannot divide by zero")
    return a / b
```

在测试里加：

```python
import pytest
from src.arithmetic import divide


def test_divide():
    assert divide(6, 3) == 2


def test_divide_by_zero():
    with pytest.raises(ValueError):
        divide(1, 0)
```

运行：

```bash
pytest
```

### 12.3 commit 这个分支上的修改

```bash
git add .
git commit -m "Add safe divide function"
```

### 12.4 回到 main 分支

```bash
git switch main
```

你会发现刚才加的 `divide` 可能不见了，因为它只在 `add-division` 分支上。

### 12.5 合并分支

```bash
git merge add-division
```

现在 `divide` 功能被合并回 `main`。

上传：

```bash
git push origin main
```

---

## 13. Python 项目推荐工作流

对你来说，比较适合的工作流是：

```bash
cd project-folder
source .venv/bin/activate
git pull origin main

# 写代码
pytest

git status
git diff
git add .
git commit -m "Meaningful message"
git push origin main
```

可以压缩成一句记忆：

> pull before work, test before commit, push after commit.

中文就是：

> 开始前先 pull，提交前先 test，提交后再 push。

---

## 14. 从 GitHub clone 自己的项目到另一台电脑

如果你以后在另一台电脑上继续写这个项目：

```bash
git clone git@github.com:chenyilun30/arithmetic-python.git
cd arithmetic-python
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
pytest
```

这样就恢复了项目环境。

注意：`.venv/` 不会被 Git 保存，所以每台电脑都要自己重新创建虚拟环境。

---

## 15. 常见错误和处理

### 15.1 `fatal: not a git repository`

说明你不在 Git 项目文件夹里。

运行：

```bash
pwd
ls
```

确认你是不是在项目根目录。

### 15.2 `nothing to commit, working tree clean`

这不是错误。意思是当前没有新修改需要保存。

### 15.3 `Your branch is ahead of 'origin/main'`

意思是你本地有 commit，但是还没有 push。

解决：

```bash
git push origin main
```

### 15.4 `Your branch is behind 'origin/main'`

意思是 GitHub 上有新内容，你本地还没有。

解决：

```bash
git pull origin main
```

### 15.5 push 被拒绝

如果看到类似：

```text
rejected
non-fast-forward
```

通常说明远程仓库比你本地更新。

先运行：

```bash
git pull origin main
```

如果没有冲突，再：

```bash
git push origin main
```

### 15.6 merge conflict

如果两边改了同一个文件同一部分，可能出现 conflict。

文件里会出现：

```text
<<<<<<< HEAD
你的本地版本
=======
远程版本
>>>>>>> origin/main
```

你需要手动编辑成最终想要的样子，然后：

```bash
git add .
git commit -m "Resolve merge conflict"
git push origin main
```

---

## 16. 自学 Git 的练习路线

### Level 1：基础保存

目标：熟悉 `status/add/commit/push`

练习：

1. 创建一个 Python 项目。
2. 写一个函数。
3. 写一个测试。
4. commit。
5. push 到 GitHub。

### Level 2：修改和查看差异

目标：熟悉 `diff` 和 `restore`

练习：

1. 故意改坏一个函数。
2. 用 `pytest` 发现错误。
3. 用 `git diff` 看改动。
4. 用 `git restore` 恢复。

### Level 3：分支实验

目标：熟悉 branch workflow

练习：

1. 创建新分支。
2. 添加一个新功能。
3. commit。
4. merge 回 main。

### Level 4：模拟真实科研/课程项目

你可以建一个更接近你实际需求的 Python repo，例如：

```text
rydberg-data-analysis/
├── README.md
├── requirements.txt
├── data/
├── notebooks/
├── src/
│   ├── fitting.py
│   ├── plotting.py
│   └── preprocessing.py
├── tests/
│   └── test_fitting.py
└── scripts/
    └── analyze_scan.py
```

`.gitignore` 里可以加：

```gitignore
data/raw/
*.h5
*.csv
*.npy
*.npz
__pycache__/
.venv/
.ipynb_checkpoints/
.DS_Store
```

科研项目里尤其要注意：**大数据文件不要直接 commit 到 GitHub**，除非你明确知道文件很小、可以公开、也适合版本控制。

---

## 17. 和原 Java 教程的对应关系

| 原 Java/CS61B 教程 | Python 自学版 |
| --- | --- |
| Java libraries repo | Python `requirements.txt` / virtualenv |
| IntelliJ | VS Code / PyCharm / terminal |
| `Arithmetic.java` | `src/arithmetic.py` |
| `ArithmeticTest.java` | `tests/test_arithmetic.py` |
| JUnit tests | pytest |
| skeleton remote | 你自己的 starter repo 或 template repo |
| Gradescope submit | GitHub push / GitHub Classroom / 手动提交 |
| `git add lab01/src/Arithmetic.java` | `git add src/arithmetic.py tests/test_arithmetic.py` |
| `git push origin main` | 同样适用 |

---

## 18. 最小 Git Cheat Sheet

你真正要背下来的就是这些：

```bash
# 第一次配置
git config --global user.name "Your Name"
git config --global user.email "your@email.com"
git config --global init.defaultBranch main
git config --global pull.rebase false

# 新建项目
git init

# 查看状态
git status

# 查看修改
git diff

# 添加修改
git add .

# 提交
git commit -m "message"

# 连接远程仓库
git remote add origin git@github.com:USERNAME/REPO.git

# 第一次推送
git branch -M main
git push -u origin main

# 之后推送
git push origin main

# 拉取远程更新
git pull origin main

# 查看历史
git log --oneline

# 撤销未提交修改
git restore filename.py

# 创建分支
git switch -c new-branch

# 切回 main
git switch main

# 合并分支
git merge new-branch
```

---

## 19. 推荐你实际照做的一次完整练习

直接按下面这一套敲一遍：

```bash
mkdir ~/git-python-practice
cd ~/git-python-practice

mkdir arithmetic-python
cd arithmetic-python

git init

mkdir src tests
touch src/arithmetic.py tests/test_arithmetic.py README.md .gitignore

python3 -m venv .venv
source .venv/bin/activate
pip install pytest
pip freeze > requirements.txt
```

写好 Python 文件和测试以后：

```bash
pytest
git status
git add .
git commit -m "Initial Python arithmetic project"
```

然后去 GitHub 创建空仓库，再：

```bash
git remote add origin git@github.com:chenyilun30/arithmetic-python.git
git branch -M main
git push -u origin main
```

之后每次写代码的固定流程：

```bash
git pull origin main
pytest
git status
git diff
git add .
git commit -m "Describe your change"
git push origin main
```

这就已经覆盖了你自学 Git 最常用的 80% 场景。
