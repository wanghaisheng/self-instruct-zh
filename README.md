# self-instruct-zh

future plan 

1. amazon review and qa 是否能做成这种
2. flybirds的规则集是否可以借鉴

基于ChatGPT构建的中文self-instruct数据集

## 数据集构想：

参考[Stanford Alpaca](https://github.com/tatsu-lab/stanford_alpaca/blob/main/generate_instruction.py)中的构想，使用大语言模型生成问题集与对应的回答。

在生成上，本数据集主要与Alpaca有如下不同：
1. 问题和答案基本都是中文的。使用ChatGPT翻译了种子数据集，并进行了人工校对，将其中关于美国的问题转成了和国人更相关的。
2. 这里采用ChatGPT作为LLM的候选，因为其价格比text-davinci-003价格更低。而且人们往往更喜欢ChatGPT的回答，看起来似乎更加有理有据。
3. 考虑到ChatGPT的回答往往很长，为追求回答的质量，把问题生成和结果生成拆分开进行。
4. 在生成中文时，采取“指令”和“上下文”来对应英文中的“Instruction”和“input”。

## 使用的Prompt

### 生成问题的Prompt
```
想出一套20个不同的任务指令。这些任务指令将被交给一个GPT模型，我们将评估GPT模型对指令的完成情况。

以下是要求。
1. 尽量不要重复每个指令的动词，以最大限度地提高多样性。
2. 指示的语言也应该是多样化的。例如，你应该把问题和命令式的指令结合起来。
3. 指示的类型应该是多样化的。列表中应包括不同类型的任务，如开放式的生成、分类、编辑等。
4. GPT语言模型应该能够完成指令。例如，不要要求助手创建任何视觉或音频输出。再比如，不要要求助手在下午5点叫醒你或设置一个提醒，因为它不能执行任何行动。
5. 指示应该是中文的。
6. 指示应该是1到2句话的长度。既可以是命令句，也可以是疑问句。
7. 你应该为指令生成一个适当的上下文。上下文应该为指令提供的具体例子。它应该涉及真实的数据，不应该包含简单的占位符。上下文应提供实质性的内容，使指令具有挑战性，但最好不要超过100字。
8. 并非所有的指令都需要上下文。例如，当一个指令询问一些一般性的信息，"世界上最高的山峰是什么"，就没有必要提供具体的背景。在这种情况下，我们只需在上下文中写上"无"。
20项任务的清单:
```

然后会在Prompt中增加三个从seed问题中采样的例子，如：
```
###问题1：
指令：
给我一个下列食物的烹饪菜谱。

上下文：
鱼香肉丝

###问题2：
指令：
你得到了一个产品特性清单，需要创建它的描述。

上下文：
名称：Spirit II E-210 燃气烧烤炉
不锈钢燃烧器：2个
尺寸-带盖关闭（英寸）：44.5"H x 48"W x 27"D
尺寸-带盖打开（英寸）：57"H x 48"W x 26"D
侧桌：2"

###问题3：
指令：
解决数学问题。提供中间步骤。

上下文：
一列火车以每小时36公里的速度行驶，经过一个杆子只用了9秒。这列火车的长度是多少？
```

以下是一个示例的生成结果：
```
###问题4：
指令：
写一篇文章，介绍旅游目的地的历史和文化。

上下文：
目的地：埃及金字塔
```

### 生成答案的Prompt
在生成答案时，为更像人类的问题，仅仅使用一个空行将指令和上下文（如果有）分开。

例：
```
你在工作面试中如何回答这个问题？（对应指令）

你一个人开车的时候会想些什么？（对应上下文）
```

样例的ChatGPT生成结果：
```
作为AI语言模型，我没有个人经验和情感，但我可以提供以下建议：

当面临这个问题时，你需要注意以下几点：

1. 面试官关注的是你的思维方式和态度，而不是你的个人生活习惯。

2. 回答应该要简洁明了。

3. 回答时应该时刻记住公司文化和职位要求。

针对这个问题，你可以考虑从以下几个方面进行回答：

1. 安全意识：我会注意交通规则，遵守交通指示，保障自己和他人的安全。

2. 放松方式：当我一个人开车时，我会选择听音乐或者收听有趣的广播，这样可以让我放松自己，缓解工作压力。

3. 思考时间：有时候我会把手中的事情过一边头，计划一下未来的工作安排，或者想象一些解决问题的方案。

4. 清晰思路：在长途驾驶的时候，我通常会开启导航仪，熟悉路线规划，保证自己在旅途中有一个清晰的思路。

总之，在回答这个问题时，你应该注重展示自己的思维方式，同时也要在回答中表现出自己的积极态度和适应力。
```

## 数据集结构
data中为数据内容
+ seed_chatgpt.csv：种子问题集及ChatGPT的回答
+ promblems：生成的问题集
+ answers: 生成的答案集

## 商业用途
根据OpenAI的要求，似乎对生成内容有一些商业上的限制。

## 其他中文指令微调数据集
1. Belle  
Belle提供了一些基于ChatGPT生成的指令微调数据集  
https://github.com/LianjiaTech/BELLE/tree/main/data
2. GPT-4-LLM  
包含由GPT4生成的中英文指令微调数据  
https://github.com/Instruction-Tuning-with-GPT-4/GPT-4-LLM
3. ShareGPT  
由ShareGPT上分享的ChatGPT真实对话  
https://huggingface.co/datasets/philschmid/sharegpt-raw
4. HC3-chinese  
搜集的社区问答，以及人工和ChatGPT的回答  
https://huggingface.co/datasets/Hello-SimpleAI/HC3-Chinese
5. InstructionWild  
网上搜集的问题，并由ChatGPT回答  
https://github.com/XueFuzhao/InstructionWild
6. COIG  
人工构建的指令数据  
https://huggingface.co/datasets/BAAI/COIG
7. FireFly  
人工构建的指令数据  
https://huggingface.co/datasets/YeungNLP/firefly-train-1.1M
