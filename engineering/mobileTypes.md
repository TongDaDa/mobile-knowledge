+ browser HTML 5
 - 单纯的浏览器驱动页面，在浏览器中运行，但是比起下面这几个，没有native的感觉，用着不方便。因为每次用你的产品
 都要打开浏览器，用户体验也随之受限。

+ (Android | IOS) Hybrid
  - Hybird 目前比较流行，指原生App内嵌web页面，实现上是由一个webview层套前端页面，实现一种新模式：Native 做系统层，h5更偏向于业务层，主要优势是
    - 面度复杂的大型App，由于h5页面是动态的不计入Native应用中，所以可以大大减少包体的大小，这一点特别适合电商类网站，因为它们业务链复杂多变。
    - 从公司的角度来讲，原生的native应用可以减少资金投入，相比与三端(Android,IOS,Web)工程师的成本减少太多了Andriod 与 IOS 工程师 可以不用那么多了。
    - 面对多变的业务场景非常有优势，你想换个活动，难道还要打包上传一次App吗？如果是Hybird，直接上传你的页面.ok
    - Hybird模式从很早以前就开始有的，早期 facebook 等网站都使用这种方法，但是它不被苹果看好，原因是它的一个缺点，就是没有Native应用流畅.
    - 针对这个缺点，阿里对他们的Hybird也做了类似的优化，说是引入 UC 的 sdk，不过只能更接近原生应用，但终归是不能与之匹敌，面对它能解决的场景需求这点缺点还是能接受的。

+ React Native
  - faceBook的跨平台框架，用同一套代码，实现两端(ios,android)应用。这个应该应该脱离了前端的范畴

+ PhoneGap & Cordova
    - phoneGap的第一行代码源于2008年，起初就是为了解决使用前端代码来写Android与ios应用，后来被Adobe收购，捐赠给Apache,Apache将这个项目命名为Cordova,之后Abode发布了
      phoneGap-Build,它们的合体就是以前老版本的 phone-gap (Cordova这是一个街道的名字,也是名字的由来)
    - phoneGap是帮你完成使用前端代码调用iOS Android 等移动设备的接口，比如摄像头，地理位置，仪表板，声音，联系人等。

+ weChat
    - 小程序或微信内嵌的页面，除了页面外，也包括调用微信平台的接口，比如获取定位，获取微信用户信息等等。从底层向上看就是 -> 系统 -> 微信native -> h5页面


总结:

PhoneGap 与 Hybrid 都属于调用native原生，只不过 PhoneGap 只给你预留了一些现成的插件，而 Hybrid 自己实现则更灵活一些。
