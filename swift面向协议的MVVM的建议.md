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



# 陌生单词
		complementary 补充


