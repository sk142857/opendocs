### 创建实例

- 配置
  
❗建议选择`4090`，低配机型运行的时候，会有很多莫名其妙的错误，影响测试，`4090`亲测可用。本文档介绍使用`Autodl`部署DB-GPT。

❗本实例中，`Autodl`默认提供`50GB`的数据盘，但是不够用，需要扩容，最低扩容后的数据盘为`65GB+`，请根据实际情况调整。

- 参数

| 参数 | 配置 |
| - | - |
| GPU型号 | RTX 4090 * 1卡 |
| CPU | 16 vCPU AMD EPYC 9654 96-Core Processor * 12核心 |
| 内存 | 90GB |
| 系统盘 | 30GB |
| 数据盘 | 免费50GB + 扩容 15GB|

- 实例截图

![image](https://github.com/sk142857/opendocs/assets/75599950/b627531b-ef36-4df3-add5-46479b484445)

- 镜像选择

选择社区提供的镜像`csunny/DB-GPT/autodl_DB-GPT`。

镜像地址：https://www.codewithgpu.com/i/csunny/DB-GPT/autodl_DB-GPT

![image](https://github.com/sk142857/opendocs/assets/75599950/4d80eeba-1b4f-4771-af15-7898011922e9)

- 启动实例

第一次启动实例，因为要拉取镜像，速度可能比较慢，请耐心等候。

![image](https://github.com/sk142857/opendocs/assets/75599950/2b8d3641-bed0-4dad-8ba4-b893bc030c9b)

这里可以看到，我们刚才选择的机器配置。如果创建实例的时候，忘记扩容数据盘，也不用担心。可以，在实例关机的情况下，选择扩容。

![image](https://github.com/sk142857/opendocs/assets/75599950/9600c147-a3b4-490c-b8c3-9f6bd63fb895)

![image](https://github.com/sk142857/opendocs/assets/75599950/969c5534-3ae3-4373-b1f4-cc04ccc3209c)

- 进入实例

实例启动以后，在【快捷工具】一栏，选择【JupyterLab】，点击进入实例管理界面。其他功能菜单，后续会介绍，这里略过。

![image](https://github.com/sk142857/opendocs/assets/75599950/b21b6e48-ad00-40cb-a22a-3d9f2654017c)

- JupyterLab 管理界面

![image](https://github.com/sk142857/opendocs/assets/75599950/83bfc010-8181-48c2-bdbe-7953785f4130)

实例目录以及相关配置说明如下：

```sh
+--------------------------------------------------AutoDL--------------------------------------------------------+
目录说明:
╔═════════════════╦════════╦════╦═════════════════════════════════════════════════════════════════════════╗
║目录             ║名称    ║速度║说明                                                                     ║
╠═════════════════╬════════╬════╬═════════════════════════════════════════════════════════════════════════╣
║/                ║系 统 盘║一般║实例关机数据不会丢失，可存放代码等。会随保存镜像一起保存。               ║
║/root/autodl-tmp ║数 据 盘║ 快 ║实例关机数据不会丢失，可存放读写IO要求高的数据。但不会随保存镜像一起保存 ║
║/root/autodl-nas ║网    盘║ 慢 ║可以实现多实例间的文件同步共享，不受实例开关机和保存镜像的影响。         ║
║/root/autodl-fs  ║文件存储║一般║可以实现多实例间的文件同步共享，不受实例开关机和保存镜像的影响。         ║
╚═════════════════╩════════╩════╩═════════════════════════════════════════════════════════════════════════╝
CPU ：16 核心
内存：60 GB
GPU ：NVIDIA GeForce RTX 4090, 1
存储：
  系 统 盘/               ：70% 21G/30G
  数 据 盘/root/autodl-tmp：1% 8.0K/65G
  网    盘/root/autodl-nas：未挂载
  文件存储/root/autodl-fs ：未挂载
+----------------------------------------------------------------------------------------------------------------+
```
### 启动 GB-GPT

- 注意目录调整

查看目录大小占用发现，`DB-GPT`已经占用系统盘`70%`的空间，`Autodl`系统盘默认只有30个G，而且不支持扩容。所以，建议把`DB-GPT`目录，移动到`/root/autodl-tmp`目录。

- 准备模型数据

我们当前使用的镜像`csunny/DB-GPT/autodl_DB-GPT`，在`~/DB-GPT/models`目录，存放了默认的模型数据，分别是：

```sh
chatglm2-6b
text2vec-large-chinese
```

![image](https://github.com/sk142857/opendocs/assets/75599950/0f3b2893-5309-45de-bd08-da0903964a63)

为了快速体验`GB-GPT`，我们直接使用默认的模型即可。

- 激活虚拟环境

```sh
# activate dbgpt
conda activate dbgpt
```
```sh
# 显示dbgpt，激活成功
dbgpt
```
- 查看虚拟环境列表

```sh
conda env list
```
```sh
# conda environments:
#
base                     /root/miniconda3
dbgpt                 *  /root/miniconda3/envs/dbgpt
```
- 查看配置信息

本实例中，按照系统生成的默认配置即可，不需要修改。如果，需要替换模型等，修改`.env`文件即可。这一部分，会有专门的文章介绍。

```sh
vi /root/DB-GPT/.env
```
```sh
#*******************************************************************#
#**             DB-GPT  - GENERAL SETTINGS                        **#  
#*******************************************************************#
## DISABLED_COMMAND_CATEGORIES - The list of categories of commands that are disabled. Each of the below are an option:
## pilot.commands.query_execute

## For example, to disable coding related features, uncomment the next line
# DISABLED_COMMAND_CATEGORIES=   

#*******************************************************************#
#**                        Webserver Port                         **#
#*******************************************************************#
WEB_SERVER_PORT=6006

#*******************************************************************#
#***                       LLM PROVIDER                          ***#
#*******************************************************************#

# TEMPERATURE=0

#*******************************************************************#
#**                         LLM MODELS                            **#
#*******************************************************************#
# LLM_MODEL, see /pilot/configs/model_config.LLM_MODEL_CONFIG
LLM_MODEL=chatglm2-6b
#vicuna-13b
MODEL_SERVER=http://127.0.0.1:8000
LIMIT_MODEL_CONCURRENCY=5
MAX_POSITION_EMBEDDINGS=4096
QUANTIZE_QLORA=True
## SMART_LLM_MODEL - Smart language model (Default: vicuna-13b)
## FAST_LLM_MODEL - Fast language model (Default: chatglm-6b)
# SMART_LLM_MODEL=vicuna-13b
# FAST_LLM_MODEL=chatglm-6b


#*******************************************************************#
#**                         EMBEDDING SETTINGS                    **#
#*******************************************************************#
EMBEDDING_MODEL=text2vec
KNOWLEDGE_CHUNK_SIZE=500
KNOWLEDGE_SEARCH_TOP_SIZE=5
## EMBEDDING_TOKENIZER   - Tokenizer to use for chunking large inputs
## EMBEDDING_TOKEN_LIMIT - Chunk size limit for large inputs
# EMBEDDING_MODEL=all-MiniLM-L6-v2
# EMBEDDING_TOKENIZER=all-MiniLM-L6-v2
# EMBEDDING_TOKEN_LIMIT=8191


#*******************************************************************#
#**                  DATABASE SETTINGS                            **#
#*******************************************************************#
LOCAL_DB_USER=root
LOCAL_DB_PASSWORD=aa12345678
LOCAL_DB_HOST=127.0.0.1
LOCAL_DB_PORT=3306


### MILVUS
## MILVUS_ADDR - Milvus remote address (e.g. localhost:19530)
## MILVUS_USERNAME - username for your Milvus database
## MILVUS_PASSWORD - password for your Milvus database
## MILVUS_SECURE - True to enable TLS. (Default: False)
##   Setting MILVUS_ADDR to a `https://` URL will override this setting.
## MILVUS_COLLECTION - Milvus collection, change it if you want to start a new memory and retain the old memory.
# MILVUS_ADDR=localhost:19530
# MILVUS_USERNAME=
# MILVUS_PASSWORD=
# MILVUS_SECURE=
# MILVUS_COLLECTION=dbgpt

#*******************************************************************#
#**                         COMMANDS                              **#
#*******************************************************************#
EXECUTE_LOCAL_COMMANDS=False



#*******************************************************************#
#**                  ALLOWLISTED PLUGINS                          **#
#*******************************************************************#

#ALLOWLISTED_PLUGINS - Sets the listed plugins that are allowed (Example: plugin1,plugin2,plugin3)
#DENYLISTED_PLUGINS - Sets the listed plugins that are not allowed (Example: plugin1,plugin2,plugin3)
ALLOWLISTED_PLUGINS=
DENYLISTED_PLUGINS=


#*******************************************************************#
#**                 CHAT PLUGIN SETTINGS                          **#
#*******************************************************************#
# CHAT_MESSAGES_ENABLED - Enable chat messages (Default: False)
# CHAT_MESSAGES_ENABLED=False


#*******************************************************************#
#**                  VECTOR STORE SETTINGS                       **#
#*******************************************************************#
VECTOR_STORE_TYPE=Chroma
#MILVUS_URL=127.0.0.1
#MILVUS_PORT=19530
#MILVUS_USERNAME
#MILVUS_PASSWORD
#MILVUS_SECURE=


#*******************************************************************#
#**                  WebServer Language Support                   **#
#*******************************************************************#
LANGUAGE=en
#LANGUAGE=zh


#*******************************************************************#
# **    PROXY_SERVER (openai interface | chatGPT proxy service), use chatGPT as your LLM.
# ** if your server can visit openai, please set PROXY_SERVER_URL=https://api.openai.com/v1/chat/completions
# ** else if you have a chatgpt proxy server, you can set PROXY_SERVER_URL={your-proxy-serverip:port/xxx}
#*******************************************************************#
PROXY_API_KEY={your-openai-sk}
PROXY_SERVER_URL=https://api.openai.com/v1/chat/completions


#*******************************************************************#
# **    SUMMARY_CONFIG
#*******************************************************************#
SUMMARY_CONFIG=FAST
```
> Conda 是一个开源的软件包管理系统和环境管理系统，用于安装多个版本的软件包及其依赖关系，并在它们之间轻松切换。

- 安装依赖

#### 方式一：使用Autodl默认学术资源加速

```sh
source /etc/network_turbo
```
然后执行

```sh
pip install -r requirements.txt
```
> 官方默认使用的是阿里云加速，实际使用起来，相比之下，速度并不是很快

`Autodl`学术资源加速参考：https://www.autodl.com/docs/network_turbo/

#### 方式二：使用中科大USTC资源加速（推荐）

```sh
pip install -r requirements.txt -i https://pypi.mirrors.ustc.edu.cn/simple/
```
> 速度极快，建议设置为默认加速

- 启动服务

```sh
python pilot/server/dbgpt_server.py  --port 6006
```
> `6006`为`Autodl`提供用于自定义服务访问的端口，具体可以根据不同的平台切换


