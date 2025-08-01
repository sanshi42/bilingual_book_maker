# bilingual_book_maker

bilingual_book_maker 是一个 AI 翻译工具，使用 ChatGPT 帮助用户制作多语言版本的 epub/txt/srt 文件和图书。该工具仅适用于翻译进入公共版权领域的 epub/txt 图书，不适用于有版权的书籍。请在使用之前阅读项目的 **[免责声明](./disclaimer.md)**。

![image](https://user-images.githubusercontent.com/15976103/222317531-a05317c5-4eee-49de-95cd-04063d9539d9.png)

## 准备

1. ChatGPT or OpenAI token [^token]
2. epub/txt books
3. 能正常联网的环境或 proxy
4. python3.8+

## 快速开始

本地放了一个 `test_books/animal_farm.epub` 给大家测试

```shell
pip install -r requirements.txt
python3 make_book.py --book_name test_books/animal_farm.epub --openai_key ${openai_key} --test
或
pip install -U bbook_maker
bbook --book_name test_books/animal_farm.epub --openai_key ${openai_key} --test
```

## 翻译服务

- 使用 `--openai_key` 指定 OpenAI API key，如果有多个可以用英文逗号分隔(xxx,xxx,xxx)，可以减少接口调用次数限制带来的错误。
  或者，指定环境变量 `BBM_OPENAI_API_KEY` 来略过这个选项。
- 默认用了 [GPT-3.5-turbo](https://openai.com/blog/introducing-chatgpt-and-whisper-apis) 模型，也就是 ChatGPT 正在使用的模型。

* DeepL

  使用 DeepL 封装的 api 进行翻译，需要付费。[DeepL Translator](https://rapidapi.com/splintPRO/api/dpl-translator) 来获得 token

  ```shell
  python3 make_book.py --book_name test_books/animal_farm.epub --model deepl --deepl_key ${deepl_key}
  ```

* DeepL free

  使用 DeepL free

  ```shell
  python3 make_book.py --book_name test_books/animal_farm.epub --model deeplfree
  ```

* Claude

  使用 [Claude](https://console.anthropic.com/docs) 模型进行翻译

  ```shell
  python3 make_book.py --book_name test_books/animal_farm.epub --model claude --claude_key ${claude_key}
  ```

* 谷歌翻译

  ```shell
  python3 make_book.py --book_name test_books/animal_farm.epub --model google
  ```

* 彩云小译

  ```shell
  python3 make_book.py --book_name test_books/animal_farm.epub --model caiyun --caiyun_key ${caiyun_key}
  ```

* Gemini

  ```shell
  python3 make_book.py --book_name test_books/animal_farm.epub --model gemini --gemini_key ${gemini_key}
  ```

* Qwen

  使用 [Qwen](https://www.aliyun.com/product/dashscope) 模型进行翻译，支持 qwen-mt-turbo 和 qwen-mt-plus 模型。

  使用 `--source_lang` 指定源语言，留空为自动检测。

  ```shell
  python3 make_book.py --book_name test_books/animal_farm.epub --qwen_key ${qwen_key} --model qwen-mt-turbo --language "Simplified Chinese"
  python3 make_book.py --book_name test_books/animal_farm.epub --qwen_key ${qwen_key} --model qwen-mt-plus --language "Japanese" --source_lang "English"
  ```

* 腾讯交互翻译

  ```shell
  python3 make_book.py --book_name test_books/animal_farm.epub --model tencentransmart
  ```

* [xAI](https://x.ai)

  ```shell
  python3 make_book.py --book_name test_books/animal_farm.epub --model xai --xai_key ${xai_key}
  ```

* [Ollama](https://github.com/ollama/ollama)

  使用 [Ollama](https://github.com/ollama/ollama) 自托管模型进行翻译。
  如果 ollama server 不运行在本地，使用 `--api_base http://x.x.x.x:port/v1` 指向 ollama server 地址

  ```shell
  python3 make_book.py --book_name test_books/animal_farm.epub --ollama_model ${ollama_model_name}
  ```

* [Groq](https://console.groq.com/keys)

  GroqCloud 当前支持的模型可以查看[Supported Models](https://console.groq.com/docs/models)

  ```shell
  python3 make_book.py --book_name test_books/animal_farm.epub --groq_key [your_key] --model groq --model_list llama3-8b-8192
  ```

## 使用说明

- 翻译完会生成一本 `{book_name}_bilingual.epub` 的双语书
- 如果出现了错误或使用 `CTRL+C` 中断命令，不想接下来继续翻译了，会生成一本 `{book_name}_bilingual_temp.epub` 的书，直接改成你想要的名字就可以了

## 参数说明

- `--test`:

  如果大家没付费可以加上这个先看看效果（有 limit 稍微有些慢）

- `--language`: 指定目标语言

  - 例如： `--language "Simplified Chinese"`，预设值为 `"Simplified Chinese"`.
  - 请阅读 helper message 来查找可用的目标语言： `python make_book.py --help`

- `--proxy`

  方便中国大陆的用户在本地测试时使用代理，传入类似 `http://127.0.0.1:7890` 的字符串

- `--resume`

  手动中断后，加入命令可以从之前中断的位置继续执行。

  ```shell
  python3 make_book.py --book_name test_books/animal_farm.epub --model google --resume
  ```

- `--translate-tags`

  指定需要翻译的标签，使用逗号分隔多个标签。epub 由 html 文件组成，默认情况下，只翻译 `<p>` 中的内容。例如: `--translate-tags h1,h2,h3,p,div`

- `--book_from`

  选项指定电子阅读器类型（现在只有 kobo 可用），并使用 `--device_path` 指定挂载点。

- `--api_base ${url}`

  如果你遇到了墙需要用 Cloudflare Workers 替换 api_base 请使用 `--api_base ${url}` 来替换。
  **请注意，此处你输入的 api 应该是'`https://xxxx/v1`'的字样，域名需要用引号包裹**

- `--allow_navigable_strings`

  如果你想要翻译电子书中的无标签字符串，可以使用 `--allow_navigable_strings` 参数，会将可遍历字符串加入翻译队列，**注意，在条件允许情况下，请寻找更规范的电子书**

- `--prompt`

  如果你想调整 prompt，你可以使用 `--prompt` 参数。有效的占位符包括 `{text}` 和 `{language}`。你可以用以下方式配置 prompt:

  - 如果您不需要设置 `system` 角色，可以这样：`--prompt "Translate {text} to {language}"` 或者 `--prompt prompt_template_sample.txt`（示例文本文件可以在 [./prompt_template_sample.txt](./prompt_template_sample.txt) 找到）。

  - 如果您需要设置 `system` 角色，可以使用以下方式配置：`--prompt '{"user":"Translate {text} to {language}", "system": "You are a professional translator."}'`，或者 `--prompt prompt_template_sample.json`（示例 JSON 文件可以在 [./prompt_template_sample.json](./prompt_template_sample.json) 找到）。

  - 你也可以用环境以下环境变量来配置 `system` 和 `user` 角色 prompt：`BBM_CHATGPTAPI_USER_MSG_TEMPLATE` 和 `BBM_CHATGPTAPI_SYS_MSG`。
  该参数可以是提示模板字符串，也可以是模板 `.txt` 文件的路径。

- `--batch_size`

  指定批量翻译的行数(默认行数为 10，目前只对 txt 生效)

- `--accumulated_num`:

  达到累计token数开始进行翻译。gpt3.5将total_token限制为4090。
  例如，如果您使用`--accumulation_num 1600`，则可能会输出2200个令牌，另外200个令牌用于系统指令（system_message）和用户指令（user_message），1600+2200+200 = 4000，所以token接近极限。你必须选择一个自己合适的值，我们无法在发送之前判断是否达到限制

- `--use_context`:

  prompts the model to create a three-paragraph summary. If it's the beginning of the translation, it will summarize the entire passage sent (the size depending on `--accumulated_num`).
  For subsequent passages, it will amend the summary to include details from the most recent passage, creating a running one-paragraph context payload of the important details of the entire translated work. This improves consistency of flow and tone throughout the translation. This option is available for all ChatGPT-compatible models and Gemini models.

  模型提示词将创建三段摘要。如果是翻译的开始，它将总结发送的整个段落（大小取决于`--accumulated_num`）。
  对于后续的段落，它将修改摘要，以包括最近段落的细节，创建一个完整的段落上下文负载，包含整个翻译作品的重要细节。 这提高了整个翻译过程中的流畅性和语气的一致性。 此选项适用于所有ChatGPT兼容型号和Gemini型号。

  - `--context_paragraph_limit`:

    使用`--use_context`选项时，使用`--context_paragraph_limit`设置上下文段落数限制。

- `--temperature`:

  使用 `--temperature` 设置 `chatgptapi`/`gpt4`/`claude`模型的temperature值.
  如 `--temperature 0.7`.

- `--block_size`:

  使用`--block_size`将多个段落合并到一个块中。这可能会提高准确性并加快处理速度，但可能会干扰原始格式。必须与`--single_translate`一起使用。
  例如：`--block_size 5 --single_translate`。

- `--single_translate`:

  使用`--single_translate`只输出翻译后的图书，不创建双语版本。

- `--translation_style`:

  如: `--translation_style "color: #808080; font-style: italic;"`

- `--retranslate "$translated_filepath" "file_name_in_epub" "start_str" "end_str"(optional)`:

  - 重新翻译，从 start_str 到 end_str 的标记:

  ```shell
  python3 "make_book.py" --book_name "test_books/animal_farm.epub" --retranslate 'test_books/animal_farm_bilingual.epub' 'index_split_002.html' 'in spite of the present book shortage which' 'This kind of thing is not a good symptom. Obviously'
  ```

  - 重新翻译, 从start_str 的标记开始:

  ```shell
  python3 "make_book.py" --book_name "test_books/animal_farm.epub" --retranslate 'test_books/animal_farm_bilingual.epub' 'index_split_002.html' 'in spite of the present book shortage which'
  ```

### 示范用例

**如果使用 `pip install bbook_maker` 以下命令都可以改成 `bbook args`**

```shell
# 如果你想快速测一下
python3 make_book.py --book_name test_books/animal_farm.epub --openai_key ${openai_key} --test

# 或翻译完整本书
python3 make_book.py --book_name test_books/animal_farm.epub --openai_key ${openai_key} --language zh-hans

# Or translate the whole book using Gemini
python3 make_book.py --book_name test_books/animal_farm.epub --gemini_key ${gemini_key} --model gemini

# 指定环境变量来略过 --openai_key
export OPENAI_API_KEY=${your_api_key}

# Use the DeepL model with Japanese
python3 make_book.py --book_name test_books/animal_farm.epub --model deepl --deepl_key ${deepl_key} --language ja

# Use the Claude model with Japanese
python3 make_book.py --book_name test_books/animal_farm.epub --model claude --claude_key ${claude_key} --language ja

# Use the CustomAPI model with Japanese
python3 make_book.py --book_name test_books/animal_farm.epub --model customapi --custom_api ${custom_api} --language ja

# Translate contents in <div> and <p>
python3 make_book.py --book_name test_books/animal_farm.epub --translate-tags div,p

# 修改prompt
python3 make_book.py --book_name test_books/animal_farm.epub --prompt prompt_template_sample.txt
# 或者
python3 make_book.py --book_name test_books/animal_farm.epub --prompt "Please translate \`{text}\` to {language}"
# 翻译 kobo e-reader 中，來自 Rakuten Kobo 的书籍
python3 make_book.py --book_from kobo --device_path /tmp/kobo

# 翻译 txt 文件
python3 make_book.py --book_name test_books/the_little_prince.txt --test
# 聚合多行翻译 txt 文件
python3 make_book.py --book_name test_books/the_little_prince.txt --test --batch_size 20


# 使用彩云小译翻译(彩云api目前只支持: 简体中文 <-> 英文， 简体中文 <-> 日语)
# 彩云提供了测试token（3975l6lr5pcbvidl6jl2）
# 你可以参考这个教程申请自己的token (https://bobtranslate.com/service/translate/caiyun.html)
python3 make_book.py --model caiyun --caiyun_key 3975l6lr5pcbvidl6jl2 --book_name test_books/animal_farm.epub
# 可以在环境变量中设置BBM_CAIYUN_API_KEY，略过--openai_key
export BBM_CAIYUN_API_KEY=${your_api_key}
```

更加小白的示例

```shell
python3 make_book.py --book_name 'animal_farm.epub' --openai_key sk-XXXXX --api_base 'https://xxxxx/v1'

# 有可能你不需要 python3 而是python
python make_book.py --book_name 'animal_farm.epub' --openai_key sk-XXXXX --api_base 'https://xxxxx/v1'
```

[演示视频](https://www.bilibili.com/video/BV1XX4y1d75D/?t=0h07m08s)
[演示视频 2](https://www.bilibili.com/video/BV1T8411c7iU/)

使用 Azure OpenAI service

```shell
python3 make_book.py --book_name 'animal_farm.epub' --openai_key XXXXX --api_base 'https://example-endpoint.openai.azure.com' --deployment_id 'deployment-name'

# Or python3 is not in your PATH
python make_book.py --book_name 'animal_farm.epub' --openai_key XXXXX --api_base 'https://example-endpoint.openai.azure.com' --deployment_id 'deployment-name'
```

## 注意

1. Free trail 的 API token 有所限制，如果想要更快的速度，可以考虑付费方案
2. 欢迎提交 PR

# 感谢

- @[yetone](https://github.com/yetone)

# 贡献

- 任何 issue PR 都欢迎
- Issue 中有些 TODO 没做的都可以选
- 提交代码前请先执行 `black make_book.py` [^black]

# 其它推荐项目

- 书译 iOS -> [AI 全书翻译工具](https://apps.apple.com/cn/app/%E4%B9%A6%E8%AF%91-ai-%E5%85%A8%E4%B9%A6%E7%BF%BB%E8%AF%91%E5%B7%A5%E5%85%B7/id6447665417)

## 赞赏

谢谢就够了

![image](https://user-images.githubusercontent.com/15976103/222407199-1ed8930c-13a8-402b-9993-aaac8ee84744.png)

[^token]: https://platform.openai.com/account/api-keys
[^black]: https://github.com/psf/black
