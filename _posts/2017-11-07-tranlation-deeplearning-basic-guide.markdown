---
layout:     post
title:      "「Translation」深度学习基础指南"
subtitle:   "“人工智能”这个词每天被到处乱用。你听到开发者们说他们要学习人工智能。你也会听到管理者说他们要把人工智能技术应用他们的服务中。但他们往往并不了解人工智能...."
date:       2017-11-07 16:57:00
author:     "YAKANG GAO"
header-img: "img/post-bg-07.jpg"
---

![](http://upload-images.jianshu.io/upload_images/129905-1bcd5f58afdf94a4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
本篇文章是我在开源中国翻译频道翻译的文章，因为这篇基本上全是我翻译的所以就拿到了简书上。[开源中国原文请点击此处](https://www.oschina.net/translate/how-deep-learning-works)
---
**正文**

人工智能和机器学习是现如今最热门的话题了。

“人工智能”这个词每天被到处乱用。你听到开发者们说他们要学习人工智能。你也会听到管理者说他们要把人工智能技术应用他们的服务中。但他们往往并不了解人工智能。
读完这篇文章，你将会了解到人工智能和机器学习的基础知识。更重要的是你将会了解到最流行的一种机器学习技术——深度学习是如何工作的。
这篇指引是面向大众的，所以文中不会出现高等数学的内容。

## 背景知识

理解学习深度的第一步是掌握重要术语之间的差异。

#### 人工智能与机器学习

> 人工智能是人类智能在计算机中的复现。

在人工智能研究的起步阶段，研究人员正试图在计算机上复现人类智能的具体任务 —— 比如玩游戏。

他们引入了计算机需要遵守的大量规则。计算机有一个具体的可能行动清单，并根据这些规则做出决定。

> 机器学习指的是机器学习使用大数据集而不是硬编码规则的能力。

机器学习允许计算机自己学习。这种学习利用了现代计算机的处理能力，它可以很容易地处理大型数据集。

#### 监督学习与无监督学习

> 监督学习涉及使用具有输入和期望输出的标记数据集。

当你使用监督学习训练人工智能时，你给计算机一个输入内容并告诉计算机预期的输出内容。

如果人工智能产生的输出是错误的，它将调整计算方法。这个过程在数据集上迭代完成，直到 AI 不再犯错误为止。

监督学习的一个例子是天气预报人工智能。它学习用历史数据预测天气。训练数据有输入（压力、湿度、风速）和输出（温度）。

> 无监督学习是使用没有特定结构的数据集进行机器学习的任务。

当你使用无监督学习训练人工智能时，你让人工智能对数据进行逻辑分类。

无监督学习的一个例子是一个电子商务网站的行为预测人工智能。它不会通过使用标记的输入和输出数据集来学习。

相反，它将创建自己的输入数据分类。它会告诉你哪种用户最有可能购买不同的产品。



## 那么，深度学习是怎样工作的？

现在你有了足够的预备知识去理解深度学习是什么，以及深度学习是怎样工作的。

深度学习是一种**机器学习**的方法。深度学习允许我们训练一个人工智能系统通过给定一个输入数据集，去预测输出数据。监督和无监督学习都可以运用到训练人工智能上。

我们将会通过一个**飞机票价评估服务**来学习深度学习是如何工作的。我们会使用监督学习方法来训练这个系统。

我们希望我们的飞机票价评估系统通过以下数据来预测票价（为了系统的简单，我们没有使用返程票价的数据）：

*   始发机场

*   目的地机场

*   启程日期

*   航空公司


####神经网络

让我们看看人工智能的大脑。

像动物一样，我们的评估系统 AI 的大脑有神经元。它们以圆圈表示。这些神经元之间相互连接。
### ![](http://upload-images.jianshu.io/upload_images/129905-058d2fe9922a807a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

神经元被分成三组不同的层级：
1. 输入层
2. 隐含层
3. 输出层

**输入层**接收输入数据。在我们的例子中，输入层有四个神经元：始发机场、目的地机场、启程日期和航空公司。输入层将输入传递给第一个隐含层。

**隐含层**对输入数据进行数学计算。创建神经网络所面临的挑战之一是决定隐含层的数目，以及每个层的神经元数目。

深度学习中的“**深度**”指的就是因为它有一个以上的隐藏层。

**输出层**返回输出数据。在我们的例子中，它给了我们对于价格的预测。
![](http://upload-images.jianshu.io/upload_images/129905-684a020f1b7af4f8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

那么深度学习系统是如何给出对于价格的预测呢？

这就是深度学习的神奇之处。

在每个神经元之间都有一个“**权重**”。这个权重描述了输入数据的重要性，初始权重是随机设置的。

当预测机票价格的时候，启程日期是一个重要的因素。因此，启程日期神经元连接将会有高的权重。
![](http://upload-images.jianshu.io/upload_images/129905-f94f7d517d7ed34d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

每个神经元都有一个[激活函数](https://en.wikipedia.org/wiki/Activation_function)。如果没有数学推倒，这些函数很难理解。

简单来说，激活函数的目的是标准化来自神经元的输出。

一旦一个数据集被传入到神经网络的所有的层，激活函数将通过输出层返回数据。

这没什么难的，对吧？



#### 训练神经网络

训练人工智能是深度学习中最困难的部分。为什么？

1.  你需要一个**很大的数据集**。

2.  你需要**强大**的**计算能力**。

对于我们的机票价格估计系统，我们需要找到机票价格的历史数据。由于机场和起飞日期的可能组合有很多，因此我们需要一个非常大的机票价格清单。

为了训练人工智能，我们需要从数据集中输入它，并将其输出与数据集的输出进行比较。由于人工智能还未经训练，其输出将是错误的。

一旦我们完成了整个数据集，我们就可以创建一个函数，告诉我们人工智能系统的输出与我们需要的输出的差距有多大。这个函数叫做成本函数。

理想情况下，我们希望我们的成本函数为零。这要求人工智能的输出数据和数据集的输出数据完全相同。




#### 如何降低成本函数？

我们可以改变神经元之间的权重。我们可以随机地改变权重，直到我们的成本函数是低的，但这个方法效率不高。

相反，我们将使用一种称为[梯度下降](https://en.wikipedia.org/wiki/Gradient_descent)的技术。

梯度下降是一种允许我们找到函数最小值的技术。在我们的例子中，我们在寻找最小的成本函数。

它通过在**每次数据集迭代后**以较小的增量**改变权重**。在一定的权重集上通过计算成本函数曲线的导数（或梯度），我们可以看到降低成本函数的方向是什么。

![](http://upload-images.jianshu.io/upload_images/129905-4ece9b180a00c136.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

为了最小化成本函数，你需要多次迭代你的数据集。这就是为什么你需要大量的计算能力。

使用梯度下降调整权重是**自动完成**的。这就是深度学习的魔力！

一旦我们训练了我们的机票价格估计AI，我们就可以用它来预测机票未来的价格。



## 哪里可以学到更多深度学习知识？

还有许多其他类型的神经网络：用于[计算机视觉](https://en.wikipedia.org/wiki/Computer_vision)的[卷积神经网络](https://en.wikipedia.org/wiki/Convolutional_neural_network)和用于[自然语言处理](https://en.wikipedia.org/wiki/Natural_language_processing)的[递归神经网络](https://en.wikipedia.org/wiki/Recurrent_neural_network)。
[](https://en.wikipedia.org/wiki/Recurrent_neural_network)如果你想学习深度学习的技术方面，我建议你参加一个在线网络课程。

目前，学习深度学习最好的课程是吴恩达（Andrew Ng）的深度学习课程。如果你对获得证书不感兴趣，你不需要为课程付钱。你可以免费旁听。

如果你有任何的问题，或者需要更多的技术概念的解释，请在下面询问。

## 总结
- 深度学习利用神经网络来模仿动物的智力。

- 一个神经网络钟有三种类型的层级神经元：输入层，隐含层和输出层。

- 神经元之间的联系与权重有关，权重来指示输入值的重要性。

- 神经元在数据上应用一个激活函数来“标准化”神经元的输出。

- 训练一个神经网络，你需要一个很大数据集。

- 迭代数据集和比较输出将产生一个成本函数，表明人工智能与实际输出有多大的差距。

- 每次迭代后，通过梯度下降调整神经元之间的权值，降低代价函数。

如果你喜欢这篇文章请给我一些掌声，让更多的人看到它。谢谢!
