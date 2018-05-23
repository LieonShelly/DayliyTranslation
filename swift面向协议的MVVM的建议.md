[原文](https://medium.com/@thibault.wittemberg/protocol-oriented-tips-for-mvvm-in-swift-5e34b6fc0eca)

Protocol Oriented Tips for MVVM in Swift 

swift面向协议的MVVM的建议

Hi folks. Lately MVVM has become some sort of standard as an architecture for iOS apps. It offers a good separation of concerns, a good way to format data and great view binding mechanisms with frameworks such as RxSwift. In this post I will give few tips I use to ease the implementation of this pattern.

大家好，最近MVVM设计模式已经成为了iosAPP架构的某一种标准。MVVM提供&lt;了一个很好的业务分隔，一个很好的格式数据，非常有效的视图绑定机制，比如Rxswift框架。在这篇文章中，我将分享一些关于我如何轻松地使用这种设计模式的一些建议

Views made easy with Reusable 

使用可复用的视图很容易

With MVVM, separation between Views and the rest of your architecture is very clear. Views include UIViewControllers and their outlets. As a matter of fact, instantiating Views becomes more and more important, especially since patterns such as Coordinator gain in popularity. We will assume in the rest of this article that you are implementing this kind of architecture. 

使用MVVM，分离视图和你的APP的其他架构是非常明确的。视图包括UIViewControllers以及其子控件。事实上，实例化视图变得越来越重要了，尤其从协调器等模式越来越受欢迎了。我们将假定在本文剩下的部分你正在实现这种架构。

Reusable is an API that comes with handy extensions to UIViews and UIViewControllers that ease their instantiation in a type safe manner.

Reusable是一种可以方便的扩展UIView和UIViewController的API，该API以类型安全的方式简化UIView和UIViewController的实例化

Here is the GitHub repo: Reusable. It is a lightweight API compatible with Carthage, CocoaPods and SPM. It would be a shame not to use it regarding the happiness it brings 🖖.

Reusable的GitHub链接你可以点[这里](https://github.com/AliSoftware/Reusable),它是兼容Carthage, CocoaPods 和 SPM的轻量级API。好好享受它给你带来的便捷吧。

Basically, Reusable provides mixins (Protocols with default implementation) that will add instantiation functions to UIViews and UIViewControllers, as soon as you make them conform to the appropriate protocol.

基本上，Reusable提供了的是协议和协议的默认实现的混合组合，UIView和UIViewControllers一旦遵守了协议，就会向它们添加实例化函数。

While using Coordinator pattern, at some point you will want to instantiate UIViewControllers and pass them ViewModels. Lucky you, because Reusable helps a lot in doing that.

当你使用协调器模式时，某些时候，你想实例化UIViewControllers并传值给viewmodes。幸运的是，Reusable能够做到这一点

Here are the things you have to do to use Reusable for UIViewControllers instantiation:

以下是使用Reusable实例化UIViewControllers 的几个注意点：

. Create a Storyboard file per UIViewController (of course if it is possible to have several UIViewControllers in the same storyboard, but for the sake of the simplicity we will consider only one UIViewController)

. 为每一个UIViewController创建一个Storyboard（当然有可能同一个storyboard有几个UIViewControllers，当然出于简单性考虑我们暂时只创建一个UIViewController）

. UIViewController as the initial ViewController in the scene

. 设置UIViewController作为场景中的初始化ViewController

. Create a UIViewController file in which the ViewController class name is the same as the Storyboard file name. For instance if the Storyboard file is named “SettingsViewController.storyboard”, then the UIViewController class will be named “SettingsViewController”

. 创建一个与Storyboard文件中的ViewController的同名的UIViewController文件。例如，如果Storyboard文件名字为“SettingsViewController.storyboard”，那么创建的UIViewController 的类名则为SettingsViewController

. Make the UIViewController implement the Protocol “StoryboardBased”

. 使UIViewController实现StoryboardBased协议

And that’s it. You can now instantiate the ViewController with a single line of code:

最后你就可以实例化ViewController通过一行代码：

	let settingsViewController = SettingsViewController.instantiate()
  
 What is cool about that is that settingsViewController’s type is SettingsViewController without the need for a cast statement.

这样的有点就是settingsViewController的类型不需要SettingsViewController进行cast语句进来类型转换了
 
In fact the StoryboardBased protocol is pretty straight forward. Let’s dive into it:

事实上StoryboardBased是非常简单的，让我们看下它的源码
  
	public protocol StoryboardBased: class {
		static var storyboard: UIStoryboard { get }
	}

	public extension StoryboardBased {
		static var storyboard: UIStoryboard {
	    return UIStoryboard(name: String(describing: self), bundle: Bundle(for: self))  
			}
	  }
	  
	public extension StoryboardBased where Self: UIViewController {
     static func instantiate() -> Self {
         guard let vc = storyboard.instantiateInitialViewController()     as? Self else {
      fatalError("The VC of \(sceneStoryboard) is not of class \(self)")
     }
         return vc
       }
    }
    
 Basically, what it does is providing a static “instantiate” function to each UIViewController that implements the Protocol. This function returns an instance of the UIViewController. As “Self” is the return type of the function, type inference assures we won’t have to cast the result.
    
大致是这样的，StoryboardBased协议为每一个UIViewController提供了一个“instantiate”静态方法，UIViewController只要遵守了改协议就可以使用该方法。该方法返回的是一个UIViewController的一个实例，返回的类型是调用该方法的UIViewController。因此类型推断可以确保了类型安全
 
I strongly encourage you to take a deep look at Reusable. It will be also helpful when it comes to instantiate UIViews from Xib or dequeue UITableViewCells in a type safe way.

我强烈的建议你深入了解下Reusable。对从Xib实例化的UIView，或以类型安全的方式UITableViewCells出列时，它也会很有帮助。

Protocol oriented ViewModels
面向协议的ViewModel

Coordinator-like architectures are common in nowadays applications, especially when being combined to a MVVM pattern. That is why I wished to talk about Reusable in the first place.


类似于协调的架构在当今的应用中很常见，尤其死与MVVM模式相结合时。这就是我为什么首先要谈论Reusable的原因
But there is a trick that I find very useful and complementary to Reusable. It fits very well with the MVVM pattern in a Protocol Oriented approach.
The idea is not only to ease the instantiation of UIViewControllers but also to provide a nice way to pass them their associated ViewModels. Let’s write a Protocol that defines what it is to have a ViewModel.


但有一个技巧，我觉得它对于Reusable非常有用和互补，它在面向协议的方法中非常适合MVVM模式

The idea is not only to ease the instantiation of UIViewControllers but also to provide a nice way to pass them their associated ViewModels

这种技巧不仅可以简化UIViewControllers的实例化，而且还提供了一个很好的方式来传递它们的相关ViewModels
下面来定义一个有ViewModel的协议

	protocol ViewModelBased: class {
       associatedtype ViewModel
       var viewModel: ViewModel { get set }
	}	
	
We can now mix it with StoryboardBased and provide a static function that instantiates a UIViewController with a ViewModel as a parameter.

我们现在可以将它与StoryboardBased混合并提供一个静态函数，它将ViewModel作为参数实例化一个UIViewController。

	extension ViewModelBased where Self: StoryboardBased & UIViewController {
     static func instantiate (with viewModel: ViewModel) -> Self {
	        let viewController = Self.instantiate()
	            viewController.viewModel = viewModel
	           return viewController
    		}
		}

Conditional extension is a very powerful tool. The “where” statement that combines “StoryboardBased” and “UIViewController” makes the Self.instantiate function available, so we just have to wrap this call in another static function that sets the UIViewController.viewModel property

有条件的扩展是一个非常强大的工具。 结合“StoryboardBased”和“UIViewController”的“where”语句使得Self.instantiate函数可用，所以我们只需要将此调用包装到另一个静态函数中，该函数设置UIViewController.viewModel属性

Let’s say we have a MyViewController that conforms to the ViewModelBased protocol:

假设我们有一个符合ViewModelBased协议的MyViewController：

	class MyViewController: UIViewController, StoryboardBased, ViewModelBased {
			var viewModel: MyViewModel!

	    override func viewDidLoad() {
	        super.viewDidLoad()
	    }
    }

Its instantiation with the ViewModel will be super easy:

带有ViewModel的MyViewController的实例化将会非常简单：

	let myViewController = MyViewController.instantiate(with: MyViewModel())

Let’s go further in ViewModel abstraction
让我们更深入的探讨ViewModel

In what we’ve done so far, we still have to instantiate the ViewModel and give it to the View. Wouldn’t it be nice to just instantiate the View and let it deal with the ViewModel instantiation in a generic way ? Swift type inference can help a lot in doing so.

到目前为止我们所做的事情中，我们仍然需要实例化ViewModel并将其提供给View。 将实例化View并让它以通用方式处理ViewModel实例是不是很好？ Swift类型推断可以有效的帮助我们这样做。

Before we dive into the code, I’d like to warn you that some may say this technic introduce a strong coupling between the View and the ViewModel. In a way this is true, but depending on the amount of time, energy, complexity allocated to your app,it can be an efficient strategy anyway.

在我们深入了解代码之前，我想告诉你一些人可能会说这个技术在View和ViewModel之间引入了大量的耦合。 某种程度上这是事实，但取决于分配给应用程的时间，精力和复杂性，它无论如何都可能是一个有效的方案。

First of all we will define WHAT is a ViewModel. Of course we will use a Protocol for that. And by doing so, we’ll introduce the notion of Services. Services are low level layers that are needed by the ViewModel to retrieve data or perform actions.
首先，我们将定义什么是ViewModel。 当然我们会为此使用一个协议。 通过这样做，我们将介绍服务的概念。 服务是ViewModel检索数据或执行操作所需的低层次API。

	protocol ViewModel {
     associatedtype Services
     init (withServices services: Services)
	}

We have to amend the ViewModelBased definition to introduce the ViewModel protocol in the associated type.
	我们必须修改ViewModelBased定义来为引入相关类型的ViewModel协议
	
	protocol ViewModelBased: class {
     associatedtype ViewModelType: ViewModel
     var viewModel: ViewModelType { get set }
	}

Finally we can adapt the ViewModelBased extension like this:
最后，我们可以像这样调整ViewModelBased扩展：

	extension ViewModelBased where Self: StoryboardBased & UIViewController {

    static func instantiate<ServicesT> (withServices services: ServicesT) -> Self

    where ServicesT == Self.ViewModelType.Services {

        let viewController = Self.instantiate()

        viewController.viewModel = ViewModelType(withServices: services)

        return viewController

    }

	}

There are 2 main differences between this version and the previous one:

这个版本和前一个版本有两个主要区别：

the first difference is obvious: this static function not only instantiates the UIViewController but also the ViewModel. That’s one thing the developper won’t have to do anymore 👍

第一个区别很明显：这个静态函数不仅实例化了UIViewController，而且实例化了ViewModel。 这是开发者不再需要做的一件事了

the second difference is the function signature. It now takes some kind of Services as a parameter. As you can see, this is a generic function. The “where” statement forces the developper to pass a ServicesT that is the same as the one required in the ViewModelType. This brings safety and consistency 👍

第二个区别是功能签名。 它现在需要某种服务作为参数。 正如你所看到的，这是一个通用功能。 “where”语句强制开发人员传递与ViewModelType中所需的ServicesT相同的ServicesT。 这带来了安全性和一致性👍s

What is great here is that Swift will infer the ViewModelType according to the ViewModelBased implementation.

这里最棒的是Swift将根据ViewModelBased实现来推断ViewModelType。

Let’s see this in action.
举个例子

First thing first, we have to define a dumb Service for the sake of this demonstration:

首先，我们必须为这个演示定义一个虚假的服务：

	class MyService {

    func executeService() {

        print ("Service execution")

    }
	}

We can now define a ViewModel that needs this Service:

我们现在可以定义需要此服务的ViewModel：

	struct MyViewModel: ViewModel {

    typealias Services = MyService

    init(withServices services: Services) {

        services.executeService()

      }
    }


MyViewController instantiation with its ViewModel becomes that easy (considering that we already have a MyService instance):

使用ViewModel实现MyViewController实例变得非常简单（考虑到我们已经有了一个MyService实例）：

	let myViewController = MyViewController.instantiate(withServices: myService)

Protocol composition for Services

Services的协议组合

Although this seems pretty handy, there is one drawback to this pattern: what if a ViewModel needs several Services 

虽然这看起来相当方便，但这种模式有一个缺点：如果ViewModel需要多个服务，该怎么办？

One solution would be to pass some kind of container that provides ALL the services of your application. This would work, but not very safe because the ViewModel could use every services of the container without restriction.

一种解决方案是通过container来提供应用程序的所有服务。 这会工作，但不是很安全，因为ViewModel可以不受限制地使用container的每个服务。

I once read a post from Krzysztof Zablocki about this issue (here) and I though it would work very gently with my ViewModel approach.

我曾经从Krzysztof Zablocki读过一篇关于这个问题的文章[这里](http://merowing.info/2017/04/using-protocol-compositon-for-dependency-injection/)，我认为它可以非常轻松地使用我的ViewModel方法。

Let’s say our application needs 3 services:

假设我们的应用程序需要3个服务：


	class Service1 {

    func executeService1() {

        print ("execution of Service1")

    }

	}

	class Service2 {

    func executeService2() {

        print ("execution of Service2")

   	 }

	}

	class Service3 {

    func executeService3() {

        print ("execution of Service3")

    }

	}

The idea is to use Protocol composition to express the services we need in our ViewModel. We will define a Protocol per Service that grants access to it:

这个想法是使用协议组合来表达我们在ViewModel中需要的服务。 我们将为每个服务定义一个协议，以授予其访问权限：

	protocol HasService1 {

    	var service1: Service1 { get }

	}



	protocol HasService2 {

    	var service2: Service2 { get }

	}



	protocol HasService3 {

    	var service3: Service3 { get }

	}

In our ViewModels we now have the ability to clearly define our dependancies, with a fine granularity:

在我们的ViewModels中，我们现在可以清晰地定义你的依赖关系，并具有精细的粒度：

	struct MyViewModel: ViewModel {

	// thanks to protocol composition we define 	only the services we want to use

    typealias Services = HasService1 & HasService2

    	init(withServices services: Services) {

      	  services.service1.executeService1()

      	  services.service2.executeService2()

    	}

	}



struct MyOtherViewModel: ViewModel {

    typealias Services = HasService2 & HasService3

    init(withServices services: Services) {

        services.service2.executeService2()

        services.service3.executeService3()

    }

}

The last step is to define the dependancy container:

最后一步是定义依赖容器：

	class MyServices: HasService1, HasService2, HasService3 {

    	let service1 = Service1()

     let service2 = Service2()

     let service3 = Service3()
	}

And we’re good to go, we can now pass the container to our ViewModels with a decent safety but a great scalability. If we need to access another Service inside a ViewModel, we just have to update the protocol composition.

大工告成了，现在我们可以通过容器将ViewModels传递给ViewModel，但是具有很好的可扩展性。 如果我们需要访问ViewModel中的另一个服务，我们只需要更新协议组合。

At the end, UIViewController instantiation is the same (consider that MyViewController2 is a ViewModelBased VC):

最后，UIViewController实例化是相同的（考虑MyViewController2是一个ViewModelBased VC）：

	let myViewController = MyViewController.instantiate(withServices: myServices)

	let myViewController2 = MyViewController2.instantiate(withServices: myServices)

	// This is the same myServices instance for the 2 ViewControllers

	// but each ViewModel will only access what's needed



# 陌生单词
		complementary 补充   strong coupling 大量的耦合 
	   amend 修改 drawback缺点    granularity 粒度
	   scalability 可扩展性

