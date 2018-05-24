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












# 陌生单词

	dominant 主要，主导 predicting 预测 consume 消耗 使用


