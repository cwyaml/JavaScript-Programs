> 给自己提出一个挑战。用纯 JavaScript 来写东西，不借助框架和库，也不使用编译器和引用。
> 每个小程序都会给出详细的教程，保证每个新手都能看懂（不出意外的话每天都会更新） <br>
>
> **项目地址：** [cwyaml](https://github.com/cwyaml)/**[JavaScript-Programs](https://github.com/cwyaml/JavaScript-Programs)**  <br>
> **教程目录：** [JavaScript 每天一个小程序](http://www.jianshu.com/p/81d379ba9003) <br>
>
> 如果喜欢的话 请给个   ★star  非常感谢！！

![](http://upload-images.jianshu.io/upload_images/4030390-00c23c03d7ab652c.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

第十天，一起来认识一下 **图片的 Base64 位编码** 。这有利于优化你的网页。

最后做一个小网页，实现用户上传图片，点击按钮获得该图片的 Base64 位编码的功能。

## 前言
图片处理在前端工作中可谓占据了很重要的一壁江山。而图片 Base64编码 可能相对一些人而言比较陌生，本文希望通过一些浅显的论述，让你知道什么是图片的 base64编码，为什么我们要用它，以及如何方便的使用它。

### 图片的Base64编码简介
图片的 base64编码，就是将一张图片数据编码成一串字符串，使用该字符串代替图像地址。

这样做有什么意义呢？我们知道，我们所看到的网页上的每一张图片，都是通过消耗一个 http请求 下载而来的。没错，每张图片都要请求一次，如果你的网页有非常多的图片，必定要消耗很大一部分资源。

我们就想了：要是图片的下载不用向服务器发出请求，而可以随着 HTML 的下载同时下载到本地那就太好了。而 base64 正好解决了这个问题。

Base64 长什么样子？ 这里贴出百度首页图片的编码：
```
data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAhwAAAECCAMAAACCFP44AAAACXBIWXMAAAsTAAALEwEAmpwYAAAKTWlDQ1BQaG90b3Nob3AgSUNDIHByb2ZpbGUAAHjanVN3WJP3Fj7f92UPVkLY8LGXbIE...........wIcCDAgQAHAhwIcCDAgQAHAhwIcCDAgSDAgQAHAhwIcCDAgQAHAhwIcCDAgQAHggAHAhwIcCDAgfg1/x8Ate1CIJg9Qs4AAAAASUVORK5CYII=
```

怎么使用呢？

在 HTML 中的写法：
``` html
<img src="data:image/gif;base64,R0lGODlhHAAmAKIH….....">
```

在 CSS 中的写法：
``` css
#box{
background: url(data:image/gif;base64,R0lGODlhHAAmAKIH.......) no-repeat center;
}
```

不得不说的是，这个技术已经过时了，因为图片经过编码后体积会变大（一张 200k 的图片编码后 大概 240K 左右）。尽管节省了 http 请求，但也带来了打开网页时拥堵的问题，有点得不偿失。所以只有很小的图片会考虑使用一下。

> 目前已经有新的技术来代替：**CssSprites** 。以后会讲。
> 如果你不感兴趣就不必在往下看了。。

本着折腾精神，还是研究一下。现在开始动手做一个获取图片 Base64 位编码的小网页。

### 首先实现图片上传及预览
1、如果仅仅是上传，我们都知道只需要一个 input 标签就可以完成。就像这样：
``` html
<div id="box">
    <img id="preview" src="" alt="">
    <div id="btn">
      <input type="file" id="file" accept="image/*">
      <button type="button">获取 Base64 编码</button>
    </div>
    <textarea name="base64" rows="15" cols="60"></textarea>
  </div>
```
`<img>` 标签时预览照片的位置，`<textarea>` 用来显示编码。
> 注意限制图片上传类型为 图片。

2、做了一点布局
``` css
html{
  font-size: 625%;
}
#box{
  width: 100%;
  height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
}
img{
  width: 5rem;
  height: 3rem;
  border: 1px solid #ccc;
  overflow: hidden;
}
#btn{
  width: 1.5rem;
  margin: 0 .1rem;
  display: flex;
  flex-direction: column;
}
#btn button{
  margin: .1rem 0;
}
```

2、预览怎么实现呢？？我们可以使用 **FileReader** 。[参考文档](https://developer.mozilla.org/zh-CN/docs/Web/API/FileReader) 中有完整的预览本地文件的代码。

仿照给出的源码写一个预览功能：
``` js
var inputFile = document.getElementById('file');
inputFile.onchange = function imgPreview(){
  //判断是否支持FileReader
  if (window.FileReader) {
      var reader = new FileReader();
  } else {
      alert("换个浏览器试一下！");
  }
  //获取文件
  var file = this.files[0];
  reader.onload = function(e) {
      //获取图片dom
      var img = document.getElementById("preview");
      //图片路径设置为读取的图片
      img.src = e.target.result;
  };
  reader.readAsDataURL(file);
}
```
- 这个构造函数目前 Opera 和 Safari 还不支持，所以判断一下。
- input 上传的文件是以 list 形式存在的，所以 `files[0]` 即代表你上传的图片。

3、上传张图片试一下：

![](http://upload-images.jianshu.io/upload_images/4030390-7c6f8d4ee4f3b472.gif?imageMogr2/auto-orient/strip)

注意开发者工具中的变化，上传图片后 `<img>` 的 src 有了值，并且这个值就是 base64 编码。

4、这就好办了，我们在写个获取 这个编码的函数。
``` js
var button = document.getElementById('button');
button.onclick = function(){
  var preview = document.getElementById('preview');
  var textarea = document.getElementById('textarea');
  textarea.innerHTML = preview.src;
}
```

这样应该就可以了，我们看一下效果：

![](http://upload-images.jianshu.io/upload_images/4030390-983738910af76b82.gif?imageMogr2/auto-orient/strip)

确实可以获得 Base64 编码。

到此功能实现。。

> 文章中如果有错误欢迎指出，非常乐意和大家一起交流。
