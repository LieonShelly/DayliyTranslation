[原文](https://medium.com/flawless-app-stories/detecting-avengers-superheroes-in-your-ios-app-with-ibm-watson-and-coreml-fe38e493a4d1)

# Machine Learning in iOS: IBM Watson and CoreML
Part 1

# iOS机械学习：IBM Watson 与 CoreML(第一部分)

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

回到之前的前端页面，从工程菜单中选择你复仇者项目，然后进入设置页面

Scroll down to Associated services section and click Watson. In the next screen we can see there are lots of services for many purposes: Text to Speech, Speech to Text, Language Translator, Tone Analyzer, etc. In our case, we need Visual Recognition service. For Lite plan, we can have only 1 instance of each service, if you try to add more, you will get the following warning.

向下滚动到联合服务区并点击Watson选项。下一屏我们看到很多用于多种用途的服务，文本语言，语言文本，语言翻译器，音频分析器。在我们的案例中，我需要视觉识别服务。对于Lite计划，每个服务只能有1个实例。如果你尝试添加更多，则会收到以下警告：

>>>Service broker error: You can only have one instance of a Lite plan 
>>>per service. To create a new instance, either delete your existing 
>>>Lite plan instance or select a paid plan.


The association between Watson projects, tools and services is confusing, probably because there are tons of features we haven’t used yet. Think of project as a bag of tool and service. We can only use 1 instance of each service for Lite plan, and tool is the front end we use to interact with the service.

Watson projects 工具和服务之前的关联是很混乱，肯能是因为我们还没有使用过很多功能。将项目看做一个工具包和服务。我们只能为Lite计划使用每个服务的1个实例，而工具是我们用来与服务交互的前端。

## Step 4: Remove existing service if any

## 移除现有的服务（如有）

To delete existing service, go back to home page, select Watson Services from Services menu. Here we can launch the tool for this service or delete it.

要删除现有服务，请返回主页，从Services菜单中选择Watson Services。 在这里，我们可以启动此服务的工具或将其删除。

After deleting existing service, go back to step 3 to configure new instance for service Visual Recognition for this project Avengers.

删除现有的服务。返回到第三步为项目Avengers的视觉识别服务配置一个新的实例

## Step 5: Create Visual recognition models

## 创建视觉识别模型

Go to your Avengers project, click Assets tab and head over to section Visual recognition models. For the fun part of this tutorial, I will name our custom model Avengers Models 🤘.

转到您的复仇者项目，点击Assets标签并转到部分视觉识别模型。 对于本教程的有趣部分，我将命名我们的自定义模型复仇者模型🤘。

We can create class for each hero, or upload a zip file containing images for each hero. Note that the name of the zip file corresponds to the name of the class. The negative class is for images that do not fall into any expected classes. For this tutorial we will deal with Iron Man, Spider-Man, Captain America and Thor, because they are my favourites 😅.

我们可以为每一个英雄创建一个类，或者上去一个包含每个英雄的图片zip文件。注意，每个zip文件的命名必须和创建的类的名字相对应。否定类是针对不属于任何预期类别的图像

The more images we upload, the more correct the model is. Also, you should use more variations of the characters, in different angles, lights. Note that we should put correct images in each folder, because garbage in is garbage out

我们上传的图片越多，模型越正确。 此外，您应该以不同的角度亮，不同的光线角度，使用同一个英雄。 请注意，我们应该在每个文件夹中放置正确的图像，因为垃圾进入垃圾了

Select the button Find and add images on the top right to add images.

选择右上角的查找和添加图像以添加图像。

## Step 6: Data set

## 数据集

We can download some free images from Google to use as our data set. You can get the data set on my GitHub repo, or you can prepare the data set yourself. Downloading images manually is not fun, let’s use a script. I don’t know of any good tools, but a search from Google shows this tool google-images-download. We’re lazy, let’s save time.

我们可以从谷歌下载一些免费的图像作为我们的数据集。 您可以在我的GitHub仓库中获取数据集，也可以自己准备数据集。 手动下载图像并不好玩，让我们使用脚本。 我不知道任何好的工具，但Google的搜索oogle-images-download显示这个工具谷歌图片下载。 我们很懒，让我们节省时间。

50 images for each hero should be good in this post. Let’s zip them and upload to Watson. After uploading finishes, add those assets to model.

这边文章中，每个英雄50张图片应该可以达到很好的效果。让我们压缩这些图片并上传到Watson。上传完成之后。添加这些图片资源到模型中。

## Step 7: Train the model

## 训练模型

This step requires all your remaining power to click that Train Model to start the training process. This takes some times depending on your dataset. For our dataset in this tutorial, it should take less than 10 minutes.

这一步将需要你全力点击训练模型来开始训练的过程。这个过程所消耗的时间依赖于你的数据集。在这个教程的数据集，训练过程应该小于10分钟

The reason it takes that short amount of time is because of our very small dataset. The other reason I think is because Visual Recognition uses a technique called transfer learning

训练模型的过程花费的时间很短是一方面是因为数据集很小，另一方面我认为是Visual Recognition 使用了一种叫做transfer learning的技术。

>>>Today, you can use “transfer learning” — i.e., use an existing image 
>>>recognition model and retrain it with your own dataset.

>>> 现在，你可以使用“transfer learning”技术---即使用现有的图像识别模型，并使用您自己的数据集重新训练。

After training is complete, go to Implementation tab then select Core ML to download the CoreML compatible model. The file is 13MB.

训练完这些模型之后，页面转到Implementation菜单，然后选择Core ML 选项去下载兼容CoreML的模型，文件大小有13MB

## Using CoreML model in iOS app

## 在iOS APP中使用CoreML模型

With the trained model from Watson, there are 2 ways to consume it in the app. The easy way is to use Watson SDK which wraps CoreML. The second way is to just use CoreML alone, and it is the approach we will show here, as it is simply to understand.

app中，有两种方法来使用Watson训练好的模型。最简答的方式使用包含了CoreML的Watson SDK。第二种方法是单独的使用CoreML框架，这是我们将要展示的方法，它仅仅是简单的帮助我们去理解深度学习

Just to let you know that this SDK exists, we won’t use any framework in this tutorial 😎.

仅仅只是让你知道有这种SDK存在。在这边教程我们不使用任何的第三方框架

	let classifierID = "your-classifier-id"

	let failure = { (error: Error) in print(error) }

	let image = UIImage(named: "your-image-filename")

	visualRecognition.classifyWithLocalModel(image: image, classifierIDs: 			[classifierID], failure: failure) {

					classifiedImages in print(classifiedImages)

	}

	let classifierID = "your-classifier-id"

	let failure = { (error: Error) in print(error) }

	visualRecognition.updateLocalModel(classifierID: classifierID, failure: failure) {

    print("model updated")

	}
	
## Use plain CoreML model

## 使用的通用的 CoreML 模型

In our guide, we just download the trained CoreML model and use it in our app. We learn the most when we don’t use any extra unnecessary frameworks. The project is on GitHub.

在这篇教程中，我们仅仅下载了训练好的CoreML模型，在再app中使用它们。当我们不需要任何额外的框架，我们可以学到更多。这个工程的[GitHub链接](https://github.com/onmyway133/Avengers)

### Running on the simulator

在模拟器上运行

The project uses UIImagePickerController with controller.sourceType = .camera so it’s great to build on device, take some pictures and predict. You can also run it on the simulator, just remember to point sourceType to photoLibrary because there’s no camera in the simulator 😅.

项目中使用了UIImagePickerController的controller.sourceType =  .camera，所以是建立在真机设备上的，拍摄照片和预测是很棒的。你也可以在模拟器上运行，只需要记住将sourceType改为photoLibrary，因为模拟器上没有相机

## Step 1: Add the model to project

## 添加模型到项目中

The model is what we use to make prediction. We just need to drag it to the project and add it to the app target. As reading from Integrating a Core ML Model into Your App we can just use the generated class AvengersModels.

这个模型就是我们用来做预测的东西。 我们只需将其拖动到该项目并将其添加到应用程序目标中。 从阅读[Integrating a Core ML Model into Your App](https://developer.apple.com/documentation/coreml/integrating_a_core_ml_model_into_your_app)，我们可以使用生成的类AvengersModels。

>>>Xcode also uses information about the model’s inputs and outputs to 
>>>automatically generate a custom programmatic interface to the model, 
>>>which you use to interact with the model in your code.

>>>Xcode还使用有关模型输入和输出的信息来自动生成模型的自定义编程接口，您可以使用该模
>>>型与代码中的模型进行交互。

## Step 2: Vision

## Vision
According to wiki:

以下是维基百科的关于Vision的解释

>>>The Vision is a fictional superhero appearing in American comic 
>>>books published by Marvel Comics, an android and a member of 
>>>the Avengers who first appeared in The Avengers #57 (October 1968)

>>>Vision是一个虚构的超级英雄，出现在漫威漫画出版的美国漫画书中，机器人和复仇者的成
>>>员最初出现在复仇者联盟＃57（1968年10月）

Just kidding 😅 Vision is a framework that works with CoreML “to apply classification models to images, and to preprocess those images to make machine learning tasks easier and more reliable”.

开玩笑😅，Vision是一个可与CoreML协作的框架，“将分类模型应用于图像，并对这些图像进行预处理，使机器学习任务变得更加简单和可靠”。

You should definitely watch WWDC 2017 video Vision Framework: Building on Core ML to get to know some other features of this framework, like detecting faces, computing facial landmarks, tracking objects, …

您绝对应该观看WWDC 2017 WWDC 视频Vision Framework：基于Core ML构建，以了解此框架的其他功能，例如检测面部，计算面部标志，跟踪对象...

With the generated AvengersModels().model we can construct Vision compatible model VNCoreMLModel and request VNCoreMLRequest, then finally send the request to VNImageRequestHandler. The code is very straightforward:

使用生成的AvengersModels().model，我们可以构建Vision兼容模型VNCoreMLModel并请求VNCoreMLRequest，然后将请求发送到VNImageRequestHandler。 代码非常简单：

	private func detect(image: UIImage) throws {

		loadingIndicator.startAnimating()
			
		let model = try VNCoreMLModel(for: AvengersModels().model)

		let request = VNCoreMLRequest(model: model, completionHandler: { [weak self] request, error in

	guard let results = request.results as? [VNClassificationObservation],

		let topResult = results.first else {
 
	   print(error as Any)

      return

    }

    DispatchQueue.main.async {

      self?.resultLabel.text = topResult.identifier + "(confidence \(topResult.confidence * 100)%)"

      self?.loadingIndicator.stopAnimating()

    }
    
	})

	 let handler = VNImageRequestHandler(cgImage: image.cgImage!, options: [:])

	DispatchQueue.global(qos: .userInteractive).async {

    do {

      try handler.perform([request])

    } catch {

      print(error)

	 	}
	 }
	}

The prediction operation may take time, so it’s good habit to send it to background queue, and to update UI when we get the result.

预测的操作可能会消耗一些时间，所以将这种耗时操作放在后台队列中是个好习惯，当得到预测的结果后更新UI

Build and run the app. If you build it on an iPhone, take a picture of a superhero, if that is one of the 4 superheroes we have trained in this tutorial, then CoreML will be able to predict who he is based on the trained .mlmodel

编译并运行App，如果是运行在iPhone上，拍一张超级英雄的照片，如果是拍的照片是我们教程中所训练的4个超级英雄中的某一个，那么CoreML将会基于训练好的.mlmodel文件进行预测

# 陌生单词

	dominant 主要，主导 predicting 预测 consume 消耗 使用
	straightforward 直截了当 one-stop 一站式 seamlessly 无缝的

