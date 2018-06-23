Android应用程序是用Java编程语言编写的。Android SDK工具将代码——连同任何数据和资源文件——编译成一个Android包，一个带有.apk后缀的归档文件。单个.apk文件中的所有代码都被认为是一个应用程序，并且是android设备用来安装应用程序的文件。<br>
一旦安装在设备上，每个Android应用程序都生活在自己的安全沙箱中：
* Android操作系统是一个多用户的Linux系统，每个应用程序都是不同的用户。
* 默认情况下，系统为每个应用程序分配一个唯一的Linux用户ID（ID仅由系统使用，并且对应用程序不知道）。该系统为应用程序中的所有文件设置权限，以便只有分配给该应用程序的用户ID才能访问它们。
* 每个进程都有自己的虚拟机（VM），因此应用程序的代码与其他应用程序隔离开来。
* 默认情况下，每个应用程序都在自己的Linux进程中运行。当应用程序的任何组件都需要执行时，Android就启动了这个过程，然后在不再需要的时候关闭这个过程，或者当系统必须恢复其他应用程序的内存时。
---
通过这种方式，Android系统实现了最小特权原则。也就是说，在默认情况下，每个应用程序只能访问它需要完成其工作的组件，仅此而已。这就创建了一个非常安全的环境，在这个环境中，应用程序不能访问系统的某些部分，而它没有得到许可。
然而，有一些方法可以让应用程序与其他应用程序共享数据，以及应用程序访问系统服务：
* 有可能安排两个应用程序共享同一个Linux用户ID，在这种情况下，它们能够访问彼此的文件。为了保存系统资源，具有相同用户ID的应用程序也可以安排在同一个Linux进程中运行并共享同一个VM（应用程序也必须使用相同的证书签署）。
* 应用程序可以请求访问设备数据，比如用户的联系人、SMS消息、可挂载存储（SD卡）、摄像头、蓝牙等。所有应用程序权限必须在安装时由用户授予。
---
这涵盖了关于Android应用程序如何在系统中存在的基本知识。本文件的其余部分将向您介绍：
* 定义您的应用程序的核心框架组件。
* 清单文件，其中声明组件和应用程序所需的设备特性。
* 与应用程序代码分离的资源，允许您的应用程序为各种设备配置优雅地优化其行为。
---
# Application Components(应用程序组件)
  应用程序组件是Android应用程序的基本构件。每个组件都是系统可以进入您的应用程序的不同的点。并不是所有的组件都是用户的实际入口点，有些是相互依赖的，但是每个组件都是作为它自己的实体存在的，并且扮演着一个特定的角色——每一个都是一个独特的构建块，帮助定义应用程序的整体行为。<br>
  有四种不同类型的应用程序组件。每种类型都有不同的用途，并且有一个独特的生命周期，它定义了组件是如何创建和销毁的。<br>
  以下是四种类型的应用程序组件：
## Activities(活动)
  活动代表一个带有用户界面的单一屏幕。例如，一个电子邮件应用程序可能有一个活动，它显示了一个新的电子邮件列表，另一个用来写电子邮件的活动，以及另一个用于阅读电子邮件的活动。尽管这些活动一起工作，在电子邮件应用程序中形成一个有凝聚力的用户体验，但每一个都是独立于其他的。因此，不同的应用程序可以启动任何一种活动（如果电子邮件应用程序允许的话）。例如，一个摄像头应用程序可以在构成新邮件的电子邮件应用程序中启动该活动，以便用户共享一张图片。<br>
  活动是作为活动的一个子类实现的，您可以在活动开发人员指南中了解更多信息。
## Services(服务)
  服务是在后台运行的组件，用于执行长时间运行的操作或为偏远进程执行工作。服务不提供用户界面。例如，当用户处于不同的应用程序时，服务可能在后台播放音乐，或者它可以通过网络获取数据，而不会阻止用户与某个活动的交互。另一个组件，比如一个活动，可以启动服务，并让它运行或绑定到它，以便与之交互。<br>
  服务被作为服务的子类实现，您可以在服务开发人员指南中了解更多信息。
