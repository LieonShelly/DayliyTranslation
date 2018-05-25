[原文](https://medium.com/flawless-app-stories/detecting-avengers-superheroes-in-your-ios-app-with-ibm-watson-and-coreml-fe38e493a4d1)

# Machine Learning in iOS: IBM Watson and CoreML

# iOS深度学习：IBM Watson 与 CoreML

Apple introduced CoreML in WWDC 2017, and it is a great deal. 
CoreML is a machine learning framework used in many Apple products, like Siri, Camera, Keyboard Dictation, etc.

 Apple在2017年的WWDC推出了CoreML框架，这无疑是个伟大的决策，CoreML是一个深度学习的框架，被大量的用于苹果相关的产品，比如苹果Siri，相机，键盘听写等等。
 
 It’s the foundation for Vision and Natural language processing. The cool stuff about CoreML is that it can use a pre-trained model to work offline. Apple has provided lots of pre-trained models like MobileNet, SqueezeNet, Inception v3, VGG16 to help us with image recognition tasks, especially detecting dominant objects in a scene.
 
CoreML是Vision和自然语言处理的基础框架。CoreML的优势是它可以使用预先训练好的模型来离线工作。苹果提供了大量的预先训练好的模型，比如MobileNetSqueezeNet, Inception v3, VGG16等等，来帮助我们进行图像识别。提供的模型主要是在服务于检测场景。
 
The job of CoreML is simply predicting data based on the models. If the provided trained models do not suit our needs, we can train the model with our own dataset. There are frameworks like Keras, TensorFlow, Caffe or the simplified turicreate, but those requires understanding of machine learning and computer power to train the models. An easy (and costly approach) is to use cloud services, like Google Cloud Vision, IBM Watson, Microsoft Azure Cognitive Service, Amazon Rekognition, … to simplify the tasks. These services usually host the full models, and retrain them, so we just need to make HTTP requests to get information about classified objects. Some offer the ability to extend the models by allowing us to upload our data set and define classifiers.

CoreML所做的工作仅仅是基于模型来预测数据。如果所提供的训练模型不满足我们的需求，我们只需要通过数据集来训练模型。有像Keras，TensorFlow，Caffe或简化的turicsreate这样的框架，但那些需要了解机器学习和计算机能力来训练模型。但有相对简单的方法就是使用云服务来简化这些工作，比如 Google Cloud Vision, IBM Watson, Microsoft Azure Cognitive Service, Amazon Rekognition。 这些服务通常托管了整个模型，并重新训练了它们，因此我们只需要发送HTTP请求去获取相关分类好的模型。 有些提供了扩展模型的功能，允许我们删除数据并定义分类器

In this guide, we will explore IBM Watson Service, especially its Visual Recognition feature to help us train a custom dataset of superheroes images, then consume the trained model in our iOS app via CoreML.

在本指南中，我们将探索IBM Watson Service，特别是其视觉识别功能，以帮助我们训练超级英雄图像的自定义数据集，然后通过COreML在我们的iOS应用程序中使用经过训练好的模型

# IBM Watson Service

Just recently Apple has announced partnership with IBM Watson that makes it easier to get started with machine learning.

就在最近，Apple宣布与IBM Watson合作，这使得机械学习开始变得更加容易

>>You can build apps that leverage Watson models on iPhone and iPad, 
>>even when offline. Your apps can quickly analyze images, accurately 
>>classify visual content, and easily train models using Watson Services

>>即使在离线的情况下，您也可以再iPhone和iPAd上构建Watson模型的应用程序，您的应用程
>>序可以快速的分析图像，准确分类视觉内容，并使用Watson Services轻松训练模型

So let’s try it first to see how it goes. The film Avengers Infinity War is popular on the cinema now and the superheroes still bumping around our heads. Maybe some of your friends won’t recognise those lots of superheroes in the movie. So let’s have a fun time by building an app that can help recognise superheroes.

所以首先看下我们的功能是个什么样的。近来，复仇者联盟这部电影在院线非常的火热，超级英雄们在脑海中回荡。但是你的朋友们可能并不能完全记得电影中的大量的超级英雄。因此，我们可以构建一个应用来帮助他们识别这些超级英雄们。

Playing with cloud services is straightforward. We pay some money for the service, upload superheroes pictures, train on the cloud, get the trained model to the app and enjoy the magic 😎.

玩云服务很简单。我们付一些钱给云服务，上传超级英雄的图片，在云服务器上训练模型，从app中获取训练好的模型，最后见证云服务带来的神器效果

I’ve been reading some articles and trying IBM Cloud but somehow I still find it confusing. Mostly because it offers many services, terminology changes, deprecated beta tools, paths that lead to the same location, interchange use of services and resources, apps and projects. In this post, I will try to clear things up based on my understanding. Feedback are welcome.

我虽然阅读了一些关于如何IBM Cloud的文章，但是我仍然存在一些疑惑。主要是因为它提供了很多的服务，术语更改，老套的测试工具，导致相同的位置的路径，交互使用服务和资源，应用程序和项目。在这篇文章中我将根据我的理解来阐述这些事情。欢迎反馈。

Bluemix vs IBM Cloud Bluemix is a cloud platform as a service which helps to build and run applications on the cloud. As of October 2017, Bluemix is IBM Cloud, although we still need to work with this domain https://console.bluemix.net. IBM Cloud offers many products, for security, analytics, storage, AI, …

Bluemix 是一个云服务平台，助力与在云上构建和运行应用程序。截止于2017年10月，Bluemix 是 IBM Cloud，尽管我们仍需要使用此域名https://console.bluemix.net.  IBM Cloud提供了许多产品，用于安全，数据分析，数据存储，AI等等。

>>>IBM’s one-stop cloud computing shop provides all the cloud solutions 
>>>and IBM cloud tools you need

>>>IBM的一站式云计算商店提供您需要的所有云解决方案和IBM云工具。

Data Science and Watson Studio
数据科学与Watson Studio

One of many products running on IBM Cloud is IBM Watson for natural language processing, visual recognition and machine learning. We will use Watson Studio to “build, train, deploy and manage AI models, and prepare and analyze data, in a single, integrated environment.” It is initially called Data Science. Think of it like a front-end web page with lots of tools for interacting with Watson services. Let’s get started!

运行在IBM Cloud上的许多产品之一是用于自然语言处理，视觉识别和机械学习的IBM Watson.我们将使用Watson Studio在单一的集成环境中构建，训练，部署和管理AI模型，并准备和分析数据。它最初被称为数据科学，可以把它想象成一个前端网页，其中有许多与Watson services交互的工具。让我们开始吧。

>>>Watson Studio is a free workspace where you can seamlessly create, 
>>>evaluate, and manage your custom models.

>>>Watson Studio 是一个免费的工作室，你可以无缝的创建，评估与管理你自定义的模型

## Step 1: Sign up for IBM Cloud

## 注册IBM Cloud

Go to IBM Cloud and click on the button Sign up for IBM Cloud.

访问IBM Cloud点击注册按钮进行IBM Cloud注册

After you successfully sign up, head over to Watson Studio and click Sign in.

注册成功之后，返回Watson Studio首页进行登录

It will take you to https://dataplatform.ibm.com/home. I think for historical reason, the domain name and the product name don’t match 🙄.

然后页面将跳转到 https://dataplatform.ibm.com/home.我认为应该是之前的一些原因，导致了域名和产品名称不匹配

## Step 2: Create new project for Visual Recognition

## 创建一个视觉识别的新工程

Click New project and select the tool Visual Recognition, which is what we need to do classification on superheroes pictures.

点击新建工程并选择Visual Recognition工具，这是我们需要对超级英雄图片分类的工具

>>>Quickly and accurately tag, classify and train visual content 
>>>using machine learning.

>>>使用机械学习，快速准确地对视觉内容进行标记，分类和训练

A project is said to contain a set of tools and services. Let’s name it Avengers. It comes with a storage.

一个项目包含一套工具和服务，让我们将它命名为复仇者。它配有一个存储

## Step 3: Create service

## 创建服务

For me, after creating new project, it goes to the page Default Custom Modeland says the project needs to associate with a service. I think the project should create a service and associate with it by default 🙄

我认为，创建一个新工程之后，将会跳转到一个自定义模式的页面，该项目需要与服务关联，我认为项目应该创建一个服务并默认与其关联

Go back to the front page, select your Avengers project from Projects menu, and go to Settings tab.

回到前端页面，从工程菜单中选择你复仇者项目，然后进入设置页面





# 陌生单词

	dominant 主要，主导 predicting 预测 consume 消耗 使用
	straightforward 直截了当 one-stop 一站式 seamlessly

