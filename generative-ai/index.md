# 生成式人工智能


微软生成式人工智能课程的笔记。

<!--more-->

---

参考：

- [generative-ai-for-beginners](https://microsoft.github.io/generative-ai-for-beginners)
- [github repo](https://github.com/Microsoft/generative-ai-for-beginners)

<br/>
<br/>

# 概述

![生成式AI](https://raw.githubusercontent.com/zhang21/images/master/cs/generative-ai/repository-thumbnail.png)

学习构建生成式 AI 应用程序的基础知识。

<br/>

---

<br/>

# 课程环境配置

<br/>
<br/>

## 设置步骤

为了避免依赖等问题，建议使用 github codespace（github online vscode）。

1. Fork this repo
2. Create a codespace
3. Storing your API keys

<br/>
<br/>

## 如何在本地环境运行

1. 安装 Python
2. 克隆本仓库到本地
3. 安装 [Minicoda](https://docs.anaconda.com/free/miniconda/)（可选）：用于创建和管理 coda 和 python 虚拟环境
4. 使用 VS Code 和 Python 扩展
5. 在浏览器中使用 [Jupyter](https://jupyter.org/)

<br/>
<br/>

## 课程和技术要求

课程包括 6 节概念课和 6 节编码课。

编码课用了 Azure OpenAI Service，你需要有 Azure OpenAI Service API key 来运行此代码。

你可以通过填写本申请来 [申请 Azure OpenAI API](https://azure.microsoft.com/zh-cn/products/ai-services/openai-service/)。

<br/>
<br/>

## 首次使用 Azure OpenAI

如果您是首次使用 Azure OpenAI 服务，请按照本指南了解如何 [创建和部署 Azure OpenAI 服务资源](https://learn.microsoft.com/zh-cn/azure/ai-services/openai/how-to/create-resource)。

<br/>
<br/>

## 首次使用 OpenAI API

如果你是首次使用 OpenAI API，请按照指南了解如何 [创建和使用接口](https://platform.openai.com/docs/quickstart)。

<br/>
<br/>

## 认识其他学员

官方的 [AI Community Discord](https://discord.com/invite/ByRwuEEgH4) 可以其他人进行交流。

<br/>

---

<br/>

# 生成式人工智能和大语言模型

了解什么是生成式 AI 以及 LLMs 的工作原理。

生成式 AI 是一种能够生成文本、图像和其他类型内容（语音、视频和代码等）的人工智能。任何人都可以使用它，只需一个文本提示，一句用自然语言写成的句子。你不需要学习 Java 或 SQL 等语言来完成事情，你只需要使用你的语言，说出你想要的东西，AI 模型就会给出建议。它的应用和影像是巨大的，你可以在短时间内编写应用程序等。

在本课程中，我们将探讨初创公司如何利用生成式 AI来开启新局面，以及如果应对社会影响和技术局限性的挑战。

<br/>
<br/>

## 教育场景

本例中，我们将通过一家虚构的初创公司来讨论它是如何彻底改变教育的。公司的目标是：在全球范围了提高学习的可及性确保公平的教育机会，并根据每个学习者的需要为其提供个性化的学习体验。

创业团队意识到，如果不利用现代最强大的工具之一 LLMs，将无法实现这一目标。

<br/>
<br/>

## 如何获取生成式AI

AI 的统计方法：机器学习。从数据中学习模式，而无需明确编程。

神经网络和现代虚拟助手。先进的机器学习算法，称为神经网络或深度学习算法。神经网络（尤其是递归神经网络 RNNs），极大的增强了自然语言处理能力。

今天，生成式 AI。它可以看作是深度学习的一个子集。

![AI发展](https://raw.githubusercontent.com/zhang21/images/master/cs/generative-ai/AI-diagram.png)

<br/>

变换器的新模型架构克服了 RNNs 的限制，能够获取更长的文本序列作为输入。

大多数的 AI 模型（也称为大语言模型，处理文本输入和输出）都是基于这种架构。

<br/>
<br/>

## 大预言模型如何工作

介绍 OpenAI GPT 模型如何工作。

- **分词器（Tokenizer），文本到数字**：LLM 接收文本作为输入，并生成文本作为输出。然而，作为统计模型，它们对数字的处理比对文本序列要好得多。这就是为什么每个输入在被核心模型使用前，都要经过分词器处理的原因。词块/词元（token）由可变的字符数量组成的文本块（chunk of text），因此分词器的主要任务是将输入分割成词块数组。然后，每个词块会映射一个词块索引（token index），即原始文本块的整数编码。

![分词器例子](https://raw.githubusercontent.com/zhang21/images/master/cs/generative-ai/tokenizer-example.png)

<br/>

- **预测输出词块**：给定 n 给词块作为输入，模型能够预测一个词块作为输出。然后，这个词块会以扩展窗口的模式纳入下一次迭代的输入中，从而使用户在获得一个（多个）句子作为答案时获得更好的体验。比如使用 ChatGPT 有时它看起来像是在句子中间停止了。

<br/>

- **选择过程，概率分布**：输出词块由模型根据当前文本序列之后出现的概率进行选择。因为模型预测了所有可能的 “下一个词块” 的概率分布（由训练计算得出）。但是，并不是从结果分布中选择概率最高的词块。这种选择增加了一定的随机性，使模型以一种非确定的方式运行——不会因为相同的输入而得到相同的输出。加入这种随机性是为了模拟创造性思维过程。

<br/>
<br/>

## 初创公司如何利用大预言模型

大语言模型的输入称为提示，输出称为完成，指的是模型生成下一个词块以完成当前输入的机制。

- 指定我们期望从模型中得到的输出类型的指令。
  - 总结文章和书籍等内容，并从中提取见解。
  - 对文章和论文等进行构思和设计。
  - 以与代理人对话的形式提出问题。
  - 对写作的帮助
  - 对代码的帮助

<br/>

---

<br/>

# 探索和比较不同的大语言模型

本章内容：

- 了解不同类型的 LLM。
- 测试、迭代和比较不同的模型。
- 如何部署 LLM。

<br/>
<br/>

## 了解不同类型的大语言模型

LLM 可以根据其架构、训练数据和用例进行多种分类。你对模型的选择取决于你的目标、数据和费用等。

根据你是否打算将模型用于文本、音频、视频、图像生成等，你可能会选择不同类型的模型。

- **音频和语音识别** [Whisper-type 模型](https://platform.openai.com/docs/models/whisper) 是不错的选择，它们是通用型的，以语音识别为目标。
- **图像生成** [DALL-E](https://platform.openai.com/docs/models/dall-e) 和 Midjourney 是两个非常著名的选择。
- **文本生成** 大多数模型都是在文本生成方面进行训练的，你有从 GPT-3.5 到 GPT-4 的多种选择。
- **多种模式** 如果你希望在输入和输出中处理多种类型的数据，你可以考虑 gpt-4 turbo 或 gpt-4o（OpenAI 模型的最新版本）。它们能够将自然语言处理于视觉理解相结合，通过多模态界面实现交互。

选择一个模型意味着你可以获得一些基本功能，但这还不够。通常情况下，你需要向 LLM 提供特定的数据。关于如何处理这些数据，

<br/>
<br/>

### 基础模型与大语言模型

基础模型（Foundation Model）一词，它被定义为遵循某些标准的人工智能模型。例如：

- **它们使用无监督学习或自我监督学习进行训练** 这意味着它们是在无标注的多模态数据上进行训练的，而且训练过程中不需要认为注解或标注数据。
- **它们是非常大的模型** 基于数十亿参数训练的深度神经网络。
- **它们通常是作为其他模型的基础** 它们可以作为一个起点，在此基础上建立其他模型。

<br/>

![基础模型](https://raw.githubusercontent.com/zhang21/images/master/cs/generative-ai/FoundationModel.png)

<br/>

为了进一步说明这种区别，以 ChatGPT 为例。在构建 ChatGPT 的第一个版本时，一个名为 GPT-3.5 的模型充当了基础模型。这意味着，OpenAI 使用了一些特定于聊天的数据，创建了一个经过调整的 GPT-3.5 版本，专门用于在对话场景（如聊天机器人）中表现良好。

![栗子](https://raw.githubusercontent.com/zhang21/images/master/cs/generative-ai/Multimodal.png)

<br/>
<br/>

### 开源模式与专有模式

LLM 也可以按其是开源还是专有进行分类。

开源模型是向公众提供的模型，允许针对 LLM 中的各种用例进行检查、修改和定制。不过，它们并不对生产使用进行优化，性能可能不如专有模式。流行的开源模型包括：Alpaca, Bloom, LLaMA, Gemma。

专有模型是公司拥有的，不向公众提供。这些模型通常针对生产进行了优化。但是，它们不允许修改或定制。它们并不是免费提供。此外，用户无法控制用于训练模型的数据。流行的专有模型包括：[OpenAI Models](https://platform.openai.com/docs/models), Google Bard, Claude 2。

<br/>
<br/>

### 嵌入与图像生成与文本和代码生成

LLM 也可以按其生成的输出进行分类。

嵌入（Embedding）是一组可以将文本转换为数字形式的模型，是输入文本的数字表示。嵌入可以使机器更容易理解单词或句子之间的关系，并可作为其他模型（如分类模型或聚类模型）的输入，这些模型在数字数据上有更好的表现。嵌入模型通常用于转换学习（transfer learning），即为一个有大量数据的代用任务建立模型，然后将模型权重（嵌入）重新用于其他下游任务。

[OpenAI embedding](https://platform.openai.com/docs/models/embeddings) 就是一个栗子。

![嵌入模型](https://raw.githubusercontent.com/zhang21/images/master/cs/generative-ai/Embedding.png)

<br/>

图像生成模型，用于图像编辑、图像合成和图像翻译。图像生成模型通常在大型图像数据集（如 LAION-5B）上进行训练，可用于生成图像，或内绘、超分辨率。例如 [DALL-E-3](https://openai.com/index/dall-e-3/) 和 [Stable Diffusion Models](https://github.com/Stability-AI/StableDiffusion)

![图像生成模型](https://raw.githubusercontent.com/zhang21/images/master/cs/generative-ai/Image.png)

<br/>

文本和代码生成模型，用于生成文本和代码。这些模型通常用于文本摘要、翻译和问题解答。文本生成模型通常在大型文本数据集上进行训练，可用于生成新文本或回答问题。代码生成模型通常在大型代码数据集上进行训练，可用于生成新代码或修复现有代码中的错误。

![文本生成](https://raw.githubusercontent.com/zhang21/images/master/cs/generative-ai/Text.png)

<br/>
<br/>

### 编码器解码器与纯解码器

Encoder-Decoder versus Decoder-only

关于 LLM 的不同架构类型，不妨打个比方。

比如为学生编写一份测试，一个人负责创造内容，另一个负责审核内容。

内容创作者就像一个解码器模型，他们可以查看主题，看看你已经写了什么，然后在此基础上编写课程。解码器很擅长编写信息丰富的内容，却不擅长理解主题和学习目标。解码器模型的例子如 GPT-3。

审阅者就像一个编码器模型，他们负责审阅所写的课程和答案，注意它们之间的关系并理解上下文，但编码器并不擅长生成内容。编码器模型的示例如 BERT。

如果有人既可以创建也可以审核内容，这就是编码器-解码器模型，如 BART 和 T5。

<br/>
<br/>

### 服务与模型

服务（service）通常是云服务商提供的产品，通常由模型、数据和其他组件组成。模型（model）是服务的核心组件。

服务通常针对生产使用进行了优化，通过 界面和 API 可调用，通常比模型更容易使用。不过，服务并不总是免费的。如 Azure OpenAI 服务等。

模型指示神经网络，包括参数、权重等。然而，要在公司本地运行，就需要购买设备、建立规模化结构和购买许可证等。像 LLaMA 这样的模型是可以使用的，但需要算力来运行模型。

<br/>
<br/>

## 在 Azure 上使用不同模型进行测试

大多数模型都可在 [Azure AI Studio](https://ai.azure.com/) 上找到。

<br/>
<br/>

## 提高大语言模型的结果

什么时候应该考虑对模型进行微调（fine-tuning），而不是使用预训练的模型呢？是否还有其它方法可以提高模型在特定工作负载上的性能？

你可以不同训练程度的不同类型的模型。

在生产环境中部署 LLM，复杂程度、成本和质量各不相同。以下是一些方法：

- **带上下文的提示工程（Prompt engineering with context）**：在提示时提供足够的上下文，以确保获得准确的回复。
- **检索增强生成（Retrieval Augmented Generation, RAG）**：对大型语言模型输出进行优化，使其能够在生成响应之前引用训练数据来源之外的权威知识库。
- **微调模型（Fine-tunned）**：根据自己的需要对模型进一步训练，从而使模型更加精确化满足你的需求，但成本可能更高。

![模型部署](https://raw.githubusercontent.com/zhang21/images/master/cs/generative-ai/Deploy.png)

<br/>
<br/>

### 带上下文的提示工程

预训练好的 LLM 在处理通用自然语言任务时的效果非常好，甚至可以通过简短的提示来调用它们——这就是所谓的 “零样本（zero-shot）” 学习。

然而，用户能够通过详细的请求和示例（上下文）框定的查询越多，答案就越准确，也就越接近用户的预期。在这种情况下，如果提示只包含一个示例，我们称之为“单样本（one-shot）”学习；如果包含多个实例，就称之为“小样本（few-shot）”学习。

带有上下文的提示工程是最具成本效益的方法。

<br/>
<br/>

### 检索增强生成

LLM 有一个局限性，它们只能使用训练过程中使用过的数据来生成答案。这意味着它们对训练过程之后发生的事实一无所知，也无法访问非公开的信息（如公司内部数据）。可以使用“检索增强生成（RAG）”来克服这一问题。RAG 是一种考虑到提示语长度限制，以文件块的形式用外部数据增强提示语的技术。矢量（vector）数据库工具可提供支持，从各种预定义数据源中检索有用的数据块，并将其添加到提示内容中。

这个技术非常有用，当企业没有足够的数据、时间或资源对 LLM 进行微调，但仍希望提高特定工作负载的性能并降低捏造的风险。

<br/>
<br/>

### 微调模型

微调是一个利用转换学习使模型适应下游任务或解决特定问题的过程。与小样本学习和 RAG 不同，微调的结果是生成一个新的模型，并更新权重（weight）和偏置（bias）。它需要一组由单一输入（提示）以及相关输出（完成）组成的训练示例。

下面这些情况，微调更可取：

- **使用微调模型**。企业希望使用功能较弱的微调模型，而不是高性能模型，从而获得更具成本效益和更快的解决方案。
- **考虑延迟**。对特定用例来说，延迟非常重要。因此不可能使用很长的提示，或者从模型中学习的示例数量与提示长度限制不符。
- **保持更新**。企业拥有大量高质量数据和实时标签，以及长期更新这些数据所需的资源。

<br/>
<br/>

### 训练模型

毫无疑问，从头开始训练 LLM 是最困难、最复杂的方法，需要海量数据、技术资源和计算能力。只有当企业拥有特定领域的用例和大量以领域为中心的数据时，才应考虑采用这种方法。

<br/>

---

<br/>

# 负责任地使用生成式人工智能

本章内容包括：

- 在构建生成式人工智能时，要优先考虑负责任的人工智能。
- 负责任的人工智能的核心原则以及生成式人工智能的关系。
- 如果通过战略和工具将这些负责任的人工智能原则付诸实践。

<br/>
<br/>

## 负责任的人工智能原则

公平、包容、可靠、安全、隐私、透明和问责。

生成式人工智能的独特之处在于它能为用户创建有用的答案、信息、指导和内容。如果没有适当的规划和策略，它也可能不幸地导致一些对用户、产品和社会有害的结果。

- 幻觉/虚假
- 有害内容
- 缺乏公平

<br/>

---

<br/>

# 了解提示工程

精心编写的提示语可以提高回复的质量。生成式人工智能能够根据用户请求创建内容（如文本、图像、音频、代码和视频等），它使用大语言模型来实现。

用户可以使用聊天等模式与模型进行交互，而无需任何专业技术知识。这些模型基于提示——用户发送输入（prompt），然后得到 AI 的回复（complete）。然后，可以在多轮对话中反复与人工智能聊天，改进他们的提示，知道回答符合预期。

现在，**提示** 已成为 AIGC 的主要编程接口，它告诉模型该做什么，并影响返回响应的质量。**提示工程（Prompt Engineering）** 是一个快速发展的研究领域，其重点是设计和优化提示，以提供一致和高质量的大规模响应。

<br/>

本章节目标：

- 解释什么是提示工程
- 描述提示语的组成部分及其使用方法
- 学习提示工程的最佳实践
- 使用 OpenAI 将学习用于实践

<br/>
<br/>

## 沙箱环境

Jupyter Notebook 提供了一个沙箱环境，你可以在其中尝试学到的内容。要执行这些练习，你需要：

- Azure OpenAI API key
- A Python Runtime
- Local Env Variables

<br/>
<br/>

## 图文指南

![提示工程基础](https://raw.githubusercontent.com/zhang21/images/master/cs/generative-ai/04-prompt-engineering-sketchnote.png)

<br/>
<br/>

## 我们的初创项目

我们希望建立由人工智能驱动的个性化学习应用，让我们思考应用如何为不同的用户设计提示。

- 管理员可能要求 AI 分析课程数据，以确定覆盖范围。
- 教育工作者可能会要求 AI 为目标受众和主题生成个性化的课程计划。
- 学生可能会要求 AI 为课程提供辅导。

<br/>
<br/>

## 什么是提示工程

可将提示工程定义为设计和优化输入（prompts）的过程，以便针对给定的应用目标和模型提供一致且高质量的响应（completions）。可将其视为一个分两步走的过程：

- 为特定模型和对象设计初始提示
- 反复推敲提示内容，提高回答质量

这必然是一个反复试验的过程。它非常重要，我们首先需要了解三个概念：

- **Tokenization**：模型如何 “看到” 提示
- **Base LLMs**：基础模型如何 “处理” 提示
- **Instruction-Tuned LLMs**：模型现在如何看到 “任务”

<br/>
<br/>

### 词块化

词块化（Tokenization）

LLM 将提示视为一串词块（token），不同的模型可以用不同的方式对同一提示进行分词（tokenize）。由于 LLM 是在 **词块/词元**（而不是原始文本）上进行训练的，因此对提示进行分词的方式会直接影响生成回复的质量。

要直观的了解词块化是如何工作的，可以使用 [OpenAI Tokenizer](https://platform.openai.com/tokenizer) 等工具。复制你的提示，看看它是如何转换成词块的，注意空白字符和标点符号是如何处理的。

![分词器](https://raw.githubusercontent.com/zhang21/images/master/cs/generative-ai/04-tokenizer-example.png)

<br/>
<br/>

### 基础模型

提示被词块化后，基础模型（foundation model）的主要功能是预测该序列中的词块。由于 LLM 是在海量的数据集上训练出来的，因此它对词块之间的关系有很好的感知，并能在一定程度上做出预测。

请注意，它并不理解提示语中的词或词块的含义。它只是看到了一种模式，可以用下一次预测来完成。它可以继续预测序列，直到用户干预或某些条件而终止。

<br/>
<br/>

### 指令微调型大语言模型

指令微调型大语言模型从基础模型开始，通过示例的微调或输入输出对进行微调。这些可以包含明确的指令，以及 AI 能试图遵循该指令的响应。

这种方法使用了 **带人类反馈的强化学习**（RLHF, Reinforcement Learning with Human Feedback）等技术，可以训练模型遵从指令并从反馈中学习，从而做出更适合的响应。

<br/>
<br/>

## 为什么需要提示工程

如果不在提示构建和优化方面下功夫，就很难实现可靠和一致性的完成。例如：

- **模型的响应是随机的**。同样的提示在不同的模型和模型版本上可能会产生不同的响应。提示工程能最大限度地减小响应的误差。
- **模型可以编造响应**。模型使用大量但有限的数据集预先训练出来的，这意味着它们缺乏训练范围之外的知识。因此，它们可能产生不准确、虚构或与事实相矛盾的完成。提示工程可以帮助识别并减少此类编造。
- **模型的功能各有差异**。较新的模型将具有更丰富的功能，但也会带来独特的怪癖，并在成本和复杂性方面进行权衡。提示工程可帮助开发最佳实践和工作流程，以抽象化的方式消除差异，并以可扩展的无缝方式适应特定模型的要求。

<br/>

编造（fabrication）。

提示 AI 为一个不存在的话题生成内容（确保在训练集中找不到这个主题），来了解编造是如何工作的。

> Prompt: generate a lesson plan on the Martian War of 2076.

可以在不同的 LLM 中查看效果。

<br/>
<br/>

## 提示工程案例

通过一个案例研究来了解提示工程在实际解决方案中的应用：GitHub Copilot。

<br/>
<br/>

## 提示语建设

了解提示语是如何构建的，以便评估不同的技术，从而更有效地设计提示语。

<br/>
<br/>

### 基本提示

最基本的提示（basic prompt）：向模型发送一个没有其他上下文的文本输入。

下面是一个例子，输入美国国歌的前几个词语，它生成了后面的内容。

| Prompt(Input) | Compeletion(output) |
| - | - |
| Oh you can see | It sounds like you're starting the lyrics to "The Star-Spangled Banner," the national anthem of the United States. The full lyrics are ... |

<br/>
<br/>

### 复杂提示

复杂提示（complex prompt）：增加了上下文和说明的基本提示。

<br/>
<br/>

### 指令提示

指令请求（instruction prompt），可以使用文本更详细地说明任务，为 AI 提供更好的指导。示例如下：

| Prompt(Input) | Completion(Output) | Instruction Type |
| - | - | - |
| Write a description of the Civil War | returned a simple paragraph | Simple |
| Write a description of the Civil War. Provide key dates and events and describe their significance | returned a paragraph followed by a list of key event dates with descriptions | Complex |
| Write a description of the Civil War in 1 paragraph. Provide 3 bullet points with key dates and their significance. Provide 3 more bullet points with key historical figures and their contributions. Return the output as a JSON file | returns more extensive details in a text box, formatted as JSON that you can copy-paste to a file and validate as needed | Complex. Formatted |

<br/>
<br/>

## 主要内容

上面的示例，提示语仍然具有相当的开放性，允许 LLM 决定其预训练数据集中哪些部分是相关的。在 **主要内容（primary content）** 设计模式中，输入文本被分为两部分：

- 指令（动作）
- 相关内容（影响动作）

一个例子，指令是 “用两句话概括”。

| Prompt(Input) | Completion(Output) |
| - | - |
| Jupiter is the fifth planet from the Sun and the largest in the Solar System. It is a gas giant with a mass one-thousandth that of the Sun, but two-and-a-half times that of all the other planets in the Solar System combined. Jupiter is one of the brightest objects visible to the naked eye in the night sky, and has been known to ancient civilizations since before recorded history. It is named after the Roman god Jupiter.[19] When viewed from Earth, Jupiter can be bright enough for its reflected light to cast visible shadows,[20] and is on average the third-brightest natural object in the night sky after the Moon and Venus. | Jupiter, the fifth planet from the Sun, is the largest in the Solar System and is known for being one of the brightest objects in the night sky. Named after the Roman god Jupiter, it's a gas giant whose mass is two-and-a-half times that of all other planets in the Solar System combined. |

<br/>

主要内容分段可以通过各种方式来推动更有效的指令：

- **举例说明**：与其用明确的指令告诉模型该怎么做，不如举例说明，让模型去推断模式。
- **提示线索**：在指令后面加一个提示，为完成指令打下基础，引导模型做出更相关的反应。
- **模板**：可重复的提示，可针对特定用例使用数据进行定制。

<br/>
<br/>

### 举例说明

此方法是使用主要内容向模型投喂一些特定指令所需要输出示例，然后让模型推断出所需的输出模式。根据所提供示例的数量，有零样本提示、单样本提示和小样本提示。

提示现在由三部分组成：

- 任务描述
- 几个所需的输出示例
- 新示例的开始

| Learning Type | Prompt(Input) | Completion(Output) |
| - | - | - |
| Zero-shot | "The Sun is Shining". Translate to Spanish | "El Sol está brillando" |
| One-shot | "The Sun is Shining" => ""El Sol está brillando" <br> "It's a Cold and Windy Day" => | "It's a Cold and Windy Day" => |
| Few-shot | The player ran the bases => Baseball <br/> The player hit an ace => Tennis <br/> The player hit a six => Cricket <br/> The player made a slam-dunk => | Basketball |

<br/>
<br/>

### 提示线索

使用主要内容的另一种技巧是提供提示（cues）而不是示例。着这种情况下，用一个反映所需的格式的片段来启动模型，从而在正确的方向上给它一个提示。然后，模型就会接受提示，并继续沿着这个方向前进。

| 提示线索 | Prompt(Input) | Completion(Output) |
| - | - | - |
| 0 | Jupiter is the fifth planet from the Sun and the largest in the Solar System. It is a gas giant with a mass one-thousandth that of the Sun, but two-and-a-half times that of all the other planets in the Solar System combined. Jupiter is one of the brightest objects visible to the naked eye in the night sky, and has been known to ancient civilizations since before recorded history. <br> Summarize This | Jupiter is the largest planet in our Solar System and the fifth one from the Sun. It is a gas giant with a mass 1/1000th of the Sun's, but it is heavier than all the other planets combined. Ancient civilizations have known about Jupiter for a long time, and it is easily visible in the night sky.. |
| 1 | Jupiter is the fifth planet from the Sun and the largest in the Solar System. It is a gas giant with a mass one-thousandth that of the Sun, but two-and-a-half times that of all the other planets in the Solar System combined. Jupiter is one of the brightest objects visible to the naked eye in the night sky, and has been known to ancient civilizations since before recorded history. <br> Summarize This <br> What we learned is that Jupiter | is the fifth planet from the Sun and the largest in the Solar System. It is a gas giant with a mass one-thousandth that of the Sun, but two-and-a-half times that of all the other planets combined. It is easily visible to the naked eye and has been known since ancient times. |
| 2 | Jupiter is the fifth planet from the Sun and the largest in the Solar System. It is a gas giant with a mass one-thousandth that of the Sun, but two-and-a-half times that of all the other planets in the Solar System combined. Jupiter is one of the brightest objects visible to the naked eye in the night sky, and has been known to ancient civilizations since before recorded history. <br> Summarize This <br> Top 3 Facts We Learned | 1. Jupiter is the fifth planet from the Sun and the largest in the Solar System. <br> 2. It is a gas giant with a mass one-thousandth that of the Sun... <br> 3. Jupiter has been visible to the naked eye since ancient times ... |

<br/>
<br/>

### 提示模板

提示模板是一个预定义的提示，可根据需要存储和重复使用。它的最简单形式就是一个提示示例的集合，以支持重复使用。

在更复杂的形式中，模板包含的占位符可以用各种来源的数据替换。这样，我们就可以创建一个可重复使用的提示库，用于以编程方式大规模驱动一致的用户体验。 

<br/>
<br/>

## 辅助内容

如果把提示构建看作是有一个指令（任务）和一个目标（主要内容），那么次要内容就像是提供的额外上下文，以某种方式影响输出。它可以是调整参数、格式化说明、主题分类法等，可以帮助模型调整响应，以适应用户的期望。

例如给定一个课程目录，其中包含课程可用的大量元数据（名称、描述、级别、标签、老师等）：

- 可以定义一条摘要课程目录的指令
- 可以使用主要内容提供一些期望输出的示例
- 可以使用次要内容来确定前 5个感兴趣的标签

现在，该模型可以按照示例所示格式提供摘要。但如果一个结果有多个标签，它可以优先处理次要内容中确定的 5个标签。

<br/>
<br/>

## 提示最佳实践

<br/>

### 提示工程思维

- 领域理解很重要。
- 模型理解很重要。
- 迭代和验证很重要。

<br/>
<br/>

### 业内人员推荐的最佳实践

| What | Why |
| - | - |
| 评估最新模型 | 新模型功能更强，但成本更高。|
| 分离指令和内容 | 更清楚的区分指令、主要内容和次要内容。|
| 具体和明确 | 详细地说明期望的内容、输出、长度和格式等。|
| 举例说明 | 展示并讲述 |
| 使用提示 | 提供引导词或短语 |
| 双倍下降 | 有时，可能需要向模型重复自己的话。|
| 顺序很重要 | 不同的顺序可能会影响输出结果。|
| 给模型一个出路 | 如果模型无法完成任务，可以给他一个后备完成响应。这可以减少模型生成错误或捏造的响应的机会。|

<br/>

---

<br/>

# 创建高级提示工程

<br/>

## 提示工程

提示工程是创建将产生所需结果的提示的过程。

<br/>
<br/>

### 提示技巧

一些基本技巧：

- 零样本提示
- 少样本提示
- 思维链
- 生成的知识
- 从最少的最多
- 自我完善
- 多维度提示

<br/>
<br/>

#### 零样本提示

这种提示风格非常简单，它只有一个提示。

```txt
Prompt: "What is Algebra?"
Answer: "Algebra is a branch of mathematics that studies mathematical symbols and the rules for manipulating these symbols."
```

<br/>
<br/>

#### 少样本提示

这种提示方式通过在提出请求的同时提供一些示例来帮助模型。

```txt
Prompt: "以莎士比亚的风格写一首诗。下面是一些莎士比亚十四行诗的例子：十四行诗第 18 首："我要把你比作夏日吗？你更可爱，更有节制......'第 116 首十四行诗："让我不要为真正心灵的结合设置障碍。爱不是爱，当它发现改变时就会改变......'十四行诗第 132 首："我爱你的眼睛，它们怜悯我，知道你的心在折磨我，对我不屑一顾......现在，请写一首关于月亮之美的十四行诗。"
Answer："在天空中，月亮闪烁着柔和的光芒，散发着温柔的光辉，......"
```

<br/>
<br/>

#### 思维链

这个想法以一种让 LLM 了解如何做某种事情的方式来指导 LLM。

```txt
Prompt: "Lisa has 7 apples, throws 1 apple, gives 4 apples to Bart and Bart gives one back: 7 -1 = 6 6 -4 = 2 2 +1 = 3
Alice has 5 apples, throws 3 apples, gives 2 to Bob and Bob gives one back, how many apples does Alice have?" Answer: 1
```

<br/>
<br/>

#### 生成的知识

使用模板构建提示。

<br/>
<br/>

#### 从少的多

从最少到最多提示的想法是将一个更大的问题分解为多个子问题。

```txt
Prompt: How to perform data science in 5 steps?
```

<br/>
<br/>

#### 自我完善

你不能相信生成式人工智能，你需要验证一下。

<br/>
<br/>

#### 多维度的提示

要求 LLM 解释自己，减少 LLM 输出不一致，以确保得出正确的答案。

<br/>
<br/>

## 改变你的数据

LLM 本质上是不确定的，这意味着每次运行相同的提示都会得到不同的结果。

<br/>
<br/>

### 利用温度来改变输出

**温度**（temperature）是 0 到 1 之间的值。其中 0 最具确定性，1 最具变化性。值越高，输出越随机。默认值为 0.7。

<br/>
<br/>

## 高级提示的最佳实践

一些好的做法：

- 指定上下文
- 限制输出
- 指定内容和方式
- 使用模板
- 拼写正确

<br/>

---

<br/>

# 创建文本生成应用

本章目标：

- 解释什么是文本生成应用程序。
- 使用 openai 构建文本生成应用程序。
- 使用 prompt, temperature 和 tokens 等概念来构建文本生成应用程序。

<br/>
<br/>

## 什么是文本生成应用

使用文本生成应用程序构建如：

- 聊天机器人
- 协同助手
- 代码助手

<br/>
<br/>

## 如何入门

需要找到一种与 LLMs 结合的方法，通常使用以下两种：

- 使用 API: 第三方 AI 提供的服务接口
- 使用库：openapi, Langchain, Semantic Kernel

<br/>
<br/>

## 第一个 openai 应用

安装 python 和 openai:

```sh
pip install openai
```

<br/>

准备 openai api key。

<br/>

文字生成和聊天补全。

<br/>
<br/>

## 改进应用

- 将 key 与代码分离
- 关于 token 长度。token 是需要花钱的，因此需要考虑使用多少 token 来生成内容。
- 进行 temperature 调整实验。值越高，输出越随机。值越低，输出越可预测。

<br/>

---

<br/>

# 创建聊天应用

<br/>

---

<br/>

# 创建搜索应用

本章目标：

- 语义搜索和关键字搜索
- 什么是文本嵌入
- 使用嵌入创建一个应用程序来搜索数据

<br/>
<br/>

## 什么是语义搜索

语义搜索是一种使用查询中单词的语义或含义来返回相关结果的搜索技术。

例如，你想买一辆汽车，你可能会搜索“我的梦想汽车”。语义搜索会理解你并不是在梦想一辆汽车，而是想购买你的理想的汽车。

另一种方法是关键字搜索，他会逐字搜索有关汽车的梦想，但通常会返回不相关的结果。

<br/>
<br/>

## 什么是文本嵌入

文本嵌入是 NLP 中使用的文本表示技术。文本嵌入是文本的语义数字表示。嵌入用于以机器易于理解的方式表示数据。

示例：

```txt
Today we are going to learn about Azure Machine Learning.

数字（向量）组成的嵌入。
[-0.006655829958617687, 0.0026128944009542465, 0.008792596869170666, -0.02446001023054123, -0.008540431968867779, 0.022071078419685364, -0.010703742504119873, 0.003311325330287218, -0.011632772162556648, -0.02187200076878071, ...]
```

<br/>
<br/>

## 嵌入索引是是如何创建的

本章的嵌入索引是使用一系列 Python 脚本创建的。

<br/>
<br/>

### 向量数据库

在生产中，嵌入索引存储在向量数据库中。

<br/>
<br/>

## 理解余弦相似度

如何使用文本嵌入来搜索数据，特别是使用余弦相似度找到与给定查询最相似的嵌入。

<br/>
<br/>

### 什么是余弦相似度

余弦相似度是两个向量之间相似度的度量（近邻搜索）。要执行余弦相似度搜索，需要使用 openai embedding api 对查询文本进行向量化。然后计算查询向量与嵌入索引中每个向量之间的余弦相似度。

<br/>

---

<br/>

# 创建图像生成应用

本章目标：

- 图像生成及其有用的原因
- 两种最流行的图像生成模型：DALL-E 和 Midjourney
- 构建图像生成应用程序

<br/>
<br/>

## DALL-E和Midjourney

[DALL-E](https://openai.com/index/dall-e-2/) 和 [Midjourney](https://www.midjourney.com/) 是两种最流行的图像生成模型，它们允许你使用提示词生成图像。

<br/>
<br/>

### DALL-E

DALL-E 是一种生成式 AI 模型，可根据文本描述生成图像。它是 CLIP 和 attention 两种模型的组合。

- CLIP：是一种从图像和文本生成嵌入的模型，嵌入是数据的数字表示。
- diffused attention：是一种从嵌入生成图像的模型。

<br/>
<br/>

### Midjourney

它根据文本提示生成图像。

<br/>
<br/>

### DALL-E和Midjourney如何运作

DALL-E 是一种带有 autoregressive transformer 的 transformer 架构的生成式人工智能模型。

autogressive transformer 定义了模型如何根据文本描述生成图像。它一次生成一个像素，然后使用生成的像素生成下一个像素。经过神经网络中的多个层，直到图像完整。

<br/>
<br/>

## 构建第一个图像生成应用程序

你需要以下库：

- python-dotenv
- openai
- pillow
- requests

<br/>

---

<br/>

# 创建低代码的人工智能应用

本章内容：

- Power Platform
- Copilot
- AI Builder

<br/>
<br/>

## Power Platform

通过生成式 AI 增强低代码开发和应用是 Power Platform 的关注领域。目标是让每个人都能够构建人工智能驱动的应用程序、网站、仪表盘并利用人工智能实现流程自动化。

Power Platform 包含五个关键产品：Power Apps、Power Automate、Power BI、Power Pages 和 Power Virtual Agent。