## Content providers(内容提供者)
  内容提供程序管理一组共享的应用程序数据。您可以将数据存储在文件系统、SQLite数据库、web或应用程序可以访问的任何其他持久性存储位置。通过内容提供程序，其他应用程序可以查询甚至修改数据（如果内容提供程序允许的话）。例如，Android系统提供了一个内容提供程序来管理用户的联系信息。因此，任何具有适当权限的应用程序都可以查询内容提供程序的一部分（如[ContactsContract.Data](http://)）来读取和写入关于某个粒子的信息。<br>
  内容提供程序对于阅读和编写私有应用程序的数据也是有用的，而不是共享的。例如，[Note Pad](http://tool.oschina.net/uploads/apidocs/android/resources/samples/NotePad/index.html)样例应用程序使用一个内容提供程序来保存注释。<br>
  内容提供程序是作为ContentProvider的子类实现的，并且必须实现一组标准的api，使其他应用程序能够执行交易。要了解更多信息，请参阅内容提供商开发指南。
## Broadcast receivers(广播接收器)
  广播接收器是响应全系统广播公告的组件。许多广播都来自于这个系统——例如，一个广播宣布屏幕关闭了，电池电量不足，或者拍摄了一张照片。应用程序还可以启动广播——例如，让其他应用程序知道一些数据已经被下载到设备上，并且可供他们使用。<br>
  尽管广播接收器不显示用户界面，但他们可能会创建一个状态栏通知，在广播事件发生时提醒用户。然而，更常见的情况是，广播接收器只是对其他组件的“网关”，目的是做非常少量的工作。例如，它可能会发起一个服务来根据事件执行一些工作。<br>
  广播接收器是作为BroadcastReceiver的一个子类实现的，每个广播都作为一个意图对象交付。要了解更多信息，请参阅BroadcastReceiver类。<br>

Android系统设计的一个独特之处在于，任何应用程序都可以启动另一个应用程序的组件。例如，如果您想让用户用设备摄像头捕捉照片，那么可能还有另一个应用程序可以使用它，而您的应用程序可以使用它，而不是开发一个活动来捕获照片。您不需要合并甚至链接到来自相机应用程序的代码。相反，您可以简单地在拍照应用程序中启动捕捉照片的活动。当完成时，照片甚至会返回给您的应用程序，以便您可以使用它。对于用户来说，似乎相机实际上是你的应用程序的一部分。<br>
当系统启动一个组件时，它启动该应用程序的进程（如果它还没有运行）并实例化组件所需的类。例如，如果您的应用程序在捕获照片的摄像机应用程序中启动活动，那么该活动将在属于摄像机应用程序的进程中运行，而不是在应用程序的过程中。因此，与大多数其他系统的应用程序不同，Android应用程序没有单一入口点（例如，没有main（）函数）。<br>
因为系统在一个单独的进程中运行每个应用程序，其中包含限制对其他应用程序的访问的文件权限，所以您的应用程序不能直接激活来自另一个应用程序的组件。然而，Android系统可以。因此，要在另一个应用程序中激活组件，您必须向系统传递一条消息，该系统指定了启动特定组件的意图。然后系统会为你激活组件。<br>
# Activating Components(活动组件)
四种组件类型中的三种——活动、服务和广播接收器——被称为意图的异步消息激活。意图在运行时将各个组件绑定在一起（您可以将它们看作是请求来自其他组件的动作的使者），无论组件属于您的应用程序还是其他组件。<br>
意图是用Intent对象创建的，它定义了一条消息来激活一个特定的组件或一个特定类型的组件——一个意图可以是显式的，也可以是隐式的。<br>
对于活动和服务，意图定义要执行的动作（例如，“查看”或“发送”某些东西），并可能指定要执行的数据的URI（在开始的组件可能需要知道的其他事情中）。例如，一个intent可能传递一个活动的请求，以显示一个图像或打开一个web页面。<br>
在某些情况下,您可以启动一个活动获得的结果,在这种情况下,意图的活动也返回结果(例如,可以发出一个意图让用户选择一个个人接触和它回到你返回意图包括一个URI指向选择接触)。<br>
对于广播接收器来说，意图只是简单地定义了正在广播的公告（例如，一个显示设备电量不足的广播，只包含一个已知的动作字符串，表明“电池很低”）。<br>
其他组件类型，内容提供程序，并不是由意图激活的。相反，它是在一个cont恳求解决者的请求的目标下被激活的。content resolver处理与内容提供者的所有直接交易，以便与提供者执行交易的组件不需要，而是调用cont主菜分解器对象上的方法。这就在内容提供者和请求信息的组件之间留下了一层抽象层（用于安全性）。<br>
激活每种类型的组件有单独的方法：
* 你可以通过传递一个意图来启动活动（或者给它一些新的东西）， by passing an Intent to startActivity() or startActivityForResult() （当你想要返回一个结果的时候）。
* 您可以启动一个服务（或者为正在进行的服务提供新的指令） by passing an Intent to startService(). Or you can bind to the service by passing an Intent to bindService().
* 你可以通过传递一个Intent来发起一个广播 like sendBroadcast(), sendOrderedBroadcast(), or sendStickyBroadcast().
* 您可以通过在cont主菜解决程序上调用query()来对内容提供程序执行查询。
有关使用意图的更多信息，请参见意图和意图过滤器文档。关于激活特定组件的更多信息也在以下文档中提供：活动、服务、广播接收和内容提供者。
## Manifest文件
在Android系统启动应用程序组件之前，系统必须知道该组件是通过读取应用程序的AndroidManifest来实现的。xml文件（“manifest”文件）。您的应用程序必须在这个文件中声明它的所有组件，它必须位于应用程序项目目录的根目录中。<br>
清单除了声明应用程序的组件之外，还做了许多事情，例如：<br>
* 识别应用程序需要的任何用户权限，比如Internet接入或对用户联系人的读访问。
* 根据应用程序使用的API，声明应用程序所需的最小API级别。
* 声明应用程序使用或要求的硬件和软件特性，如相机、蓝牙服务或多点触控屏幕。
* API库应用程序需要与（除了Android框架API）相关联，比如Google Maps库。
## Declaring components(声明组件)
清单的主要任务是通知系统关于应用程序的组件。例如，清单文件可以声明一个活动如下：     

```
<?xml version="1.0" encoding="utf-8"?>
<manifest ... >
    <application android:icon="@drawable/app_icon.png" ... >
        <activity android:name="com.example.project.ExampleActivity"
                  android:label="@string/example_label" ... >
        </activity>
        ...
    </application>
</manifest>
`````
在<application>元素中，android:icon属性指向标识应用程序的图标的资源。     
在<activity>元素中，android:name属性指定活动子类的全限定类名和android:label属性指定一个字符串作为活动的用户可见标签。     
您必须以这种方式声明所有应用程序组件：
* <activity> elements for activities
* <service> elements for services
* <receiver> elements for broadcast receivers
* <provider> elements for content providers
  您在源代码中包含的活动、服务和内容提供者，但是在清单中没有声明，对系统是不可见的，因此，永远不能运行。然而，广播接收器可以在清单中声明，也可以在代码中动态创建（作为BroadcastReceiver对象），并通过调用registerReceiver()在系统中注册。
  有关如何为应用程序构造manifest文件的更多信息，请参见AndroidManifest。xml文件文档。
## Declaring component capabilities(声明组件的功能)
  正如上面所讨论的，在激活组件时，您可以使用一种意图来启动活动、服务和广播接收器。您可以通过在意图中显式地命名目标组件（使用组件类名）来做到这一点。然而，意图的真正力量在于意图行为的概念。有了意图操作，您只需描述您想要执行的动作的类型（以及您想要执行该操作的数据），并允许系统在设备上找到能够执行该操作并启动它的组件。如果有多个组件可以执行意图所描述的动作，那么用户就会选择要使用哪一个。
  系统识别可以响应意图的组件的方式是将接收到的意图与设备上其他应用程序的清单文件中提供的意图过滤器进行比较。
  当你在你的应用程序的清单中声明一个组件时，你可以选择包含意图过滤器来声明组件的功能，这样它就可以响应来自其他应用程序的意图。您可以通过在组件的声明元素的子元素中添加一个 <intent-filter> 元素来声明组件的意图过滤器。
  例如，一个含有用于编写新邮件的活动的电子邮件应用程序可能会在其清单条目中声明一个意图过滤器，以响应“发送”意图（为了发送电子邮件）。您的应用程序中的一个活动可以用“send”动作（actionsend）来创建一个意图，系统与电子邮件应用程序的“发送”活动相匹配，并在您使用startActivity（）调用意图时启动它。
  关于创建意图过滤器的更多信息，请参阅 Intents and Intent Filters意图和意图过滤器文档。
## Declaring application requirements
There are a variety of devices powered by Android and not all of them provide the same features and capabilities. In order to prevent your application from being installed on devices that lack features needed by your application, it's important that you clearly define a profile for the types of devices your application supports by declaring device and software requirements in your manifest file. Most of these declarations are informational only and the system does not read them, but external services such as Google Play do read them in order to provide filtering for users when they search for applications from their device.
For example, if your application requires a camera and uses APIs introduced in Android 2.1 (API Level 7), you should declare these as requirements in your manifest file. That way, devices that do not have a camera and have an Android version lower than 2.1 cannot install your application from Google Play.    
However, you can also declare that your application uses the camera, but does not require it. In that case, your application must perform a check at runtime to determine if the device has a camera and disable any features that use the camera if one is not available.
Here are some of the important device characteristics that you should consider as you design and develop your application:
### Screen size and density    
In order to categorize devices by their screen type, Android defines two characteristics for each device: screen size (the physical dimensions of the screen) and screen density (the physical density of the pixels on the screen, or dpi—dots per inch). To simplify all the different types of screen configurations, the Android system generalizes them into select groups that make them easier to target. 
The screen sizes are: small, normal, large, and extra large.    
The screen densities are: low density, medium density, high density, and extra high density.    
By default, your application is compatible with all screen sizes and densities, because the Android system makes the appropriate adjustments to your UI layout and image resources. However, you should create specialized layouts for certain screen sizes and provide specialized images for certain densities, using alternative layout resources, and by declaring in your manifest exactly which screen sizes your application supports with the <supports-screens> element.    
For more information, see the Supporting Multiple Screens document.    
### Input configurations
Many devices provide a different type of user input mechanism, such as a hardware keyboard, a trackball, or a five-way navigation pad. If your application requires a particular kind of input hardware, then you should declare it in your manifest with the <uses-configuration> element. However, it is rare that an application should require a certain input configuration.
### Device features
There are many hardware and software features that may or may not exist on a given Android-powered device, such as a camera, a light sensor, bluetooth, a certain version of OpenGL, or the fidelity of the touchscreen. You should never assume that a certain feature is available on all Android-powered devices (other than the availability of the standard Android library), so you should declare any features used by your application with the <uses-feature> element.
### Platform Version
Different Android-powered devices often run different versions of the Android platform, such as Android 1.6 or Android 2.3. Each successive version often includes additional APIs not available in the previous version. In order to indicate which set of APIs are available, each platform version specifies an API Level (for example, Android 1.0 is API Level 1 and Android 2.3 is API Level 9). If you use any APIs that were added to the platform after version 1.0, you should declare the minimum API Level in which those APIs were introduced using the <uses-sdk> element.
It's important that you declare all such requirements for your application, because, when you distribute your application on Google Play, the store uses these declarations to filter which applications are available on each device. As such, your application should be available only to devices that meet all your application requirements.
For more information about how Google Play filters applications based on these (and other) requirements, see the Filters on Google Play document.
# Application Resources
An Android application is composed of more than just code—it requires resources that are separate from the source code, such as images, audio files, and anything relating to the visual presentation of the application. For example, you should define animations, menus, styles, colors, and the layout of activity user interfaces with XML files. Using application resources makes it easy to update various characteristics of your application without modifying code and—by providing sets of alternative resources—enables you to optimize your application for a variety of device configurations (such as different languages and screen sizes).
For every resource that you include in your Android project, the SDK build tools define a unique integer ID, which you can use to reference the resource from your application code or from other resources defined in XML. For example, if your application contains an image file named logo.png (saved in the res/drawable/ directory), the SDK tools generate a resource ID named R.drawable.logo, which you can use to reference the image and insert it in your user interface.
One of the most important aspects of providing resources separate from your source code is the ability for you to provide alternative resources for different device configurations. For example, by defining UI strings in XML, you can translate the strings into other languages and save those strings in separate files. Then, based on a language qualifier that you append to the resource directory's name (such as res/values-fr/ for French string values) and the user's language setting, the Android system applies the appropriate language strings to your UI.
Android supports many different qualifiers for your alternative resources. The qualifier is a short string that you include in the name of your resource directories in order to define the device configuration for which those resources should be used. As another example, you should often create different layouts for your activities, depending on the device's screen orientation and size. For example, when the device screen is in portrait orientation (tall), you might want a layout with buttons to be vertical, but when the screen is in landscape orientation (wide), the buttons should be aligned horizontally. To change the layout depending on the orientation, you can define two different layouts and apply the appropriate qualifier to each layout's directory name. Then, the system automatically applies the appropriate layout depending on the current device orientation.
For more about the different kinds of resources you can include in your application and how to create alternative resources for various device configurations, see the Application Resources developer guide.
