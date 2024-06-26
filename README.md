## 变更项

手机端H5项目引用该仓库时，IOS端存在问题，XMLHttpRequest报错。
分析发现，IOS端会将本地文件转换为file:///......路径文件，安卓端为https://......路径文件，代码调用XMLHttpRequest转换文件为arrayBuffer格式，IOS因协议
问题报错。
因此本仓库做出调整，兼容IOS系统文件格式转换，详细步骤如下：
1. 先使用[工具](https://www.bejson.com/ui/file_to_base64/)将svga文件转换为base64字符串格式保存至项目txt文件中。
2. 在项目中引入文件字符串源数据，替换paser.load中的第一个参数路径即可。
例：
```
<template>
    <div id="test" class="demo_canvas"></div>
</template>
<script setup>
import red from '@/assets/red.txt?raw'
import { onMounted } from 'vue'
onMounted(() => {
  var player = new SVGA.Player('#test')
  var parser = new SVGA.Parser('#test')
  parser.load(red, function(videoItem) {
    player.setVideoItem(videoItem)
    player.startAnimation()
  })
})
</script>
```

# Archived

本仓库已经停止维护，你仍然继续阅读源码及创建分叉，但本仓库不会继续更新，也不会回答任何 issue。

This repo has stopped maintenance, you can still continue to read the source code and create forks, but this repo will not continue to be updated, nor will it answer any issues.

# SVGAPlayer-Web

Language: [中文](README.zh.md)

SVGAPlayer 现已支持在微信小程序播放动画，详情请[参阅](https://github.com/yyued/SVGAPlayer-Web/tree/mp)。

## 支持本项目

1. 轻点 GitHub Star，让更多人看到该项目。

## News

* **[Lite Version](https://github.com/svga/SVGAPlayer-Web-Lite)**
* 2.3.0 - Add audio support.

## Can I Use

SVGAPlayer 2.0.0 only supports below browsers.

* Edge / IE 6+
* Safari / Chrome
* iOS 6.0+ / Android 4.0+

SVGAPlayer 2.0.0 also supports below Game Engines.

* CreateJS [Usage](CreateJS.README.md)
* LayaBox [Usage](LayaBox.README.md)

## Install

### Prebuild JS

1. Add ```<script src="https://cdn.jsdelivr.net/npm/svgaplayerweb@2.3.1/build/svga.min.js"></script>``` to your.html

### NPM

1. ```npm install svgaplayerweb --save```
2. Add ``` require('svgaplayerweb') ``` to ```xxx.js```

### IE6 ~ IE9

* IE6+ only supports 2.x format.
* You couldn't use npm to install SVGA library.

1. Add [SVGAPlayerWeb.swf](https://github.com/yyued/SVGAPlayer-Web/blob/master/tests/SVGAPlayerWeb.swf) to your.html locate directory.
2. Add following code to your.html

```
<!--[if lt IE 10]> 
    <script src="../build/svga.ie.min.js"></script>
<![endif]-->
<!--[if gte IE 10]><!-->
    <script src="../build/svga.min.js"></script>
<!--<![endif]-->
```

### Audio support

If your need to play audios, add ```howler.min.js``` to your html.

```html
<script src="https://cdn.jsdelivr.net/npm/howler@2.0.15/dist/howler.core.min.js"></script>
```

Notice: audio plays needs browser support, some browser requires user interaction before playing.

### SVGA-Format 1.x support

Both Prebuild & NPM, if you need to support SVGA-Format 1.x, add JSZip script to html.

```html
<script src="//s1.yy.com/ued_web_static/lib/jszip/3.1.4/??jszip.min.js,jszip-utils.min.js" charset="utf-8"></script>
```

## Usage

### Load Animation Mannally

You may create Player and Parser by yourself.

1. Add Div Tag.

```html
<div id="demoCanvas" style="styles..."></div>
```

2. Load Animation

```js
var player = new SVGA.Player('#demoCanvas');
var parser = new SVGA.Parser('#demoCanvas'); // Must Provide same selector eg:#demoCanvas IF support IE6+
parser.load('rose_2.0.0.svga', function(videoItem) {
    player.setVideoItem(videoItem);
    player.startAnimation();
})
```

### Load Animation Automatically

Assign canvas element properties as below.

```html
<div src="rose_2.0.0.svga" loops="0" clearsAfterStop="true" style="styles..."></div>
```

Animation will play after Web-Page onload.

### Replace Animation Images Dynamically

You can replace specific image by yourself, ask your designer tell you the ImageKey.

```
player.setImage('http://yourserver.com/xxx.png', 'ImageKey');
```

### Add Text on Animation Image Dynamically

You can add text on specific image, ask your designer tell you the ImageKey.

```
player.setText('Hello, World!', 'ImageKey');
```

```
player.setText({ 
    text: 'Hello, World!', 
    family: 'Arial',
    size: "24px", 
    color: "#ffe0a4",
    offset: {x: 0.0, y: 0.0}
}, 'ImageKey'); // customize text styles.
```

## Classes

### SVGA.Player

You use SVGA.Player controls animation play and stop.

#### Properties

* int loops; - Animation loop count, defaults to 0 means infinity loop.
* BOOL clearsAfterStop; - defaults to true, means player will clear all contents after stop.
* string fillMode; - defaults to Forward，optional Forward / Backward，fillMode = Forward，Animation will pause on last frame while finished，fillMode = Backward , Animation will pause on first frame.

#### Methods

* constructor (canvas); - first params could be '#id' or CanvasHTMLElement
* startAnimation(reverse: boolean = false); - start animation from zero frame.
* startAnimationWithRange(range: {location: number, length: number}, reverse: boolean = false); - start animation in [location, location+length] frame range.
* pauseAnimation(); - pause animation on current frame.
* stopAnimation(); - stop animation, clear contents while clearsAfterStop === true
* setContentMode(mode: "Fill" | "AspectFill" | "AspectFit"); - Specific Scale Mode
* setClipsToBounds(clipsToBounds: boolean); - Clips if image render out of box.
* clear(); - force clear contents.
* stepToFrame(frame: int, andPlay: Boolean); - stop to specific frame, play animation while andPlay === true
* stepToPercentage(percentage: float, andPlay: Boolean); - stop to specific percentage, play animation while andPlay === true
* setImage(image: string, forKey: string, transform: [a, b, c, d, tx, ty]); - Replace Animation Images Dynamically, transform is optional, transform could adjust replacing image.
* setText(text: string | {text: string, family: string, size: string, color: string, offset: {x: float, y: float}}, forKey: string); - Add Text on Animation Image Dynamically
* clearDynamicObjects(); - clear all dynamic objects.

#### Callback Method
* onFinished(callback: () => void): void; - call after animation stop.
* onFrame(callback: (frame: number): void): void; - call after animation specific frame rendered.
* onPercentage(callback: (percentage: number): void): void; - call after animation specific percentage rendered.

### SVGA.Parser

You use SVGA.Parser load VideoItem from remote or Base64 string.

Only Cross-Domain allow files could be loaded.

If you eager to load resources from Base64 or File, deliver as ```load(File)``` or ```load('data:svga/2.0;base64,xxxxxx')```.

#### Methods

* constructor();
* load(url: string, success: (videoItem: VideoEntity) => void, failure: (error: Error) => void): void;

## Issues

### Android 4.x Breaks

As known, some Android OS lack Blob support, add Blob Polyfill by yourself.

```
<script src="//cdn.bootcss.com/blob-polyfill/1.0.20150320/Blob.min.js"></script>
```
