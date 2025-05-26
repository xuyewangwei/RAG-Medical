# base
<https://github.com/chatchat-space/Langchain-Chatchat/tree/master>

# 快速上手

## 知识库配置
```bash
git clone https://github.com/chatchat-space/Langchain-Chatchat.git
```

```bash
pip install langchain-chatchat -U
```

在根目录下运行,生成需要的文件
```
chatchat init
```

根目录下多出了很多yaml文件
* basic_settings.yaml
* prompt_settings.yaml
* tool_settings.yaml
* kb_settings.yaml
* model_settings.yaml

主要更改model_setting.yaml:
```
# 默认选用的 LLM 名称
 DEFAULT_LLM_MODEL: qwen1.5-chat

 # 默认选用的 Embedding 名称
 DEFAULT_EMBEDDING_MODEL: bge-large-zh-v1.5

# 将 `LLM_MODEL_CONFIG` 中 `llm_model, action_model` 的键改成对应的 LLM 模型
# 在 `MODEL_PLATFORMS` 中修改对应模型平台信息
```

这里更改为：
```
# 默认选用的 LLM 名称
DEFAULT_LLM_MODEL: qwen1.5-chat

# 默认选用的 Embedding 名称
DEFAULT_EMBEDDING_MODEL: bge-large-zh-v1.5
```


以及更改配置知识库路径basic_settings.yaml
```
# 知识库默认存储路径
 KB_ROOT_PATH: D:\chatchat-test\data\knowledge_base

 # 数据库默认存储路径。如果使用sqlite，可以直接修改DB_ROOT_PATH；如果使用其它数据库，请直接修改SQLALCHEMY_DATABASE_URI。
 DB_ROOT_PATH: D:\chatchat-test\data\knowledge_base\info.db

 # 知识库信息数据库连接URI
 SQLALCHEMY_DATABASE_URI: sqlite:///D:\chatchat-test\data\knowledge_base\info.db
```

这里更改为：
```
# 知识库默认存储路径
KB_ROOT_PATH: /root/autodl-tmp/langchain/Langchain-Chatchat/data/knowledge_base

# 数据库默认存储路径。如果使用sqlite，可以直接修改DB_ROOT_PATH；如果使用其它数据库，请直接修改SQLALCHEMY_DATABASE_URI。
DB_ROOT_PATH: /root/autodl-tmp/langchain/Langchain-Chatchat/data/knowledge_base/info.db

# 知识库信息数据库连接URI
SQLALCHEMY_DATABASE_URI: 
  sqlite:////root/autodl-tmp/langchain/Langchain-Chatchat/data/knowledge_base/info.db
```

## 准备llm模型与embedding模型
仔细观察，发现0.3.x版本这些模型需要用户自行启动模型部署框架后，通过修改配置信息接入项目（哈哈）
进行向量化的时候出现错误：可以看到尝试连接127.0.0.1:11434失败，因为我们根本没有开启服务
```
2025-05-26 14:50:20.892 | ERROR    | chatchat.server.knowledge_base.kb_cache.faiss_cache:load_vector_store:140 - Error raised by inference endpoint: HTTPConnectionPool(host='127.0.0.1', port=11434): Max retries exceeded with url: /api/embeddings (Caused by NewConnectionError('<urllib3.connection.HTTPConnection object at 0x7fcc1aaaec80>: Failed to establish a new connection: [Errno 111] Connection refused'))
2025-05-26 14:50:20.892 | ERROR    | chatchat.init_database:worker:61 - 向量库 samples 加载失败。
```

我们需要使用本地框架启动服务，并介入项目，有如下本地框架：
* Xinference
* LocalAI
* Ollama
* FastChat
我们使用Ollama框架

### ollama框架的使用
下载ollama:
```
curl -fsSL https://aliendao.cn/ollama/install.sh | sh
```

有一些ollama常用命令：
```
# 查看当前有哪些模型
ollama list

# 查看ollama执行
ollama

ollama serve 		# 启动ollama 
ollama create 	# 从模型文件创建模型 
ollama show 		# 显示模型信息 
ollama run 			# 运行模型，会先自动下载模型 
ollama pull 		# 从注册仓库中拉取模型 
ollama push 		# 将模型推送到注册仓库 
ollama list 		# 列出已下载模型 
ollama ps 			# 列出正在运行的模型 
ollama cp 			# 复制模型 
ollama rm 			# 删除模型
```

我们使用qwen2:7b
在ollama上找，并运行:qwen2:7b

使用bge-large-zh-v1.5
在ollama上运行: quentinz/bge-large-zh-v1.5:latest

```
ollama run <xxx>
```

*更改ollama模型存储路径*
创建文件夹后
```
sudo chown -R root:root /path/to/ollama/models
sudo chmod -R 777 /path/to/ollama/models
```

```
sudo vi /etc/systemd/system/ollama.service
```

```
Environment="PATH=/root/miniconda3/bin:/root/miniconda3/condabin:/root/miniconda3/bin:/usr/local/bin:/usr/local/nvidia/bin:/usr/local/cuda/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
Environment="/root/autodl-tmp/ollama/models" #添加模型路径


[Install]
WantedBy=default.target
```

## 初始化知识库

* 在初始化之前确保你已经使用ollama下载了需要的model和embedding

使用一下命令进行知识库初始化
```bash
chatchat kb -r
```

```
2025-05-26 16:17:44.549 | INFO     | chatchat.server.knowledge_base.kb_cache.faiss_cache:save:40 - 已将向量库 ('samples', 'quentinz/bge-large-zh-v1.5') 保存到磁盘

----------------------------------------------------------------------------------------------------
知识库名称      ：samples
知识库类型      ：faiss
向量模型：      ：quentinz/bge-large-zh-v1.5
知识库路径      ：/root/autodl-tmp/langchain/Langchain-Chatchat/data/knowledge_base/samples
文件总数量      ：12
入库文件数      ：12
知识条目数      ：755
用时            ：0:02:22.850388
----------------------------------------------------------------------------------------------------

总计用时        ：0:02:22.865635
```


这样就构建了向量知识库

# 运行程序
```
chatchat start -a
```

#在本地上连接服务器中的地址

<https://blog.csdn.net/TYUT_xiaoming/article/details/135935184>






