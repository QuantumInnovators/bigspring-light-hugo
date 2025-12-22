---
title: "Nano Banana Pro 终极开发指南"
date: 2025-12-22T10:25:07+08:00
type: "featured"
image: "http://writesome.oss-cn-chengdu.aliyuncs.com/20151222-nano-banana-pro/nano-banana-pro.jpeg"
description: "探索这个下一代 AI 模型如何通过“思考能力”、实时搜索落地（Search Grounding）以及惊艳的 4K 画质，助你构建复杂而富有创意的应用程序。"
---

原文：[Complete Developer Tutorial for Nano Banana Pro](https://x.com/GoogleAIStudio/status/1992267030050083091)

探索这个下一代 AI 模型如何通过“思考能力”、实时搜索落地（Search Grounding）以及惊艳的 4K 画质，助你构建复杂而富有创意的应用程序。

如果说 Flash 模型（Nano Banana）是速度与性价比的王者，那么 Pro 版本则是一位拥有“大脑”的艺术家——它具备推理能力，能结合实时搜索结果，还能输出电影级的 4K 高清图像。是时候用它来搞点大事了！

本指南将带你通过 [Gemini Developer API](https://ai.google.dev/) 全面解锁 Nano Banana Pro 的高级功能。

我们将涵盖以下内容：

- 在 Google AI Studio 中试玩 Nano Banana Pro

- 项目环境搭建

- 初始化客户端

- 基础生成（经典玩法）

- “思考”过程（The "Thinking" Process）

- 搜索落地（Search Grounding）

- 4K 超高清生成

- 多语言能力

- 高级图像混合

- ro 版独家演示

- 最佳实践与提示词技巧

注意：如果你想直接上手体验，可以查看互动版的 [Python使用手册](https://colab.sandbox.google.com/github/google-gemini/cookbook/blob/main/quickstarts/Get_Started_Nano_Banana.ipynb) 或者 AI Studio 的 [Javascript 指南](https://aistudio.google.com/apps/bundled/get_started_image_out?fullscreenApplet=true&showPreview=true&showAssistant=true)。

## 1. 在 Google AI Studio 中试玩 Nano Banana Pro
虽然普通用户可以在 [Gemini app](https://gemini.google.com/app) 里直接使用 Nano Banana Pro，但对于开发者来说，原型设计和测试提示词（Prompt）的最佳场所是 [Google AI Studio](https://aistudio.google.com/apps)。AI Studio 就像是一个游乐场，你可以在写任何代码之前先试验各种 AI 模型，它也是构建 Gemini API 应用的入口。

要开始使用，请前往 [aistudio.google.com](https://aistudio.google.com/)，登录你的 Google 账号，然后从模型选择器中选择 Nano Banana Pro (Gemini 3 Pro Image)。

与 Nano-Banana（Flash 版）不同，Pro 版本没有免费层。这意味着你需要选择一个已启用计费功能的 API 密钥（详见下方的“项目环境搭建”部分）。
![img2](http://writesome.oss-cn-chengdu.aliyuncs.com/20151222-nano-banana-pro/nano-banana-pro-2.jpeg)

> 小贴士：你也可以直接在 AI Studio 的 ai.studio/apps 页面，利用“凭感觉编程”（Vibe Coding，指不写具体代码，而是通过>自然语言描述需求让 AI 自动生成代码）来构建 Nano Banana Web 应用；或者去探索代码，改编现有的 应用案例。

## 2. 项目环境搭建
要跟随本指南操作，你需要准备好以下东西：

- 一个来自 Google AI Studio 的 API 密钥。

- 为你的项目设置好计费功能。

- 安装好 Google Gen AI SDK，支持 Python 或 JavaScript/TypeScript。

如果你已经是 Gemini API 的资深玩家，这些都搞定了，那就直接跳到下一节。否则，请按以下步骤操作：

### 步骤 A：获取 API 密钥
当你首次登录 AI Studio 时，系统应该会自动为你创建一个 Google Cloud 项目和一个 API 密钥。

打开 API 密钥管理页面，点击“复制”图标保存你的密钥。
![img3](http://writesome.oss-cn-chengdu.aliyuncs.com/20151222-nano-banana-pro/nano-banana-pro-4.jpeg)

### 步骤 B：启用计费
由于 Nano Banana Pro 没有免费午餐，你必须在 Google Cloud 项目中启用计费。

在 API 密钥管理页面，点击你项目旁边的“设置计费（Set up billing）”，然后按照屏幕提示操作即可。
![img4](http://writesome.oss-cn-chengdu.aliyuncs.com/20151222-nano-banana-pro/nano-banana-pro-3.jpeg)

### Nano Banana Pro 贵吗？

使用 Nano Banana Pro 生成图像确实比 Flash 版本贵一些，尤其是生成 4K 图片时。截至本文发布时，生成一张 1K 或 2K 的图片费用为 0.134 美元，而 4K 图片则为 0.24 美元（此外还需加上输入 Token 和文本输出的 Token 费用）。

请务必查阅文档中的 定价页面 以获取最新信息。

省钱妙招：如果你想节省 50% 的生成成本，可以使用 [Batch API（批量 API）](https://ai.google.dev/gemini-api/docs/image-generation?batch=file&hl=zh-cn#batch-api)。代价是你可能需要等待最多 24 小时才能拿到结果，适合不急的任务。

### 步骤 C：安装 SDK
根据你偏好的编程语言选择安装 SDK。

Python:
```python
pip install -U google-genai
# 安装 Pillow 库用于图像处理
pip install Pillow
```

JavaScript / TypeScript:
```javascript
npm install @google/genai
```
> 注意：下文中的示例将使用 Python SDK 进行演示。如果你使用 JavaScript，可以在这个 JS 笔记本 中找到对应的代码片段。

### 3. 初始化客户端
要使用 Pro 模型，你需要指定模型 ID gemini-3-pro-image-preview。

```python
from google import genai
from google.genai import types

# 初始化客户端
client = genai.Client(api_key="YOUR_API_KEY")

# 设置模型 ID
PRO_MODEL_ID = "gemini-3-pro-image-preview"
```

### 4. 基础生成（经典玩法）
在搞那些花哨的操作之前，我们先来看个标准的生成流程。你可以通过 `response_modalities`（控制是返回文本加图片，还是只返回图片）以及 `aspect_ratio`（长宽比）来控制输出。

```python
prompt = "Create a photorealistic image of a siamese cat with a green left eye and a blue right one"
# 创建一张逼真的暹罗猫照片，左眼绿色，右眼蓝色
aspect_ratio = "16:9" # 可选: "1:1","2:3","3:2","3:4","4:3","4:5","5:4","9:16","16:9" 或 "21:9"

response = client.models.generate_content(
    model=PRO_MODEL_ID,
    contents=prompt,
    config=types.GenerateContentConfig(
        response_modalities=['Text', 'Image'], # 或者只选 ['Image']
        image_config=types.ImageConfig(
            aspect_ratio=aspect_ratio,
        )
    )
)

# 显示并保存图片
for part in response.parts:
    if image:= part.as_image():
        image.save("cat.png")
```
![img5](http://writesome.oss-cn-chengdu.aliyuncs.com/20151222-nano-banana-pro/nano-banana-pro-5.jpeg)
聊天模式也是一个不错的选择（如果你需要进行多轮修改，我强烈推荐这种方式）。可以参考第 8 个示例“多语言 Banana”。

### 5. “思考”过程（The "Thinking" Process）
Nano Banana Pro 不只是个画师，它还是个思想家。这意味着在开始动笔之前，它会先仔细推敲你那些复杂、甚至有点刁钻的提示词。最棒的是，你可以“偷看”它的大脑！

要启用此功能，只需在 `thinking_config` 中设置 `include_thoughts=True`。

```python
prompt = "Create an unusual but realistic image that might go viral"
# 创建一张不寻常但逼真、可能会病毒式传播的图片
aspect_ratio = "16:9"

response = client.models.generate_content(
    model=PRO_MODEL_ID,
    contents=prompt,
    config=types.GenerateContentConfig(
        response_modalities=['Text', 'Image'],
        image_config=types.ImageConfig(
            aspect_ratio=aspect_ratio,
        ),
        thinking_config=types.ThinkingConfig(
            include_thoughts=True # 启用思考过程
        )
    )
)

# 显示思考内容和图片
for part in response.parts:
  if part.thought:
    print(f"Thought: {part.text}") # 打印思考过程
  elif image:= part.as_image():
    image.save("viral.png")
```

你可能会得到类似这样的输出：
```python
## Imagining Llama Commuters (构思羊驼通勤者)

I'm focusing on the llamas now. The goal is to capture them as
daily commuters on a bustling bus in La Paz, Bolivia. My plan
involves a vintage bus crammed with amused passengers. The image
will highlight details like one llama looking out the window,
another interacting with a passenger, all while people take
photos.
(我现在专注于羊驼。目标是捕捉它们作为日常通勤者在玻利维亚拉巴斯拥挤的公交车上的场景...)

[IMAGE]

## Visualizing the Concept (视觉化概念)

I'm now fully immersed in the requested scenario. My primary
focus is on the "unusual yet realistic" aspects. The scene is
starting to take shape with the key elements established.
(我现在完全沉浸在这个场景中。我的主要重点是“不寻常但逼真”这一方面...)
```
![img6](http://writesome.oss-cn-chengdu.aliyuncs.com/20151222-nano-banana-pro/nano-banana-pro-6.jpeg)

这种透明度（Transparency）能帮你理解模型是如何解读你的需求的。这就像是在和你的专属艺术家进行对话！

### 6. 搜索落地（Search Grounding）—— 实时魔法
这是最改变游戏规则的功能之一：搜索落地（Search Grounding）。Nano Banana Pro 不会活在过去的数据里；它可以访问 Google 搜索的实时数据，生成准确、即时的图像。想要当天的天气图？没问题。

例如，你可以让它将未来的天气预报可视化：

```python
prompt = "Visualize the current weather forecast for the next 5 days in Tokyo as a clean, modern weather chart. add a visual on what i should wear each day"
# 将东京未来5天的天气预报可视化为一张简洁现代的天气图表，并加上每天穿衣建议的视觉元素

response = client.models.generate_content(
    model=PRO_MODEL_ID,
    contents=prompt,
    config=types.GenerateContentConfig(
        response_modalities=['Text', 'Image'],
        image_config=types.ImageConfig(
            aspect_ratio="16:9",
        ),
        tools=[{"google_search": {}}] # 启用 Google 搜索
    )
)

# 保存图片
for part in response.parts:
    if image:= part.as_image():
        image.save("weather.png")

# 显示来源（这一步必须做，这是规范）
print(response.candidates[0].grounding_metadata.search_entry_point.rendered_content)
```
![img7](http://writesome.oss-cn-chengdu.aliyuncs.com/20151222-nano-banana-pro/nano-banana-pro-7.jpeg)

### 7. 搞大的：4K 超高清生成
需要打印级别的画质？Nano Banana Pro 支持 4K 分辨率。因为有时候，大就是好。

```python
prompt = "A photo of an oak tree experiencing every season"
# 一张展示了一棵橡树经历四季变迁的照片
resolution = "4K" # 选项: "1K", "2K", "4K", 注意必须大写

response = client.models.generate_content(
    model=PRO_MODEL_ID,
    contents=prompt,
    config=types.GenerateContentConfig(
        response_modalities=['Text', 'Image'],
        image_config=types.ImageConfig(
            aspect_ratio="1:1",
            image_size=resolution
        )
    )
)
```
![img8](http://writesome.oss-cn-chengdu.aliyuncs.com/20151222-nano-banana-pro/nano-banana-pro-8.jpeg)
> 注意：4K 生成成本较高，请按需使用！

### 8. 多语言 Banana（Multilingual Capabilities）
这个模型不仅能生成图像，还能在图像中生成甚至翻译超过十几种语言的文本。它基本上就是你眼睛的通用翻译机。

```python
# 生成一张西班牙语的信息图
message = "Make an infographic explaining Einstein's theory of General Relativity suitable for a 6th grader in Spanish"
# 制作一张适合六年级学生的西班牙语信息图，解释爱因斯坦的广义相对论

response = chat.send_message(message,
    config=types.GenerateContentConfig(
        image_config=types.ImageConfig(aspect_ratio="16:9")
    )
)

# 保存图片
for part in response.parts:
    if image:= part.as_image():
        image.save("relativity.png")
```
![img9](http://writesome.oss-cn-chengdu.aliyuncs.com/20151222-nano-banana-pro/nano-banana-pro-9.jpeg)

```python
# 将其翻译成日语
message = "Translate this infographic in Japanese, keeping everything else the same"
# 将此信息图翻译成日语，保持其他所有内容不变
response = chat.send_message(message)

# 保存图片
for part in response.parts:
    if image:= part.as_image():
        image.save("relativity_JP.png")
```
![img10](http://writesome.oss-cn-chengdu.aliyuncs.com/20151222-nano-banana-pro/nano-banana-pro-10.jpeg)

### 9. 混搭大法！（高级图像混合）
Flash 模型最多只能混合 3 张图片，但 Pro 模型能处理高达 14 张！这简直是在开派对。非常适合创建复杂的拼贴画或展示你的整个产品线。

```python
# 混合多张图片
response = client.models.generate_content(
    model=PRO_MODEL_ID,
    contents=[
        "An office group photo of these people, they are making funny faces.",
        # 这些人的办公室合影，他们正在做鬼脸
        PIL.Image.open('John.png'),
        PIL.Image.open('Jane.png'),
        # ... 最多可添加 14 张图片
    ],
)

# 保存图片
for part in response.parts:
    if image:= part.as_image():
        image.save("group_picture.png")
```
![img11](http://writesome.oss-cn-chengdu.aliyuncs.com/20151222-nano-banana-pro/nano-banana-pro-11.jpeg)
> 注意：如果你希望人物保持极高的保真度，建议限制在 5 人以内，但这对于一个派对之夜来说已经绰绰有余了！

### 10. Pro 版独家演示
以下是只有 Nano Banana Pro 才能实现的一些效果演示。准备好大吃一惊吧：

#### 个性化像素艺术（搜索落地）
> Prompt: "Search the web then generate an image of isometric perspective, detailed pixel art that shows the career of Guillaume Vernade" （搜索网络，然后生成一张等轴视角的详细像素艺术图，展示 Guillaume Vernade 的职业生涯）

这利用了搜索落地功能来查找关于某人的具体信息，并以特定的艺术风格将其可视化。
![img12](http://writesome.oss-cn-chengdu.aliyuncs.com/20151222-nano-banana-pro/nano-banana-pro-12.jpeg)

#### 复杂文本集成
> Prompt: "Show me an infographic about how sonnets work, using a sonnet about bananas written in it, along with a lengthy literary analysis of the poem. Good vintage aesthetics" （展示一张关于十四行诗如何运作的信息图，其中要包含一首关于香蕉的十四行诗，以及对该诗的长篇文学分析。采用好看的复古美学风格。）

模型可以生成连贯的长文本，并将其完美地融入复杂的排版布局中。
![img13](http://writesome.oss-cn-chengdu.aliyuncs.com/20151222-nano-banana-pro/nano-banana-pro-13.jpeg)

#### 高保真样机展示
> Prompt: "A photo of a program for the Broadway show about TCG players on a nice theater seat, it's professional and well made, glossy, we can see the cover and a page showing a photo of the stage." （一张关于集换式卡牌玩家的百老汇演出节目单的照片，放在漂亮的剧院座椅上，制作专业精良，有光泽感，我们可以看到封面和展示舞台照片的内页。）

创建具有准确光照和纹理的印刷品照片级样机。
![img14](http://writesome.oss-cn-chengdu.aliyuncs.com/20151222-nano-banana-pro/nano-banana-pro-14.jpeg)

### 11. Nano Banana 和 Nano Banana Pro 的最佳实践与提示词技巧
要想用 Nano Banana 模型获得最佳效果，请遵循以下提示词指南：

- 极度具体（Be Hyper-Specific）： 你对主体、颜色、光线和构图的描述越详细，你对输出的控制权就越大。

- 提供背景和意图（Context and Intent）： 解释图片的用途或想要的情绪。模型对语境的理解会影响它的创作选择。

- 迭代与优化（Iterate and Refine）： 别指望一次就能完美。利用模型的对话能力进行增量修改，逐步完善你的图片。

- 分步指令（Step-by-Step Instructions）： 对于复杂的场景，将你的提示词拆解为一系列清晰、顺序的指令。

- 正向描述（Positive Framing）： 尽量少用“不要汽车”这样的负面提示，而是正向描述你想要的场景：“一条空荡荡、荒凉的街道，没有任何交通迹象。”

- 掌控镜头（Control the Camera）： 使用摄影和电影术语来指导构图，例如“广角镜头（wide-angle shot）”、“微距镜头（macro shot）”或“低角度视角（low-angle perspective）”。

- 善用搜索落地： 当你知道想要模型使用实时或现实世界的数据时，要非常明确。比如，“搜索关于里昂奥林匹克队上一场比赛的信息并制作一张信息图”，效果会比仅仅说“一张里昂奥林匹克队近期比赛的信息图”要好得多（后者可能也能行，但别碰运气）。

- 使用 Batch API 降低成本并获得更多配额： Batch API 是一种将少量或大量请求打包发送的方法。处理可能需要长达 24 小时，但作为交换，你可以节省 50% 的生成成本，而且配额也更高！

如需深入了解最佳实践，请查阅文档中的 [提示词指南](https://ai.google.dev/gemini-api/docs/image-generation?hl=zh-cn#prompt-guide) 以及官方博客上发布的 Nano Banana [提示词最佳实践](https://developers.googleblog.com/en/how-to-prompt-gemini-2-5-flash-image-generation-for-the-best-results/)。

## 总结
![img15](http://writesome.oss-cn-chengdu.aliyuncs.com/20151222-nano-banana-pro/nano-banana-pro-15.jpeg)
Nano Banana Pro (Gemini 3 Pro Image) 开启了 AI 图像生成的新篇章。凭借其思考、搜索和 4K 渲染能力，它不仅是严肃创作者的利器，也是极致乐趣的来源。

准备好尝试了吗？前往 Google AI Studio，体验或定制我们的 Apps，或者查看 使用手册。