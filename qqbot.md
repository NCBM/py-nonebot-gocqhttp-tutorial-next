# 基于 NoneBot2 和 go-cqhttp 的 QQ 机器人的进阶教程

## 温馨提示

作为一个进阶教程，我们不会涉及特别基础的东西。  
如有需要请自行搜索其它教程。

这里选用 `Windows 10` | `Python 3.9.10` | `nonebot2 2.0.0-rc.1` 作为示范。

## 官网链接

[NoneBot 官网](https://v2.nonebot.dev/) | [go-cqhttp 官网](https://go-cqhttp.org)

## 1. venv 环境的简单搭建

`venv` 是 Python 中自带的虚拟环境创建工具。

Python有各种各样的系统包和第三方开发的包，极大地便利了我们的开发。不过这也引入了一个问题：**不同代码需要的包版本可能是不一样的**。

如果你只用 Python 搭建 nonebot 机器人，这并无大碍；**但如果你的 Python 还要做其他事情**，就可能会出现这种情况：为了某代码修改了依赖包的版本使其能正常工作了，但是之前安装的 nonebot 没法正常工作了。

因此我们常常需要对不同的代码设置不同的 Python 虚拟环境。这可以优雅地避免不同版本包的冲突。

### 创建虚拟环境

首先我们在命令行中进入我们的目标目录，然后我们可以在命令行中运行以下命令：

```bash
python -m venv venv
```

这会在当前目录的 venv 文件夹下创建虚拟环境。

> 注意：如果系统中装有多个 Python 版本，在 Windows 上一定要使用绝对路径，在 Linux 上一定要指明 Python 版本。
> 如：
>
> ```console
> # Windows
> "C:\Users\world\AppData\Local\Programs\Python\Python39\python.exe" -m venv venv
> # Linux
> python3.9 -m venv venv
> ```

### 虚拟环境的使用

#### 进入虚拟环境

- Linux

    在 `Linux/MinGW` 下运行以下命令进入虚拟环境（以 bash 为例）：

    ```bash
    source venv/bin/activate
    ```

- Windows

    在 `cmd` 下运行以下命令进入虚拟环境：

    ```cmd
    .\venv\Scripts\activate.bat
    ```

    如果你用的是 `Powershell`，则输入以下命令：

    ```powershell
    .\venv\Scripts\Activate.ps1
    ```

进入成功后可以看到命令行的提示符前面会出现括号，里面是虚拟环境名称。

#### 退出虚拟环境

在已经进入虚拟环境的命令行窗口中运行以下命令退出虚拟环境：

```console
deactivate
```

### 在虚拟环境中部署 nonebot

有了全新独立的虚拟环境，我们就可以安心部署了。

**首先我们要再次进入虚拟环境**，此处不再赘述。

然后跟着 NoneBot 教程走：

```bash
pip install nb-cli
nb create
# ...
nb run
```

最终如果看到正常的启动信息，恭喜你，大功告成！

### 为什么不用 poetry/conda

~~首要原因是：我不会。~~

这两个工具很便利，但也因此很重，不如 venv 轻量且广泛可用。

当然 venv 的缺点也是功能少，需要其它工具配合使用。

## 2. 公用代码

很多时候我们会发现有不少结构相似或重复的代码，这时我们可以将它们抽离出来，使代码更加美观。

这时我们面临一个问题：这些代码该放哪里？

### Python 的相对导入机制

Python 的相对导入是从**入口文件**所在的文件夹开始计算的。

#### 入口文件

入口文件即运行 Python 时执行者提供的文件，非入口文件即运行时导入的文件。

比如，运行下面一条命令：

```bash
python abababa.py
```

其中 `abababa.py` 就是一个入口文件。

如果这个文件里写有：

```python
import abab
```

那么同一目录下的 `abab.py` 就是一个非入口文件。

Python 会在入口文件中将 `__name__` 设为 `"__main__"`，而在非入口文件中将其设为其文件路径。因此你会在一些代码中见到这样的结构：

```python
if __name__ == "__main__":
    ...
```

这就是 Python 程序用来区分直接运行和间接导入并执行特定代码的方法。

### 在 nonebot 中的实践应用

我们在运行 `nb run` 的时候，实际上是将 `bot.py` 作为入口文件运行，因此以 `bot.py` 为基础的所有程序都会从项目文件夹（`bot.py` 所在文件夹）开始计算导入关系。

所以我们在项目文件夹里新建一个叫 `public` 的文件夹。

```console
mkdir public
```

然后我们在这个文件夹里写公用代码。

比如，我们新建一个 `abab.py`：

```python
def say_a():
    print("aaaaaaaaaaaaaaaaaaa---")
```

接着在插件里写上导入并使用：

```python
from public.abab import say_a

say_a()
```

最后运行。

```console
nb run
```

当看到打印的 aaaaaaaaaaaa 的时候，大功告成！
