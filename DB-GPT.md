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
### 启动 DB-GPT

- 创建软链接

系统盘空间有限，建议对 `/root/autodl-tmp/models/*` 创建软链接

```sh
ln -s /root/autodl-tmp/models/* /root/DB-GPT/models/
```

- 准备模型数据

#### 默认模型

我们当前使用的镜像`csunny/DB-GPT/autodl_DB-GPT`，在`~/DB-GPT/models`目录，存放了默认的模型数据，分别是：

```sh
chatglm2-6b
text2vec-large-chinese
```

![image](https://github.com/sk142857/opendocs/assets/75599950/0f3b2893-5309-45de-bd08-da0903964a63)

为了快速体验`GB-GPT`，我们直接使用默认的模型即可。

#### 自定义模型

目前支持的模型有：

![image](https://github.com/sk142857/opendocs/assets/75599950/c2abc745-f4e4-4360-9e4c-5a4b6af494bc)

小编这里使用`baichuan-inc/Baichuan-13B-Chat`模型：

![image](https://github.com/sk142857/opendocs/assets/75599950/ce9b7c09-d46b-4fc7-a0d6-7101216d0543)

下载方式一（使用LFS）：

```sh
# 操作简单，但是有可能速度较慢
git lfs clone https://huggingface.co/baichuan-inc/Baichuan-13B-Chat
```

下载方式二（手动下载）：

```sh
# 如果 git lfs 下载速度太慢，可以考虑手动下载。注意设置学术资源加速！

# config
wget https://huggingface.co/baichuan-inc/Baichuan2-13B-Chat/resolve/main/config.json
wget https://huggingface.co/baichuan-inc/Baichuan2-13B-Chat/resolve/main/configuration_baichuan.py
wget https://huggingface.co/baichuan-inc/Baichuan2-13B-Chat/resolve/main/generation_config.json
wget https://huggingface.co/baichuan-inc/Baichuan2-13B-Chat/resolve/main/generation_utils.py
wget https://huggingface.co/baichuan-inc/Baichuan2-13B-Chat/resolve/main/handler.py
wget https://huggingface.co/baichuan-inc/Baichuan2-13B-Chat/resolve/main/modeling_baichuan.py
wget https://huggingface.co/baichuan-inc/Baichuan2-13B-Chat/resolve/main/pytorch_model.bin.index.json
wget https://huggingface.co/baichuan-inc/Baichuan2-13B-Chat/resolve/main/quantizer.py
wget https://huggingface.co/baichuan-inc/Baichuan2-13B-Chat/resolve/main/special_tokens_map.json
wget https://huggingface.co/baichuan-inc/Baichuan2-13B-Chat/resolve/main/tokenization_baichuan.py
wget https://huggingface.co/baichuan-inc/Baichuan2-13B-Chat/resolve/main/tokenizer.model
wget https://huggingface.co/baichuan-inc/Baichuan2-13B-Chat/resolve/main/tokenizer_config.json

# models
wget https://huggingface.co/baichuan-inc/Baichuan-13B-Chat/resolve/main/pytorch_model-00001-of-00003.bin
wget https://huggingface.co/baichuan-inc/Baichuan-13B-Chat/resolve/main/pytorch_model-00002-of-00003.bin
wget https://huggingface.co/baichuan-inc/Baichuan-13B-Chat/resolve/main/pytorch_model-00003-of-00003.bin
```

- 激活虚拟环境

```sh
python>=3.10
conda create -n dbgpt_env python=3.10
conda activate dbgpt_env
pip install -e ".[default]"
```
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

![image](https://github.com/sk142857/opendocs/assets/75599950/3bb00972-db92-4a54-b5c9-3dfed078e058)

- 启动服务

```sh
python pilot/server/dbgpt_server.py  --port 6006
```
> `6006`为`Autodl`提供用于自定义服务访问的端口，具体可以根据不同的平台切换

```sh
2023-10-13 11:48:27 | INFO | stdout |  
2023-10-13 11:48:27 | INFO | stdout | Plugins found: 2
2023-10-13 11:48:27 | INFO | stdout | --------------------
2023-10-13 11:48:27 | INFO | LOGGER | 
Plugins found: 2
--------------------
2023-10-13 11:48:27 | INFO | stdout |   DB-GPT-DASHBOARD-Plugin: 0.1.0 - This is an DbGPT plugin to generate data analysis charts.
2023-10-13 11:48:27 | INFO | LOGGER | DB-GPT-DASHBOARD-Plugin: 0.1.0 - This is an DbGPT plugin to generate data analysis charts.
2023-10-13 11:48:27 | INFO | stdout |   DB-GPT-SQL-Execution-Plugin: 0.1.0 - This is an DbGPT plugin to connect Generic Db And Execute SQL.
2023-10-13 11:48:27 | INFO | LOGGER | DB-GPT-SQL-Execution-Plugin: 0.1.0 - This is an DbGPT plugin to connect Generic Db And Execute SQL.
Loading checkpoint shards:  29%|████████████████████▎                                                  | 2/7 [00:04<00:10,  2.13s/it]
Loading checkpoint shards:  43%|██████████████████████████████▍                                        | 3/7 [00:06<00:08,  2.05s/it]
Loading checkpoint shards:  57%|████████████████████████████████████████▌                              | 4/7 [00:07<00:05,  1.88s/it]
Loading checkpoint shards:  71%|██████████████████████████████████████████████████▋                    | 5/7 [00:09<00:03,  1.74s/it]
Loading checkpoint shards:  86%|████████████████████████████████████████████████████████████▊          | 6/7 [00:10<00:01,  1.68s/it]
Loading checkpoint shards: 100%|███████████████████████████████████████████████████████████████████████| 7/7 [00:11<00:00,  1.43s/it]
Loading checkpoint shards: 100%|███████████████████████████████████████████████████████████████████████| 7/7 [00:11<00:00,  1.69s/it]
2023-10-13 11:48:35 | ERROR | stderr | 
2023-10-13 11:48:39 | INFO | stdout | LLM Model Loading Success！
2023-10-13 11:48:39 | ERROR | stderr | INFO:     Started server process [1585]
2023-10-13 11:48:39 | ERROR | stderr | INFO:     Waiting for application startup.
2023-10-13 11:48:39 | ERROR | stderr | INFO:     Application startup complete.
2023-10-13 11:48:39 | ERROR | stderr | INFO:     Uvicorn running on http://0.0.0.0:6006 (Press CTRL+C to quit)
```
### 暴露服务

回到`Autodl`容器实例列表，选中【服务所在实例】，点击【自定义服务】，即可获得对外访问地址。

![image](https://github.com/sk142857/opendocs/assets/75599950/a6118915-764c-4174-b72c-7da982880d32)

类似：https://u124467-****.westa.seetacloud.com:8443/

![image](https://github.com/sk142857/opendocs/assets/75599950/3585c462-6965-4597-91ae-3d2548eea3e0)

### 附录一 数据库连接

如果启动服务的时候，提示`MySql`连接错误，那么就需要安装对应的`MySql`数据库。具体的配置信息，请参考`.env`。

```sh
docker run --name=mysql -p 3306:3306 -e MYSQL_ROOT_PASSWORD=aa12345678 -dit mysql:latest
```
### 附录二 官方镜像

区别没有什么，就是需要手动配置一些信息以及下载镜像文件等。

参考文档：https://db-gpt.readthedocs.io/en/latest/getting_started/install/deploy/deploy.html

#### 安装

- 基础环境

```sh
git clone https://github.com/eosphoros-ai/DB-GPT.git
```

```sh
python>=3.10
conda create -n dbgpt_env python=3.10
conda activate dbgpt_env
pip install -e ".[default]"
```
- 下载text2vec-large-chinese

![image](https://github.com/sk142857/opendocs/assets/75599950/69c6cb26-41d9-413c-81d1-bc83f54382ac)

```sh
#  config
git clone https://huggingface.co/GanymedeNil/text2vec-large-chinese

# model.safetensors
wget https://huggingface.co/GanymedeNil/text2vec-large-chinese/resolve/main/model.safetensors

# pytorch_model.bin
wget https://huggingface.co/GanymedeNil/text2vec-large-chinese/resolve/main/pytorch_model.bin
```
- 拷贝env配置文件

```sh
cp .env.template .env
```
- 切换模型

切换模型，只需要修改环境变量`.env`文件，但是要注意对应关系。

```sh
LLM_MODEL_CONFIG = {
    "flan-t5-base": os.path.join(MODEL_PATH, "flan-t5-base"),
    "vicuna-13b": os.path.join(MODEL_PATH, "vicuna-13b"),
    "vicuna-7b": os.path.join(MODEL_PATH, "vicuna-7b"),
    # (Llama2 based) see https://huggingface.co/lmsys/vicuna-13b-v1.5
    "vicuna-13b-v1.5": os.path.join(MODEL_PATH, "vicuna-13b-v1.5"),
    "vicuna-7b-v1.5": os.path.join(MODEL_PATH, "vicuna-7b-v1.5"),
    "codegen2-1b": os.path.join(MODEL_PATH, "codegen2-1B"),
    "codet5p-2b": os.path.join(MODEL_PATH, "codet5p-2b"),
    "chatglm-6b-int4": os.path.join(MODEL_PATH, "chatglm-6b-int4"),
    "chatglm-6b": os.path.join(MODEL_PATH, "chatglm-6b"),
    "chatglm2-6b": os.path.join(MODEL_PATH, "chatglm2-6b"),
    "chatglm2-6b-int4": os.path.join(MODEL_PATH, "chatglm2-6b-int4"),
    "guanaco-33b-merged": os.path.join(MODEL_PATH, "guanaco-33b-merged"),
    "falcon-40b": os.path.join(MODEL_PATH, "falcon-40b"),
    "gorilla-7b": os.path.join(MODEL_PATH, "gorilla-7b"),
    "gptj-6b": os.path.join(MODEL_PATH, "ggml-gpt4all-j-v1.3-groovy.bin"),
    "proxyllm": "chatgpt_proxyllm",
    "chatgpt_proxyllm": "chatgpt_proxyllm",
    "bard_proxyllm": "bard_proxyllm",
    "claude_proxyllm": "claude_proxyllm",
    "wenxin_proxyllm": "wenxin_proxyllm",
    "tongyi_proxyllm": "tongyi_proxyllm",
    "zhipu_proxyllm": "zhipu_proxyllm",
    "llama-2-7b": os.path.join(MODEL_PATH, "Llama-2-7b-chat-hf"),
    "llama-2-13b": os.path.join(MODEL_PATH, "Llama-2-13b-chat-hf"),
    "llama-2-70b": os.path.join(MODEL_PATH, "Llama-2-70b-chat-hf"),
    "baichuan-13b": os.path.join(MODEL_PATH, "Baichuan-13B-Chat"),
    # please rename "fireballoon/baichuan-vicuna-chinese-7b" to "baichuan-7b"
    "baichuan-7b": os.path.join(MODEL_PATH, "baichuan-7b"),
    "baichuan2-7b": os.path.join(MODEL_PATH, "Baichuan2-7B-Chat"),
    "baichuan2-13b": os.path.join(MODEL_PATH, "Baichuan2-13B-Chat"),
    # (Llama2 based) We only support WizardLM-13B-V1.2 for now, which is trained from Llama-2 13b, see https://huggingface.co/WizardLM/WizardLM-13B-V1.2
    "wizardlm-13b": os.path.join(MODEL_PATH, "WizardLM-13B-V1.2"),
    # wget https://huggingface.co/TheBloke/vicuna-13B-v1.5-GGUF/resolve/main/vicuna-13b-v1.5.Q4_K_M.gguf -O models/ggml-model-q4_0.gguf
    "llama-cpp": os.path.join(MODEL_PATH, "ggml-model-q4_0.gguf"),
    # https://huggingface.co/internlm/internlm-chat-7b-v1_1, 7b vs 7b-v1.1: https://github.com/InternLM/InternLM/issues/288
    "internlm-7b": os.path.join(MODEL_PATH, "internlm-chat-7b"),
    "internlm-7b-8k": os.path.join(MODEL_PATH, "internlm-chat-7b-8k"),
    "internlm-20b": os.path.join(MODEL_PATH, "internlm-20b-chat"),
    # For test now
    "opt-125m": os.path.join(MODEL_PATH, "opt-125m"),
}
```

- 启动服务
