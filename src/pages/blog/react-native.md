---
title: 'react-native'
layout: '../../layouts/Blog.astro'
---


# React Native


### 核心组件与原生组件

React Native 是一个使用React和应用平台的原生功能来构建 Android 和 iOS 应用的开源框架。通过 React Native，您可以使用 JavaScript 来访问移动平台的 API，以及使用 React 组件来描述 UI 的外观和行为：一系列可重用、可嵌套的代码。你可以在下一节了解更多关于 React 的信息。但首先，让我们介绍一下组件在 React Native 中是如何工作的。

### 视图（Views）与移动开发

在 Android 和 iOS 开发中，一个视图是 UI 的基本组成部分：屏幕上的一个小矩形元素、可用于显示文本、图像或响应用户输入。甚至应用程序最小的视觉元素（例如一行文本或一个按钮）也都是各种视图。某些类型的视图可以包含其他视图。全部都是视图。

### 原生组件

在 Android 开发中是使用 Kotlin 或 Java 来编写视图；在 iOS 开发中是使用 Swift 或 Objective-C 来编写视图。在 React Native 中，则使用 React 组件通过 JavaScript 来调用这些视图。在运行时，React Native 为这些组件创建相应的 Android 和 iOS 视图。由于 React Native 组件就是对原生视图的封装，因此使用 React Native 编写的应用外观、感觉和性能与其他任何原生应用一样。我们将这些平台支持的组件称为原生组件。

React Native 允许您为 Android 和 iOS 构建自己的 Native Components（原生组件），以满足您开发应用程序的独特需求。我们还有一个由社区贡献的繁荣生态系统，您可以到Native Directory来查找社区已创建的内容。

### 核心组件

React Native 还包括一组基本的，随时可用的原生组件，您可以使用它们来构建您的应用程序。这些是 React Native 的核心组件。

