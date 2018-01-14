
## 全局适配方案

### flexible -> dpr transform scale

在rem还没出现和普及之前，这是手淘最早实现适配的方式。通过`视口宽度`来调整 ` viewport ` 元信息的 scale 值.

模拟代码:

```
((win,doc)=>{
 // <meta name="viewport" content="width=device-width,initial-scale=1,maximum-scale=1,user-scalable=no" />

 const viewWidth = document.documentElement.clientWidth，
       docEl = doc.documnetElement,
       dpr = win.devicePixelRatio,
       metaEl = doc.querySelector("meta[name:'viewport']"),

 let scale = 1;

 if (dpr) {
      scale = parseFloat((1 / dpr).toFixed(2))
    } else {
      scale = 1;
 }

 const setScale = () => {
    if (metaEl) {
      metaEl.setAttribute('content', 'initial-scale=' + scale + ', maximum-scale=' + scale + ', minimum-scale=' + scale + ', user-scalable=no');
    }
 }

 window.addEventListener('resize',setScale)

 window.addEventListener('pageshow',setScale)

})(window,document)

```

### flexible -> viewport transform rem

直接使用视口宽度，转换成10份(/10)，设置在根节点中.

```
 const vp = document.documentElement.clientWidth,
       docEl = document.documentElement;

 let rem = 100;

 cont setRem = () => {
    rem = vp / 10
    docEl.style.fontSize = rem;
 }

  window.addEventListener('resize',setRem)

  window.addEventListener('pageshow',setRem)

```

### flexible -> calculate viewport transform vw

vw这个单位可以直接代替上个方法的视口宽度，所以，我们不需要使用视口宽度再转换一次rem应用到根节点上了。本来flexible使用100来做转换比例就是为了以后可以兼容
vw这个单位，比如：

```
    .div {
      width: 120px; // -> 120 / 750 = .16vw
    }
```

## 多倍图解决方案

首先在大多数页面中，图片无非是以两种形式设置 css-background and img-element。

如果不要求兼容性的话，完全可以使用

```
 <img set-src="***@1x.jpg 2x, ***@3x.jpg 3x" />
 background-image: -webkit-imgage-set(url(***@1x.png) 1x,url(***@2x.png) 2x)
```

具体使用方法参考
[srcset](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/img)
[image-set](https://developer.mozilla.org/en-US/docs/Web/CSS/background-image)

腾讯从2015年就已经开始使用这些，推荐大家也赶快用起来，但是兼容的话，可以使用下面的解决方式.

### <img /> 解决方案

在img元素上，我们可以通过使用js，根据dpr来设置使用*倍图，并修改对应的img。

应用这种方式时，img默认的src路径应该是一个缓冲图或是空，因为在你的脚本判断dpr进行替换img的src属性时，
其img元素已经存在页面上了，这时，如果你的默认src是1倍图，当脚本去设置多倍图路径时，两张图已经加载。所以
较优的解决方案是，给一个loading图(如果不放图，alt>`不美观`)

### 使用background-image

如果你非常勤快，热爱劳动。可以使用这种方式。

```
  .banner {
    background-image: url(http://www.***1@.png)
  }

  @media (-webkit-min-device-pixel-ratio: 1.5){
      background-image: url(http://www.***3@.png)
      background-size : ***
  }
  @media (-min--moz-device-pixel-ratio: 1.5){
       background-image: url(http://www.***3@.png)
       background-size : ***
  }
  @media (-o-min-device-pixel-ratio: 3/4){
         background-image: url(http://www.***3@.png)
         background-size : ***
  }

```

哈哈，开个玩笑，我相信大家都是`懒人`。

我们可以使用 css 预编译语言写一个 mixin 。

以 scss 为例.

```
  /* mixin */
  .retina(@path,@size: auto auto){
    @fix: ~'(-webkit-min-device-pixel-ratio: 1.5),
        (-min--moz-device-pixel-ratio: 1.5),
        (-o-min-device-pixel-ratio: 3/2)';

    @2xpath: ~`@{path}.replace(/\.\w+$/, function(match) { return "@2x" + match; })`;

    background: url(@path) @extras;
    background-size: @size;

    /* 在有些屏幕上 dpi 并不能表示 ppi , 但是可以加上作为后退方案。 */
    @media (min-resolution: 1.5dppx){
       background-image: url(http://www.***2@.png)
    }

    @media @lowretina {
      background      : url(@2xpath) @extras;
      background-size : @size;
    }
  }
```

如何你使用postcss,别着急来看下[这个](https://github.com/SuperOl3g/postcss-image-set-polyfill)

> 开源世界，只有你想不到，咩有搜不到的...

`注： css device-pixel-ratio 这个 queries 是非标准的。`

`总结`一下:

通过 srcset attribute 来设置img资源路径 (ie Edge 以下不兼容)

通过 image-set() css方法来匹配资源 (兼容不佳)

如果不要求兼容完全可以使用以上两个属性。否则可以使用 [retina.js](https://github.com/strues/retinajs) 解决方式，

目前对于retina的这个库，我使用过一次，但是没有发现上面所说到的资源浪费问题的解决方式（1x&2x图全部加载）。

::可以 fork 下来，自行改造。 (:😳

## 页面布局方案

我一直很喜欢写手机端页面，第一是小巧，其次是我可以使用更多的布局方案. 写到这，脑海突然浮现出了用

### flex

使用场景主要是各式各样的对齐规则，如果使用传统布局，恐怕要折腾一会了，flex的最低兼容是安卓4.4与Safari7.1，基本上手机端是都可以兼容的，
使用规则不说了，说下具体的使用场景.

1. 水平排列布局
对于一个元素下的多个块元素，如果是经典布局我们可能会把它下面的子元素变成行块或浮动起来，以保持在一行。

```
 .list > li {
    display: inline-block;
 }
 //或
 .list > li {
     display: float-left;
  }
```

以上两种方法都指定了.list类下面的li，可是这样不够[DRY](http://vanseodesign.com/css/dry-principles/),当我们
的.list下有些改动，我们需要换成div，那么我们需要修改一下css。但使用flex则直接指定 .list 父元素一个属性，不用修改其子元素。
```
.list {
    display:flex;
}
```

这样实用性并不大，因为很多时候item是需要撑开一行的，这时候我们需要指定一行放多少个，且占据比例.

```
/*.scss*/
.list {
    display:flex;
    > li{
        flex-basic:33.333%
        flex:1;
        margin-right:5px;
        &:nth-child(3n){ margin-right:0; }
    }
}
```


2. 等边距排列

```
display:flex;
justifty: space-around;
```

3. 垂直居中

具体使用可以参考

[阮一峰 Flex](http://www.ruanyifeng.com/blog/2015/07/flex-grammar.html)
[MND Flex](https://developer.mozilla.org/en-US/docs/Web/CSS/flex)
[官方规范](https://drafts.csswg.org/css-flexbox-1/#flex-property)

### grid

## 总结

对于全局适配，一切源于`视口`大小，不管用什么适配方案，我们都离不开它。

对于页面布局适配，有时我们会有很多的选择，在兼容允许的情况下，应该有点追求，去使用新属性。

文献:

[大漠:retina 1px 解决方案](https://www.w3cplus.com/css/fix-1px-for-retina.html)
[大漠:flex发展史](https://www.w3cplus.com/css/vw-for-layout.html)
[flexible适配h5手淘方案](https://www.w3cplus.com/mobile/lib-flexible-for-html5-layout.html)
[布局视口(layout viewport)与视觉视口(visual viewport)](https://www.quirksmode.org/mobile/viewports2.html)