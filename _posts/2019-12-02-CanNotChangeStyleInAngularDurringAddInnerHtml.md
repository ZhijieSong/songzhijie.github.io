---
layout: post
title:  "Angular中添加InnerHtml无法现实scss样式"
author: seven
categories: [ Angular ]
image: "https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1575229009713&di=f93b608fbc1ab73ac29e5c88520563d7&imgtype=0&src=http%3A%2F%2Fimg1.qunarzz.com%2Ftravel%2Fd5%2F1610%2F3b%2Fad59834b3e4778b5.jpg_r_720x480x95_11700d9d.jpg"
tags: [angular, html, js]
---

# 无法应用.css文件中的样式
近期在做一个angular的小工具，通过ts动态更新元素的innerHtml属性的时候发现元素是追加上去了，但是样式却没有应用，作为一个面向搜索引擎的开发者，找了很久之后终于获得了想要的答案，话不多说，代码如下：

html代码:
```html
<div class="preview-container" id="preview-container" [innerHtml] = "content">
</div>
```
ts代码:
```ts
import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'app-preview',
  templateUrl: './preview.component.html',
  styleUrls: ['./preview.component.scss']
})
export class PreviewComponent implements OnInit {
  content = null;
  constructor() {}
  ngOnInit() {
     this.content = "<h1></h1>";
  }
}
```
scss样式:
```css
h1{
  background-color: yellow;
}
```
本来期望是在组件初始化的时候追加一行h1元素，背景为黄色，但是由于angular给css的class加入了前缀，或者对html元素的一些属性做了自动的更改，导致你在自己追加innerHtml代码的时候，这些css样式选择器无法匹配，所以最终解决办法就是关闭angular的自动添加前缀功能，修改后的ts代码如下：
```ts
import { Component, OnInit } from '@angular/core';
import { ViewEncapsulation } from '@angular/core';

@Component({
  selector: 'app-preview',
  templateUrl: './preview.component.html',
  encapsulation: ViewEncapsulation.None,
  styleUrls: ['./preview.component.scss']
})
export class PreviewComponent implements OnInit {
  content = null;
  constructor() {}
  ngOnInit() {
     this.content = "<h1></h1>";
  }
}
```
# 无法应用sytle属性中添加的样式
另外值得一提的是，在查找上面的问题时候，发现一个类似的问题，就是在设置innerHtml时候，如果你的innerHtml中添加的代码设置了style属性来调整其样式，那么很可能是不会起作用的，这个应该是出于安全考虑（浏览器防止注入攻击？），对于innerHtml中style属性未进行加载渲染，解决办法也很简单，就是利用angular自带的DomSanitizer进行转换一下即可：

```ts
import { Component, OnInit } from '@angular/core';
import { ViewEncapsulation } from '@angular/core';
import { DomSanitizer} from '@angular/platform-browser';

@Component({
  selector: 'app-preview',
  templateUrl: './preview.component.html',
  encapsulation: ViewEncapsulation.None,
  styleUrls: ['./preview.component.scss']
})
export class PreviewComponent implements OnInit {
  content = null;
  constructor(private sanitized: DomSanitizer) {}
  
ngOnInit() {
    this.content =  this.sanitized.bypassSecurityTrustHtml( "<h1 style="font-size: 80px"></h1>");
  }
}
```

当然，也可以写成pipe的形式，这样就不需要修改前面的component.ts文件，也方便代码重用：
Pipe代码如下：
```ts
import { Pipe, PipeTransform } from '@angular/core';
import { DomSanitizer} from '@angular/platform-browser';

@Pipe({
  name: 'html'
})
export class HtmlPipe implements PipeTransform {
  constructor(private sanitized: DomSanitizer) {}
  
  transform(value: string): any {
    return this.sanitized.bypassSecurityTrustHtml(value);
  }
}
```
html文件如下：
```html
<div class="preview-container" id="preview-container" [innerHtml] = "content | html">
</div>
```
到此为止，就可以顺利的更新innerHtml代码了，如有疑问，欢迎留言讨论。
