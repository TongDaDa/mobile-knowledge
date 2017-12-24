
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

### img 解决方案

在img元素上，我们可以通过使用js，根据dpr来设置使用*倍图，并修改对应的img。

应用这种方式时，img默认的src路径应该是一个缓冲图或是空，因为在你的脚本判断dpr进行替换img的src属性时，
其img元素已经存在页面上了，这时，如果你的默认src是1倍图，当脚本去设置多倍图路径时，两张图已经加载。所以
较优的解决方案是，给一个loading图(如果不放图，alt>`不美观`)

### 在css中使用background-image

css如果你非常勤快，热爱劳动。可以使用这种方式。

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

以 less 为例.

```
  /* mixin */
  .retina(@path,@size: auto auto){
    @fix: ~'(-webkit-min-device-pixel-ratio: 1.5),
        (-min--moz-device-pixel-ratio: 1.5),
        (-o-min-device-pixel-ratio: 4/3),
        (-webkit-min-device-pixel-ratio: 1.5)';

    @2xpath: ~`@{path}.replace(/\.\w+$/, function(match) { return "@2x" + match; })`;

    background: url(@path) @extras;
    background-size: @size;

    /* 在有些屏幕上 dpi 并不能表示 ppi 不建议这么做, 但是作为后退方案，只写一个。 */
    @media (min-resolution: 1.5dppx){
       background-image: url(http://www.***2@.png)
    }

    @media @lowretina {
      background      : url(@2xpath) @extras;
      background-size : @size;
    }
  }
```

device-pixel-ratio 这个 queries 是非标准的。

其他库

[retina.js](https://github.com/strues/retinajs)

目前对于retina的这个库，我使用过一次，但是没有发现，上面所说到的资源浪费问题的解决方式。

## 页面布局方案

### flex

### grid

## 总结

对于全局适配，一切源于`视口`，不管用什么适配方案，都离不开它。

对于页面布局适配，有时我们会有很多的选择，在兼容允许的情况下，应该有点追求，去使用新属性。

文献:

[大漠:retina 1px 解决方案](https://www.w3cplus.com/css/fix-1px-for-retina.html)
[大漠:flex发展史](https://www.w3cplus.com/css/vw-for-layout.html)
[flexible适配h5手淘方案](https://www.w3cplus.com/mobile/lib-flexible-for-html5-layout.html)
[布局视口(layout viewport)与视觉视口(visual viewport)](https://www.quirksmode.org/mobile/viewports2.html)