![Snipaste_2023-12-21_13-54-20](https://github.com/fsyud/astro-web/assets/26371465/3fa424e9-cb99-463e-85f3-1d2892ae3aa1)

示例请参考
[https://reactnative.cn/docs/using-a-listview](https://reactnative.cn/docs/using-a-listview)

## 特定平台代码

在编写跨平台的应用时，我们肯定希望尽可能多地复用代码。但是总有些时候我们会碰到针对不同平台编写不同代码的需求。

React Native 提供了两种方法来区分平台：

- 使用Platform模块.
- 使用特定平台后缀.

另外有些内置组件的某些属性可能只在特定平台上有效。请在阅读文档时留意。

### Platform 模块

React Native 提供了一个检测当前运行平台的模块。如果组件只有一小部分代码需要依据平台定制，那么这个模块就可以派上用场。

```react
import {Platform, StyleSheet} from 'react-native';

const styles = StyleSheet.create({
  height: Platform.OS === 'ios' ? 200 : 100,
});
```

Platform.OS在 iOS 上会返回ios，而在 Android 设备或模拟器上则会返回android。

还有个实用的方法是 Platform.select()，它可以以 Platform.OS 为 key，从传入的对象中返回对应平台的值，见下面的示例：

```react
import {Platform, StyleSheet} from 'react-native';

const styles = StyleSheet.create({
  container: {
    flex: 1,
    ...Platform.select({
      ios: {
        backgroundColor: 'red',
      },
      android: {
        backgroundColor: 'blue',
      },
    }),
  },
});
```

上面的代码会根据平台的不同返回不同的 container 样式 —— iOS 上背景色为红色，而 android 为蓝色。

这一方法可以接受任何合法类型的参数，因此你也可以直接用它针对不同平台返回不同的组件，像下面这样：

```react
const Component = Platform.select({
  ios: () => require('ComponentIOS'),
  android: () => require('ComponentAndroid'),
})();

<Component />;
```

### 检测 Android 版本

在 Android 上，Version属性是一个数字，表示 Android 的 api level：

```react
import {Platform} from 'react-native';

if (Platform.Version === 25) {
  console.log('Running on Nougat!');
}
```

### 检测 iOS 版本

在 iOS 上，Version属性是-[UIDevice systemVersion]的返回值，具体形式为一个表示当前系统版本的字符串。比如可能是"10.3"。

```react
import {Platform} from 'react-native';

const majorVersionIOS = parseInt(Platform.Version, 10);
if (majorVersionIOS <= 9) {
  console.log('Work around a change in behavior');
}
```

### 特定平台后缀

当不同平台的代码逻辑较为复杂时，最好是放到不同的文件里，这时候我们可以使用特定平台后缀。React Native 会检测某个文件是否具有.ios.或是.android.的后缀，然后根据当前运行的平台自动加载正确对应的文件。

比如你可以在项目中创建下面这样的组件：

```
BigButton.ios.js
BigButton.android.js
```

然后去掉平台后缀直接引用：

```
import BigButton from './BigButton';
```

React Native 会根据运行平台的不同自动引入正确对应的组件。

如果你还希望在 web 端复用 React Native 的代码，那么还可以使用.native.js的后缀。此时 iOS 和 Android 会使用BigButton.native.js文件，而 web 端会使用BigButton.js。（注意目前官方并没有直接提供 web 端的支持，请在社区搜索第三方方案）。

比如在你的项目中存在如下的两个文件:

```
Container.js # 由 Webpack, Rollup 或者其他打包工具打包的文件
Container.native.js # 由 React Native 自带打包工具(Metro) 为ios和android 打包的文件
```

在引用时并不需要添加.native.后缀:

```
import Container from './Container';
```

提示: 为避免在构建后的 Web 生产环境的代码中出现多余代码，要记得在你的 Web 打包工具中配置忽略.native.js结尾的文件, 这样可以减少打包后文件的大小。

## 其他参考资源

[https://reactnative.cn/docs/more-resources](https://reactnative.cn/docs/more-resources)

如果你耐心的读完并理解了本网站上的所有文档，那么你应该已经可以编写一个像样的 React Native 应用了。但是 React Native 并不全是某一家公司的作品——它汇聚了成千上万开源社区开发者的智慧结晶。如果你想深入研究 React Native，那么建议不要错过下面这些参考资源。

### 常用的第三方库

如果你正在使用 React Native，那你应该已经对React有一定的了解了。React 是基础中的基础所以我其实不太好意思提这个——但是，如果不幸你属于“但是”，那么请一定先了解下 React，它也非常适合编写现代化的网站。

开发实践中的一个常见问题就是如何管理应用的“状态（state）”。这方面目前最流行的库非Redux莫属了。不要被 Redux 中经常出现的类似"reducer"这样的概念术语给吓住了——它其实是个很简单的库，网上也有很多优秀的视频教程（英文） 。

如果你在寻找具有某个特定功能的第三方库，那么可以看看别人精心整理的资源列表。这里还有个类似的中文资源列表。

更重要的技能是学会在 github 上搜索。比如你需要搜索视频相关的库，那么可以在 github 中搜索react native video。

### 开发工具

VS Code是目前非常受 JS 开发者欢迎的 IDE 工具。

Ignite是一套整合了 Redux 以及一些常见 UI 组件的脚手架。它带有一个命令行可以生成 app、组件或是容器。如果你喜欢它的选择搭配，那么不妨一试。

App Center是由微软提供的热更新服务。热更新可以使你绕过 AppStore 的审核机制，直接修改已经上架的应用。对于国内用户，我们也推荐由本网站提供的Pushy热更新服务，相比 CodePush 来说，提供了全中文的文档和技术支持，服务器部署在国内速度更快，还提供了全自动的差量更新方式，大幅节约更新流量，欢迎朋友们试用和反馈意见！

Expo是一套沙盒开发环境，还带有一个已上架的空应用容器。这样你可以在没有原生开发平台（Xcode 或是 Android Studio）的情况下直接编写 React Native 应用（当然这样你只能写 js 部分代码而没法写原生代码）。

Yoga是一个独立的布局引擎。它并不局限于 React Native，高度优化的开源布局引擎能让产品工程师为多个平台快速构建布局。该引擎在设计时考虑了速度、大小和易用性。

## 搭建开发环境

### 完整原生环境

根据你所使用的操作系统、针对的目标平台不同，具体步骤有所不同。如果想同时开发 iOS 和 Android 也没问题，你只需要先选一个平台开始，另一个平台的环境搭建只是稍有不同。

> 本文搭建环境 windows10，目标平台android

#### 安装依赖

必须安装的依赖有：Node、JDK 和 Android Studio。

虽然你可以使用任何编辑器来开发应用（编写 js 代码），但你仍然必须安装 Android Studio 来获得编译 Android 应用所需的工具和环境。

Node, JDK
我们建议直接使用搜索引擎搜索下载 Node 和[Java SE Development Kit (JDK)](https://www.oracle.com/java/technologies/downloads/#java17)

**注意 Node 的版本应大于等于 16**，安装完 Node 后建议设置 npm 镜像（淘宝源）以加速后面的过程（或使用科学上网工具）。

> 注意：强烈建议始终选择 Node 当前的 LTS （长期维护）版本，一般是偶数版本，不要选择偏实验性质的奇数版本。
> 注意：不要使用 cnpm！cnpm 安装的模块路径比较奇怪，packager 不能正常识别！

React Native 需要 **Java Development Kit [JDK] 17**。你可以在命令行中输入 javac -version（请注意是 javac，不是 java）来查看你当前安装的 JDK 版本。如果版本不合要求，则可以去 [Temurin](https://adoptium.net/temurin/releases/?variant=openjdk17&jvmVariant=hotspot) 或[Oracle JDK](https://www.oracle.com/java/technologies/downloads/#java17)上下载(后者下载需注册登录)。

> 低于 0.73 版本的 React Native 需要 JDK 11 版本，而低于 0.67 的需要 JDK 8 版本。

**本文作者使用版本 "react-native": "0.73.1" "react": "18.2.0"**

```
# 使用nrm工具切换淘宝源
npx nrm use taobao

# 如果之后需要切换回官方源可使用
npx nrm use npm
```

## Android 开发环境

> 译注：请注意！！！国内用户必须必须必须有稳定的代理软件，否则在下载、安装、配置过程中会不断遭遇链接超时或断开，无法进行开发工作。某些代理软件可能只提供浏览器的代理功能，或只针对特定网站代理等等，请自行研究配置或更换其他软件。总之如果报错中出现有网址，那就是因为链接源仓库的网络链接被阻断了，这一阻断现象可能因时间、地区、运营商而不同。

### 1. 安装 Android Studio

首先下载和安装[Android Studio](https://developer.android.google.cn/studio?hl=zh-cn)，国内用户可能无法打开官方链接，请自行使用搜索引擎搜索可用的下载链接。安装界面中选择"`Custom`"选项，确保选中了以下几项：

- Android SDK
- Android SDK Platform
- Android Virtual Device

然后点击"Next"来安装选中的组件。

> 如果选择框是灰的，你也可以先跳过，稍后再来安装这些组件。

安装完成后，看到欢迎界面时，就可以进行下面的操作了。

### 2. 安装 Android SDK

Android Studio 默认会安装最新版本的 Android SDK。目前编译 React Native 应用需要的是Android 13 (Tiramisu)版本的 SDK（注意 SDK 版本不等于终端系统版本，RN 目前支持 android 5 以上设备）。你可以在 Android Studio 的 SDK Manager 中选择安装各版本的 SDK。

你可以在 Android Studio 的欢迎界面中找到`SDK Manager`。点击"Configure"，然后就能看到"SDK Manager"。

SDK Manager 还可以在 Android Studio 的"Preferences"菜单中找到。具体路径是Appearance & Behavior → System Settings → Android SDK。

![Snipaste_2023-12-21_14-15-23](https://github.com/fsyud/astro-web/assets/26371465/9bc05b5c-e8a9-4791-b1b0-52a72bf56ca9)

在 SDK Manager 中选择"SDK Platforms"选项卡，然后在右下角勾选"Show Package Details"。展开Android 13 (Tiramisu)选项，确保勾选了下面这些组件（如果你看不到这个界面，则需要使用稳定的代理软件）：

- Android SDK Platform 33
- Intel x86 Atom_64 System Image（官方模拟器镜像文件，使用非官方模拟器不需要安装此组件）
  然后点击"SDK Tools"选项卡，同样勾中右下角的"Show Package Details"。展开"Android SDK Build-Tools"选项，确保选中了 React Native 所必须的33.0.0版本。你可以同时安装多个其他版本。
  ![Snipaste_2023-12-21_14-17-34](https://github.com/fsyud/astro-web/assets/26371465/b4200a00-9b45-483c-8d30-705b1986c943)

  ![Snipaste_2023-12-21_14-17-52](https://github.com/fsyud/astro-web/assets/26371465/b78ac623-7aad-4143-9ae8-69cb176d3e07)

### 3. 配置 ANDROID_HOME 环境变量

React Native 需要通过环境变量来了解你的 Android SDK 装在什么路径，从而正常进行编译。

打开控制面板 -> 系统和安全 -> 系统 -> 高级系统设置 -> 高级 -> 环境变量 -> 新建，创建一个名为ANDROID_HOME的环境变量（系统或用户变量均可），指向你的 Android SDK 所在的目录（具体的路径可能和下图不一致，请自行确认）：

SDK 默认是安装在下面的目录：

```
C:\Users\你的用户名\AppData\Local\Android\Sdk
```

你可以在 Android Studio 的"Preferences"菜单中查看 SDK 的真实路径，具体是Appearance & Behavior → System Settings → Android SDK。

![Snipaste_2023-12-21_14-20-36](https://github.com/fsyud/astro-web/assets/26371465/ed34a58a-162e-4002-a609-b84812e6d12b)
![Snipaste_2023-12-21_14-22-26](https://github.com/fsyud/astro-web/assets/26371465/abea52ee-cf3b-406c-8d6f-625d5354b110)

你需要关闭现有的命令符提示窗口然后重新打开，这样新的环境变量才能生效。

### 4. 把一些工具目录添加到环境变量 Path

打开控制面板 -> 系统和安全 -> 系统 -> 高级系统设置 -> 高级 -> 环境变量，选中Path变量，然后点击编辑。点击新建然后把这些工具目录路径添加进去：platform-tools、emulator、tools、tools/bin

```
%ANDROID_HOME%\platform-tools
%ANDROID_HOME%\emulator
%ANDROID_HOME%\tools
%ANDROID_HOME%\tools\bin
```

**JAVA_HOME**也需要配置到系统变量

![Snipaste_2023-12-21_14-26-03](https://github.com/fsyud/astro-web/assets/26371465/2f48ce23-52d5-4392-ae90-56e2b0129613)
![Snipaste_2023-12-21_14-27-16](https://github.com/fsyud/astro-web/assets/26371465/ffe1c047-1228-445f-8b6e-9bb688b8bb0e)

## 创建新项目

如果你之前全局安装过旧的react-native-cli命令行工具，请使用npm uninstall -g react-native-cli卸载掉它以避免一些冲突：

```
npm uninstall -g react-native-cli @react-native-community/cli
```

使用 React Native 内建的命令行工具来创建一个名为"AwesomeProject"的新项目。这个命令行工具不需要安装，可以直接用 node 自带的npx命令来使用：

> 必须要看的注意事项一：请不要在目录、文件名中使用中文、空格等特殊符号。请不要单独使用常见的关键字作为项目名（如 class, native, new, package 等等）。请不要使用与核心模块同名的项目名（如 react, react-native 等）。
> 必须要看的注意事项二：请不要在某些权限敏感的目录例如 System32 目录中 init 项目！会有各种权限限制导致不能运行！
> 必须要看的注意事项三：请不要使用一些移植的终端环境，例如git bash或mingw等等，这些在 windows 下可能导致找不到环境变量。请使用系统自带的命令行（CMD 或 powershell）运行。

```
npx react-native@latest init AwesomeProject
```

如果你是想把 React Native 集成到现有的原生项目中，则步骤完全不同，请参考集成到现有原生应用。

### [可选参数] 指定版本或项目模板

你可以使用--version参数（注意是两个杠）创建指定版本的项目。注意版本号必须精确到两个小数点。

```
npx react-native@X.XX.X init AwesomeProject --version X.XX.X
```

还可以使用--template来使用一些社区提供的模板。

### 准备 Android 设备

你需要准备一台 Android 设备来运行 React Native Android 应用。这里所指的设备既可以是真机，也可以是模拟器。后面我们所有的文档除非特别说明，并不区分真机或者模拟器。Android 官方提供了名为 Android Virtual Device（简称 AVD）的模拟器。此外还有很多第三方提供的模拟器如Genymotion、BlueStack 等。一般来说官方模拟器免费、功能完整，但性能较差。第三方模拟器性能较好，但可能需要付费，或带有广告。

### 使用 Android 真机

你也可以使用 Android 真机来代替模拟器进行开发，只需用 usb 数据线连接到电脑，然后遵照在设备上运行这篇文档的说明操作即可。

### 使用 Android 模拟器

你可以使用 Android Studio 打开项目下的"android"目录，然后可以使用"AVD Manager"来查看可用的虚拟设备，它的图标看起来像下面这样：

Android Studio AVD Manager

如果你刚刚才安装 Android Studio，那么可能需要先创建一个虚拟设备。点击"Create Virtual Device..."，然后选择所需的设备类型并点击"Next"，然后选择Tiramisu API Level 33 image.

![Snipaste_2023-12-21_14-30-35](https://github.com/fsyud/astro-web/assets/26371465/4aa9303e-312c-41fb-bd1a-40dba00f1a1c)

> 译注：请不要轻易点击 Android Studio 中可能弹出的建议更新项目中某依赖项的建议，否则可能导致无法运行。

## 编译并运行 React Native 应用

确保你先运行了模拟器或者连接了真机，然后在你的项目目录中运行yarn android或者yarn react-native

此命令会对项目的原生部分进行编译，同时在另外一个命令行中启动Metro服务对 js 代码进行实时打包处理（类似 webpack）。Metro服务也可以使用yarn start命令单独启动。

```
<!-- 开启Metro服务 -->
yarn start
yarn android

# 或者
yarn start
yarn react-native run-android
```

![Snipaste_2023-12-21_14-32-07](https://github.com/fsyud/astro-web/assets/26371465/c7a677e9-0141-43e2-87b6-836bae44bdf1)

如果配置没有问题，你应该可以看到应用自动安装到设备上并开始运行。注意第一次运行时需要下载大量编译依赖，耗时可能数十分钟。此过程严重依赖稳定的代理软件，否则将频繁遭遇链接超时和断开，导致无法运行。

`npx react-native run-android`只是运行应用的方式之一。你也可以在 Android Studio 中直接运行应用。

译注：建议在run-android成功后再尝试使用 Android Studio 启动。请不要轻易点击 Android Studio 中可能弹出的建议更新项目中某依赖项的建议，否则可能导致无法运行。

如果你无法正常运行，遇到奇奇怪怪的红屏错误，先回头仔细对照文档检查，然后可以看看问题讨论区。不同时期不同版本可能会碰到不同的问题，我们会在论坛中及时解答更新。但请注意千万不要执行 bundle 命令，那样会导致代码完全无法刷新。

### 修改项目

现在你已经成功运行了项目，我们可以开始尝试动手改一改了：

使用你喜欢的文本编辑器打开App.js并随便改上几行
按两下 R 键，或是在开发者菜单中选择 Reload，就可以看到你的最新修改。
完成了！
恭喜！你已经成功运行并修改了你的第一个 React Native 应用

参考[<https://reactnative.cn/docs/environment-setup](https://reactnative.cn/docs/environment-setup>)

## 打包发布

Android 要求所有应用都有一个数字签名才会被允许安装在用户手机上，所以在把应用发布到应用市场之前，你需要先生成一个签名的 AAB 或 APK 包（Google Play 现在要求 AAB 格式，而国内的应用市场目前仅支持 APK 格式。但无论哪种格式，下面的签名步骤是一样的）。Android 开发者官网上的如何给你的应用签名文档描述了签名的细节。本指南旨在提供一个简化的签名和打包的操作步骤，不会涉及太多理论。

生成一个签名密钥
你可以用keytool命令生成一个私有密钥。在 Windows 上keytool命令放在 JDK 的 bin 目录中（比如C:\Program Files\Java\jdkx.x.x_x\bin），你可能需要在命令行中先进入那个目录才能执行此命令。

```
keytool -genkeypair -v -storetype PKCS12 -keystore my-upload-key.keystore -alias my-key-alias -keyalg RSA -keysize 2048 -validity 10000
```

![Snipaste_2023-12-21_14-40-43](https://github.com/fsyud/astro-web/assets/26371465/71aabfb9-4d9d-457c-928b-3245a1bd5741)

**keytool**需配置环境变量，通过`管理员身份`打开Powershell

此命令会提示您输入密钥库和密钥的密码以及密钥的专有名称字段。然后，它将密钥库生成为名为 my-upload-key.keystore. 的文件。

密钥库包含单个密钥，有效期为 10000 天。别名是您稍后在签署应用程序时将使用的名称，因此请记住记下别名。

### 设置 Gradle 变量

- 1.将`my-upload-key.keystore` 文件放置在项目文件夹的android/app 目录下。
  -2.编辑文件`~/.gradle/gradle.properties或android/gradle.properties`，并添加以下内容（将**\***替换为正确的密钥库密码、别名和密钥密码）

```
MYAPP_UPLOAD_STORE_FILE=my-upload-key.keystore
MYAPP_UPLOAD_KEY_ALIAS=my-key-alias
MYAPP_UPLOAD_STORE_PASSWORD=*****
MYAPP_UPLOAD_KEY_PASSWORD=*****
```

这些将是全局 Gradle 变量，我们稍后可以在 Gradle 配置中使用它们来签署我们的应用程序。

### 把签名配置加入到项目的 gradle 配置中

编辑你项目目录下的android/app/build.gradle，添加如下的签名配置：

```
...
android {
    ...
    defaultConfig { ... }
    signingConfigs {
        release {
            if (project.hasProperty('MYAPP_RELEASE_STORE_FILE')) {
                storeFile file(MYAPP_RELEASE_STORE_FILE)
                storePassword MYAPP_RELEASE_STORE_PASSWORD
                keyAlias MYAPP_RELEASE_KEY_ALIAS
                keyPassword MYAPP_RELEASE_KEY_PASSWORD
            }
        }
    }
    buildTypes {
        release {
            ...
            signingConfig signingConfigs.release
        }
    }
}
...
```

### 生成发行 APK 包

只需在终端中运行以下命令：

```
cd android
./gradlew assembleRelease
```

> 译注：cd android表示进入 android 目录（如果你已经在 android 目录中了那就不用输入了）。./gradlew assembleRelease在 macOS、Linux 或是 windows 的 PowerShell 环境中表示执行当前目录下的名为 gradlew 的脚本文件，且其运行参数为 assembleRelease，注意这个./不可省略；而在 windows 的传统 CMD 命令行下则需要去掉./。

> Gradle 的assembleRelease参数会把所有用到的 JavaScript 代码都打包到一起，然后内置到 APK 包中。如果你想调整下这个行为（比如 js 代码以及静态资源打包的默认文件名或是目录结构等），可以看看android/app/build.gradle文件，然后琢磨下应该怎么修改以满足你的需求。

> 注意：请确保 gradle.properties 中没有包含*org.gradle.configureondemand=true*，否则会跳过 js 打包的步骤，导致最终生成的是一个无法运行的空壳。

生成的 APK 文件位于`android/app/build/outputs/apk/release/app-release.apk`，它已经可以用来发布了。

### 运行apk文件

下载android模拟机 [MUMU](https://mumu.163.com/)

完成
![Snipaste_2023-12-21_14-47-16](https://github.com/fsyud/astro-web/assets/26371465/9eded7d7-d46a-4eb6-bc5d-e58c2e658dd8)

参考 [https://reactnative.dev/docs/signed-apk-android](https://reactnative.dev/docs/signed-apk-android)
