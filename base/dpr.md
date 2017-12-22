### dpr

dpr 是 devicePixelRatio(设备像素比) 的简写,此属性位于全局当中(可配置，可枚举)，它表示1个`css像素`比多少个物理像素(设备像素)。

比如，我们使用css设置一个50*50像素的正方形。在dpr为1的设备中，它的真实物理像素分辨率为50*50，但是在dpr为2的设备当中
却是100*100。

> 它的出现正是在**浏览器**与**设备**两者之间表明两端的`像素比例`。

device pixel(物理像素) = window.devicePixelRetio * css px

我们页面上的每个css像素都通过以上这个公式来转换，从而计算出物理像素反应到设备上。

#### 它出现的意义?

web发展快速，如今伴随着HTML5技术，如微信端，hybrid等场景的出现，现在的网页三剑客完全可以占据一部分移动端市场。显示屏技术也发生了
日新月异的变化，自从iphone4发布retina屏幕之后，其他的厂商如oppo,三星等都已经推出了自己的高清屏幕。

在这之前建议看一下我的
与 [什么是像素](https://github.com/TongDaDa/mobile-knowledge/blob/master/base/pixel.md) 两篇内容.

我们先假设，如果没有devicePixelRatio的出现，即css像素 1px 对应物理像素的 1px ，在`小设备`上到底是啥样的呢？

因为没有办法实现去除此属性，所以我使用ps模拟了一下(调整ppi)。

<img src="https://github.com/TongDaDa/mobile-knowledge/blob/master/img/ppiExample.png?raw=true" />

可见，它的体验效果并不好，因为通常pc厂商不会把ppi配置太高，毕竟视距与移动端不一样。所以导致了两端的像素不能完全统一。

浏览器为了解决这个问题，才出现了 devicePixelRatio 这个api，我认为它是浏览器厂商根据手机ppi所设置的比例值.

由于pc的ppi与mobile的ppi差距较大，导致的你在pc上看100px可能是长长的一条线，手机上看可能就那么一丁丁。当然，
对于苹果的 [Retina屏幕](https://github.com/TongDaDa/mobile-knowledge/blob/master/base/retina.md)  又是另外一回事.

这样的情况下，我们需要有一个平台到设备之间的衔接，以更好的达到适配效果。

> 需要注意的是，虽然我们可以修改这个属性，但这并没有什么意义。尽量不要这么做，如果这个修改操作出现在你`适配方法`之前会造成意料之外
的后果.

iphone4, 4s, 5, 5s, 6, 6s的ppi都是326，dpr都是2，6+和6s+ppi是441，dpr是3

<img src="https://github.com/TongDaDa/mobile-knowledge/blob/master/img/iphone-dpi.png?raw=true" />

### dppx

dppx 表示一个css像素可以有表示多少个物理像素，与dpr都是一样的，只是说法不一样。

文献

[问答-dpr与dppx的区别？](https://www.zhihu.com/question/29226201)

[问答-dp*单位的来源及疑问？](https://www.zhihu.com/question/29226201)

[第一个回答-px,dpr周边技术介绍](https://www.zhihu.com/question/35221839/answer/66825618)

[dpr浅析](http://www.jianshu.com/p/221bebfae266)