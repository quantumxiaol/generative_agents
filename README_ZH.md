
# 生成式智能体：人类行为的交互式模拟

<p align="center" width="100%">
<img src="cover.png" alt="Smallville" style="width: 80%; min-width: 300px; display: block; margin: auto;">
</p>

本仓库伴随我们的研究论文"[生成式智能体：人类行为的交互式模拟](https://arxiv.org/abs/2304.03442)"。它包含了生成式智能体的核心模拟模块——能够模拟可信人类行为的计算智能体——以及它们的游戏环境。下面，我们记录了在本地机器上设置模拟环境以及将模拟重放为演示动画的步骤。

## <img src="https://joonsungpark.s3.amazonaws.com:443/static/assets/characters/profile/Isabella_Rodriguez.png" alt="Generative Isabella">   环境设置
要设置您的环境，您需要生成一个包含您的 OpenAI API 密钥的 `utils.py` 文件，并下载必要的包。

### 步骤 1. 生成 Utils 文件
在 `reverie/backend_server` 文件夹中（`reverie.py` 所在的位置），创建一个名为 `utils.py` 的新文件，并将以下内容复制粘贴到文件中：
```
# Copy and paste your OpenAI API Key
openai_api_key = "<Your OpenAI API>"
# Put your name
key_owner = "<Name>"
# Leave as None to use default OpenAI API endpoint
base_url = "https://api2.aigcbest.top/v1/chat/completions"  # e.g., "https://api.openai.com/v1" or your custom endpoint

maze_assets_loc = "../../environment/frontend_server/static_dirs/assets"
env_matrix = f"{maze_assets_loc}/the_ville/matrix"
env_visuals = f"{maze_assets_loc}/the_ville/visuals"

fs_storage = "../../environment/frontend_server/storage"
fs_temp_storage = "../../environment/frontend_server/temp_storage"

collision_block_id = "32125"

# Verbose 
debug = True
```
将 `<Your OpenAI API>` 替换为您的 OpenAI API 密钥，将 `<name>` 替换为您的姓名。
 
### 步骤 2. 安装依赖
本项目使用 `uv` 进行依赖管理。运行以下命令安装所有依赖：

    uv sync

关于 Python 版本的说明：我们在 Python 3.9.12 上测试了环境，但项目现在需要 Python >=3.12。

### 步骤 3. 数据库设置
运行 Django 迁移以自动创建数据库（`db.sqlite3`）：

    cd environment/frontend_server
    python manage.py migrate

## <img src="https://joonsungpark.s3.amazonaws.com:443/static/assets/characters/profile/Klaus_Mueller.png" alt="Generative Klaus">   运行模拟
要运行新的模拟，您需要同时启动两个服务器：环境服务器和智能体模拟服务器。

### 步骤 1. 启动环境服务器
同样，环境是作为 Django 项目实现的，因此您需要启动 Django 服务器。为此，首先在命令行中导航到 `environment/frontend_server`（这是 `manage.py` 所在的位置）。然后运行以下命令：

    python manage.py runserver

