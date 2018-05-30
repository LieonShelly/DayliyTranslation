[原文](https://medium.com/flawless-app-stories/machine-learning-in-ios-turi-create-and-coreml-5ddce0dc8e26)

## Machine Learning in iOS: Turi Create and CoreML

## iOS深度学习：Turi Create 与 CoreML

[Machine learning](https://developer.apple.com/machine-learning/)on iOS became trendy when Apple introduced CoreML at WWDC 2017. It can be intimidating at first with all the new concepts, frameworks and models, so we started with cloud services to learn basic machine learning, dataset preparation and training in[IBM Watson and CoreML](https://medium.com/flawless-app-stories/detecting-avengers-superheroes-in-your-ios-app-with-ibm-watson-and-coreml-fe38e493a4d1)(Part 1) and[Azure Custom Vision and CoreML](https://medium.com/p/645e93f35eee/edit)(Part2).

自WWDC2017，Apple引入了CoreML框架，iOS机械学习变得火热起来。首先，所有新的概念，框架和模型都可能令人感到恐惧，因此我们从[IBM Watson and CoreML](https://medium.com/flawless-app-stories/detecting-avengers-superheroes-in-your-ios-app-with-ibm-watson-and-coreml-fe38e493a4d1)和[Azure Custom Vision and CoreML](https://medium.com/p/645e93f35eee/edit)云服务开始学习基本的机械学习，数据集准备和训练

Using[cloud services](http://machinethink.net/blog/machine-learning-device-or-cloud/)is convenient. However, it costs money and has certain limitation on how we want to customise our model training. And not to mention the privacy issue with all of the user’s images being uploaded to someone’s servers.

使用云服务固然很便捷，但是，对于我们想要定制模型训练要进行付费并且有一定的限制。更不用说将用户的图片上传到某个服务器所带来的隐私问题。

Today we try a less easy approach: using a framework to train model locally. There are many frameworks like TensorFlow, Keras, Scikit learn, Caffe, etc. We will work with Turi Create because it’s relatively easy to set up and it can export CoreML compatible model on the fly. Also, Turi Create gives us better understanding of some machine learning tasks and concepts, those will be the foundation for us to use other advanced frameworks.

今天我们尝试一个不那么简单的方法：使用框架在本地训练模型。有很多的机械学习的框架比如TensorFlow, Keras, Scikit learn, Caffe等等。我们将使用Turi Create框架，因为它设置起来相对简单，并且能够导出与CoreML相兼容的模型。同时，Turi Create可以让我们更好的理解一些机械学习任务和一些概念。这些将会是我们使用其他高级的机械学习框架的基础。

In this tutorial we will build another Avengers superheroes classification using Turi Create. Let’s dive in!

这篇教程中我们建使用Turi Create创建另一个超级英雄的分类。开始吧

### Turi Create

### Turi Create

Turi was initially a machine learning startup in Seattle, known for its product [GraphLab](https://github.com/turi-code/GraphLab-Create-SDK) that simplifies common machine learning tasks. In 2016 Apple [acquired](https://9to5mac.com/2016/08/05/apple-acquires-turi-machine-learning-artificial-intelligence/) Turi and later in December 2017 made [Turi Create](https://github.com/apple/turicreate) as an open source project.

Turi最初是西雅图的一家机器学习机构，以其产品GraphLab闻名，简化了常见的机器学习任务。 在2016年，苹果公司收购了Turi，并于2017年12月后来将Turi Create作为开源项目。

> Turi Create simplifies the development of custom machine learning models. You don’t have to be a machine learning expert to add recommendations, object detection, image classification, image similarity or activity classification to your app.

> Turi Create简化了定制机器学习模型的开发。 您无需成为机器学习专家，即可向您的应用添加推荐内容，对象检测，图像分类，图像相似度或活动分类。

As I mentioned before, the framework is simple to use, visual, flexible and exportable to CoreML compatible models. So what you trained can be used right away in iOS, macOS, tvOS and watchOS apps without any extra conversion.

正如我之前所提及奥的，这个框架使用起来很简单，可视化，灵活，并且能够导出与CoreML兼容的模型。因此你不需额外的转换就可以将所训练的模型用于iOS，macOS，tvOS和watchOS App

Turi Create focuses on app domain, rather than model domain. It means that we don’t need to worry about constructing model architecture and connecting layers, instead we deal with high level APIs for the tasks at hands like loading images, training and evaluating. It also means that we don’t need to be machine learning experts to use it. For now the supported [tasks](https://apple.github.io/turicreate/docs/userguide/applications/) are:

Turi Create侧重于app端，而不是模型训练。这就意味这我我们不需要关心模型架构的构建和连接层，相反，我需要使用高层的API去处理这些任务，例如加载图像，训练和评估等任务。同时，这也意味着我不需要成为机械学习专家就可以使用Turi Create。就目前为止Turi Create支持的任务有：

- [Recommender systems](https://apple.github.io/turicreate/docs/userguide/recommender/)

- 推荐系统

- [Image classification](https://apple.github.io/turicreate/docs/userguide/image_classifier/)

- 图像分类

- [Image similarity](https://apple.github.io/turicreate/docs/userguide/image_similarity/)

- 图像相似度检查

- [Object detection](https://apple.github.io/turicreate/docs/userguide/object_detection/)

- 对象检查

- [Activity classification](https://apple.github.io/turicreate/docs/userguide/activity_classifier/)

- 活动分类

- [Text classifier](https://apple.github.io/turicreate/docs/userguide/text_classifier/)

- 文本分类器

In this tutorial, we use [Image classification](https://apple.github.io/turicreate/docs/userguide/image_classifier/) task to train custom model for our superheroes dataset. I have also put lots of hyperlinks to source code and reference in this post for you to explore further. Let’s take the first step now.

> Given an image, the goal of an image classifier is to assign it to one of a pre-determined number of labels.



这篇教程中，我们使用图像分类任务来训练我们的超级英雄数据集的自定义模型。我也在文中提供了很多超链接到源代码和引用的文章，供你进一步探索。现在我们开始第一步

> 给定图像，图像分类器的目标是将其分配给预定数量的标签之一

#### Step 1: Python

#### 步骤一：安装Python

Like many other machine learning frameworks, Turi Create is written in Python and we need to write some Python code to call its APIs, so some basic understanding of Python is required. There are [many ](https://www.learnpython.org/)[tutorials](https://medium.freecodecamp.org/learning-python-from-zero-to-hero-120ea540b567) [online](https://www.python.org/about/gettingstarted/).

像和很多的机械学习框架，Turi Create 也是用Python写的，我们需要写一些Python代码去调用Turi Create的API。因此需要对Python有一些的基本的认识。这是一些在线教程。



According to[system requirements](https://github.com/apple/turicreate#system-requirements), Python 2.7, 3.5, 3.6 are supported, this is confirmed in this[issue](https://github.com/apple/turicreate/issues/514)as from Turi Create 4.1. I’m using a MacBook with macOS High Sierra so I have Python 2.7 by default. Although there’s movement for[Python 3](https://wiki.python.org/moin/Python2orPython3), let’s use the system Python 2.7 for now.

根据系统要求，支持Python 2.7,3.5,3.6，这在Turi Create 4.1中得到了证实。 我正在使用MacOS高级Sierra的MacBook，所以我默认使用Python 2.7。 虽然Python 3有所变化，但现在让我们使用Python 2.7系统。

You can check Python version and its executable by running the following commands in terminal:

您可以通过在终端中运行以下命令来检查Python版本及其可执行文件

```
> python - version 
  Python 2.7.13 
> which python 
  /usr/local/bin/python 
```

If for some reasons you don’t have Python installed, you can install it[here](https://www.python.org/downloads/release/python-2714/). Python comes with`pip`, which is a package management system used to install and manage software packages written in Python. We need pip to install [turicreate](https://pypi.org/project/turicreate/), run the following command:

如果由于某些原因你没有安装Python，可以在这里安装它。 Python附带pip，这是一个用于安装和管理用Python编写的软件包的软件包管理系统。 我们需要pip来安装turicreate，运行以下命令：

```
pip install turicreate
```

Turi Create recommends using [virtualenv](https://virtualenv.pypa.io/en/stable/)or [Anaconda](https://www.anaconda.com/what-is-anaconda/) to create isolated Python environment. You are free to create virtual environment, but in this post we just merely execute Python scripts for simplicity.

Turi Create建议使用virtualenv或Anaconda创建独立的Python环境。 您可以自由创建虚拟环境，但在本文中，我们只是简单地执行Python脚本。

#### Step 2: Data set

#### 数据集

We use the same data set from [Machine Learning in iOS: IBM Watson and CoreML](https://medium.com/flawless-app-stories/detecting-avengers-superheroes-in-your-ios-app-with-ibm-watson-and-coreml-fe38e493a4d1)post. You can collect your own dataset or use ones in this GitHub[repo](https://github.com/onmyway133/Avengers). Eventually, we train with images of 4 superheroes: Ironman, Captain America, Spiderman and Thor. Images for each superhero lie in their own folder, the name of the folder can be seen as a label or a tag.



我们使用来自 [Machine Learning in iOS: IBM Watson and CoreML](https://medium.com/flawless-app-stories/detecting-avengers-superheroes-in-your-ios-app-with-ibm-watson-and-coreml-fe38e493a4d1)相同的数据集。 你可以收集你自己的数据集或者使用这个GitHub仓库中的数据集。 最终，我们训练了4名超级英雄的照片：钢铁侠，美国队长，蜘蛛侠和托尔。 每个超级英雄的图像位于他们自己的文件夹中，文件夹的名称可以被看作标签。
