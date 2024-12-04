# LlamaFeeder

## 使用步骤

### 安装

```bash
git clone https://github.com/the-seeds/LlamaFeeder.git
cd LlamaFeeder
pip install -r requirements.txt
```

### 配置文件

配置文件示例：

```yaml
# config.yaml
openai:
  model: ""      # OpenAI 模型名称
  base_url: ""   # API 基础URL
  api_key: ""    # OpenAI API密钥

save_dir: "./example"                # 生成数据集保存目录
file_path: "./example/Olympics.txt" # 文件源地址
main_theme: "巴黎奥运会"              # 文本主题
concurrent_api_requests_num: 4       # api异步请求数
method: "genQA"                      # 数据生成方式
file_folder: "../../LLaMA-Factory-Doc" # 文件夹路径
file_type: rst                         # 文件类型
```

> [!NOTE]
> 闭源模型推荐使用 [One API](https://github.com/songquanpeng/one-api) 接入。
> 
> 开源模型推荐使用 [imitater](https://github.com/the-seeds/imitater) 接入。

### 参数说明

| 参数名                      | 参数介绍                                   | 默认值                   |
| --------------------------- | ------------------------------------------ | ------------------------ |
| openai.model                | API 模型名称                                | \                      |
| openai.base_url             | API URL地址                                 | \                      |
| openai.api_key              | API密钥                                    | \                      |
| save_dir                    | 生成的数据集保存目录                       | "./example"              |
| file_path                   | 源文件路径，用于单文件处理                 | \ |
| file_folder               |文件夹路径，用于批量处理多个文件| \           |
| file_type       |  要处理的文件类型|txt|
| main_theme                  | 文本主题，用于生成相关问题                 | \                |
| concurrent_api_requests_num | API并发请求数量       | 4                        |
| method                      | 数据生成方式 | "genQA" |
| save_file_name    | 文件保存名，如'dataset.json'|"dataset.json"|
| is_structure_data | 是否是结构化 json 数据，是则按照text_template读取文本，否则直接读入纯文本|False|
| text_template     | 从 json 格式构造生成问题所需文本的模板。如"【标题】: {msg_title}\n【来源】: {msg_source}\n【时间】: {msg_date}\n【内容】: {msg_context}\n"| \ |

> 输入纯文本时请设置 is_structure_data 为 False。
>
> 设置 is_structure_data 为 True 时请确保数据包含 text_template 中对应的键。
>

### 数据格式

生成数据默认以 `alpaca` 格式保存，如：

```json
{
    "instruction": "中国的首都是哪里？",
    "input": "",
    "output": "中国的首都是北京。"
}
```

### 使用方式

#### 命令行

##### 单文件处理

在 `config.yaml` 中配置 `file_path` 指向单个文件，运行工具生成数据集。

```yaml
file_path: "./example/Olympics.txt"
main_theme: "巴黎奥运会"
save_dir: "dataset/"
save_file_name: "test.json"
# 文件将会被保存在 dataset/test.json中
```

-----

##### 多文件处理

如果需要批量处理多个文件，可以指定 `file_folder` 和 `file_type`。

```yaml
file_folder: "./example_docs"
file_type: "rst txt md" # 意味着rst,txt,md文件会被读取
```

-----

##### 文本读取

`file_path` 和 `file_folder` 中的文本将被用于数据生成。若不指定 `is_structure_data` 与 `text_template` ，工具会默认以纯文本方式读取文件内容（包括 JSON 文件）。 

若指定`is_structure_data` 与 `text_template`  工具会根据模板解析文件内容读取文本。

以下是一个示例：

```yaml
is_structure_data: true
text_template: "【标题】: {msg_title}\n【来源】: {msg_source}\n【时间】: {msg_date}\n【内容】: {msg_context}\n"
```

假设文件内容为以下 json 格式数据：

```json
{
  "msg_date": "2024-01-01",
  "msg_title": "会议通知",
  "msg_source":"C先生",
  "msg_context": "今天上午11:00在A楼B会议室召开会议，请准时到达。"
}
```

根据 `text_template` ，工具会将 json 格式数据解析为以下文本：

```
【标题】: 会议通知
【来源】: C先生
【时间】: 2024-01-01
【内容】: 今天上午11:00在A楼B会议室召开会议，请准时到达。
```

##### 项目运行

项目运行只需在 `src` 文件夹下执行命令：

```bash
python main.py config.yaml
```

#### WebUI

##### 启动

WebUI 通过在 `src` 目录下运行以下命令启动：

```bash
python webui.py
```

若点击所提供 url（通常是:http://127.0.0.1:7860 ）后出现以下界面则说明启动成功。

<img src="assets/image-20241129181253778.png" alt="image-20241129181253778" style="zoom:50%;" />

##### 使用

若您已配置好配置文件，可直接输入配置文件路径载入并进行修改。此外您也可以直接在 WebUI 界面直接进行文件配置。

![image-20241129181618047](assets/image-20241129181618047.png)

配置完成后，点击 `Run` 按钮便可开始生成数据。