然后，在您喜欢的浏览器中，访问 [http://localhost:8000/](http://localhost:8000/)。如果您看到一条消息说"Your environment server is up and running"（您的环境服务器正在运行），则说明您的服务器运行正常。确保在运行模拟时环境服务器继续运行，因此请保持此命令行标签页打开！（注意：我建议使用 Chrome 或 Safari。Firefox 可能会产生一些前端故障，尽管它不应该干扰实际的模拟。）

### 步骤 2. 启动模拟服务器
打开另一个命令行（您在步骤 1 中使用的那个应该仍在运行环境服务器，因此保持原样）。导航到 `reverie/backend_server` 并运行 `reverie.py`。

    python reverie.py
这将启动模拟服务器。将出现一个命令行提示，询问以下内容："Enter the name of the forked simulation: "（输入分叉模拟的名称：）。要启动一个包含 Isabella Rodriguez、Maria Lopez 和 Klaus Mueller 的 3 智能体模拟，请键入以下内容：
    
    base_the_ville_isabella_maria_klaus
然后提示将询问"Enter the name of the new simulation: "（输入新模拟的名称：）。键入任何名称来表示您当前的模拟（例如，现在只需"test-simulation"即可）。

    test-simulation
保持模拟器服务器运行。在此阶段，它将显示以下提示："Enter option: "（输入选项：）

### 步骤 3. 运行和保存模拟
在浏览器中，导航到 [http://localhost:8000/simulator_home](http://localhost:8000/simulator_home)。您应该看到 Smallville 的地图，以及地图上活动智能体的列表。您可以使用键盘箭头在地图上移动。请保持此标签页打开。要运行模拟，请在模拟服务器中响应提示"Enter option"（输入选项）时键入以下命令：

    run <step-count>
请注意，您需要将上面的 `<step-count>` 替换为表示要模拟的游戏步数的整数。例如，如果您想模拟 100 个游戏步数，您应该输入 `run 100`。一个游戏步数代表游戏中的 10 秒。


您的模拟应该正在运行，您将在浏览器中看到智能体在地图上移动。模拟运行完成后，"Enter option"（输入选项）提示将重新出现。此时，您可以通过重新输入带有所需游戏步数的 run 命令来模拟更多步数，通过键入 `exit` 退出模拟而不保存，或通过键入 `fin` 保存并退出。

保存的模拟可以在下次运行模拟服务器时通过提供您的模拟名称作为分叉模拟来访问。这将允许您从上次离开的地方重新启动模拟。

### 步骤 4. 重放模拟
您可以通过运行环境服务器并在浏览器中导航到以下地址来重放已运行的模拟：`http://localhost:8000/replay/<simulation-name>/<starting-time-step>`。请确保将 `<simulation-name>` 替换为您要重放的模拟名称，将 `<starting-time-step>` 替换为您希望开始重放的整数时间步。

例如，通过访问以下链接，您将启动一个预模拟示例，从时间步 1 开始：  
[http://localhost:8000/replay/July1_the_ville_isabella_maria_klaus-step-3-20/1/](http://localhost:8000/replay/July1_the_ville_isabella_maria_klaus-step-3-20/1/)

### 步骤 5. 演示模拟
您可能已经注意到，重放中的所有角色精灵看起来都相同。我们想澄清的是，重放功能主要用于调试目的，并不优先考虑优化模拟文件夹的大小或视觉效果。要正确演示具有适当角色精灵的模拟，您需要先压缩模拟。为此，使用文本编辑器打开位于 `reverie` 目录中的 `compress_sim_storage.py` 文件。然后，执行 `compress` 函数，将目标模拟的名称作为其输入。通过这样做，模拟文件将被压缩，使其准备好进行演示。

要开始演示，请在浏览器中访问以下地址：`http://localhost:8000/demo/<simulation-name>/<starting-time-step>/<simulation-speed>`。请注意，`<simulation-name>` 和 `<starting-time-step>` 表示与上面提到的相同内容。`<simulation-speed>` 可以设置为控制演示速度，其中 1 是最慢的，5 是最快的。例如，访问以下链接将启动一个预模拟示例，从时间步 1 开始，具有中等演示速度：  
[http://localhost:8000/demo/July1_the_ville_isabella_maria_klaus-step-3-20/1/3/](http://localhost:8000/demo/July1_the_ville_isabella_maria_klaus-step-3-20/1/3/)

### 提示
我们注意到，当 OpenAI 的 API 达到每小时速率限制时可能会挂起。发生这种情况时，您可能需要重新启动模拟。目前，我们建议在进展过程中经常保存模拟，以确保在需要停止并重新运行它时尽可能少地丢失模拟。运行这些模拟，至少在 2023 年初，可能会有些昂贵，特别是当环境中有许多智能体时。

## <img src="https://joonsungpark.s3.amazonaws.com:443/static/assets/characters/profile/Maria_Lopez.png" alt="Generative Maria">   模拟存储位置
您保存的所有模拟将位于 `environment/frontend_server/storage`，所有压缩的演示将位于 `environment/frontend_server/compressed_storage`。

## <img src="https://joonsungpark.s3.amazonaws.com:443/static/assets/characters/profile/Sam_Moore.png" alt="Generative Sam">   自定义

有两种方法可以可选地自定义您的模拟。

### 编写和加载智能体历史
第一种是在模拟开始时使用唯一历史初始化智能体。为此，您需要 1) 使用其中一个基础模拟启动模拟，以及 2) 编写和加载智能体历史。更具体地说，以下是步骤：

#### 步骤 1. 启动基础模拟
仓库中包含两个基础模拟：包含 25 个智能体的 `base_the_ville_n25`，以及包含 3 个智能体的 `base_the_ville_isabella_maria_klaus`。按照上述步骤直到步骤 2 加载其中一个基础模拟。

#### 步骤 2. 加载历史文件
然后，当提示"Enter option: "（输入选项：）时，您应该通过响应以下命令来加载智能体历史：

    call -- load history the_ville/<history_file_name>.csv
请注意，您需要将 `<history_file_name>` 替换为现有历史文件的名称。仓库中包含两个历史文件作为示例：`base_the_ville_n25` 的 `agent_history_init_n25.csv` 和 `base_the_ville_isabella_maria_klaus` 的 `agent_history_init_n3.csv`。这些文件包含每个智能体的分号分隔的记忆记录列表——加载它们会将记忆记录插入到智能体的记忆流中。

#### 步骤 3. 进一步自定义
要通过编写自己的历史文件来自定义初始化，请将您的文件放在以下文件夹中：`environment/frontend_server/static_dirs/assets/the_ville`。您的自定义历史文件的列格式必须与包含的示例历史文件匹配。因此，我们建议通过复制和粘贴仓库中已有的文件来开始此过程。

### 创建新的基础模拟
对于更复杂的自定义，您需要编写自己的基础模拟文件。最直接的方法是复制和粘贴现有的基础模拟文件夹，根据您的要求重命名和编辑它。如果您决定保持智能体名称不变，此过程将更简单。但是，如果您希望更改它们的名称或增加 Smallville 地图可以容纳的智能体数量，您可能需要使用 [Tiled](https://www.mapeditor.org/) 地图编辑器直接编辑地图。


## <img src="https://joonsungpark.s3.amazonaws.com:443/static/assets/characters/profile/Eddy_Lin.png" alt="Generative Eddy">   作者和引用

**作者：** Joon Sung Park, Joseph C. O'Brien, Carrie J. Cai, Meredith Ringel Morris, Percy Liang, Michael S. Bernstein

如果您使用本仓库中的代码或数据，请引用我们的论文。
```
@inproceedings{Park2023GenerativeAgents,  
author = {Park, Joon Sung and O'Brien, Joseph C. and Cai, Carrie J. and Morris, Meredith Ringel and Liang, Percy and Bernstein, Michael S.},  
title = {Generative Agents: Interactive Simulacra of Human Behavior},  
year = {2023},  
publisher = {Association for Computing Machinery},  
address = {New York, NY, USA},  
booktitle = {In the 36th Annual ACM Symposium on User Interface Software and Technology (UIST '23)},  
keywords = {Human-AI interaction, agents, generative AI, large language models},  
location = {San Francisco, CA, USA},  
series = {UIST '23}
}
```

## <img src="https://joonsungpark.s3.amazonaws.com:443/static/assets/characters/profile/Wolfgang_Schulz.png" alt="Generative Wolfgang">   致谢

我们鼓励您支持以下三位为该项目设计游戏资源的优秀艺术家，特别是如果您计划将此处包含的资源用于您自己的项目：
* 背景艺术： [PixyMoon (@_PixyMoon\_)](https://twitter.com/_PixyMoon_)
* 家具/室内设计： [LimeZu (@lime_px)](https://twitter.com/lime_px)
* 角色设计： [ぴぽ (@pipohi)](https://twitter.com/pipohi)

此外，我们感谢 Lindsay Popowski、Philip Guo、Michael Terry 以及行为科学高级研究中心（CASBS）社区的见解、讨论和支持。最后，Smallville 中出现的所有地点都受到 Joon 作为本科生和研究生经常光顾的真实世界地点的启发——他感谢那里的每个人在这些年里一直喂养和支持他。

