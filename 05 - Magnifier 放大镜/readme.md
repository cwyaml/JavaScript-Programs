> 给自己提出一个挑战。用纯 JavaScript 来写东西，不借助框架和库，也不使用编译器和引用。
> 每个小程序都会给出详细的教程，保证每个新手都能看懂（不出意外的话每天都会更新） <br>
>
> **项目地址：** [cwyaml](https://github.com/cwyaml)/**[JavaScript-Programs](https://github.com/cwyaml/JavaScript-Programs)**  <br>
> **教程目录：** [JavaScript 每天一个小程序](http://www.jianshu.com/p/81d379ba9003) <br>
>
> 如果喜欢的话 请给个   ★star  非常感谢！！

第五天，做一个网店都有的图片放大功能：**Magnifier**

## 实现效果

![](http://upload-images.jianshu.io/upload_images/4030390-997d0e0ea8bdebc3.gif?imageMogr2/auto-orient/strip)

## 项目分析
1、首先可以确定页面中必须有两长相同的图片，一直展示的照片要缩小，被放大的图片设置原始大小；

2、右侧的图片要默认隐藏，只有当鼠标放在左侧图片上时才显示；

3、在左侧图片上还要有一个 选择框 跟随鼠标的移动，代表要放大的图片范围，但不能移动出图片范围；

4、还要注意 两张图片 还有 选择框 尺寸之间的关系。

下面来动手实现一下。。。

### 基本的布局
``` html
<span id="sp1"></span>
<img src="timg.jpg" alt="" id="img1">
<div id="box">
    <img src="timg.jpg" alt="" id="img2">
</div>
```
1、两张图片分别为 `img1` 和 `img2`;

2、`<span>` 为选择框，在 `img1` 上方；

3、`img2` 外要有一个盒子，来限制图片的显示范围

### 添加样式
``` css
#sp1{
  width: 6rem;
  height: 4rem;
  background: rgba(62,54,58,.4);
  position: absolute;
  left: 0;
  top: 0;
}
#img1{
  width: 36rem;
  height: 22.5rem;
}
#img2{
  width: 288rem;
  height: 180rem;
  position: absolute;
}
#box{
  width: 48rem;
  height: 32rem;
  position: absolute;
  left: 37rem;
  top: 0;
  overflow: hidden;     
  display: none;  
}
```

![](http://upload-images.jianshu.io/upload_images/4030390-b64e4a31ea26c339.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

1、单位使用 `rem`，基础长度为 10px；

2、上面说过 box 的作用是限制 img2 的显示范围，`overflow: hidden;` 使超出 box 大小的部分隐藏；

3、页面中一共有四个“盒子”，分别为 img1、img2、sp1 和 box。他们的尺寸要有一定的关系，后面再说，你可以先大致设置一下。

### JS部分
``` js
var img1 = document.getElementById('img1');
var sp1 = document.getElementById('sp1');
var oBox = document.getElementById('box');
var img2 = document.getElementById('img2');
sp1.onmouseover = function(){
  oBox.style.display = 'block';
}
sp1.onmouseout = function(){
  oBox.style.display = 'none';
}
document.onmousemove = function(evt){
  var event = evt || window.event;
  var x = event.clientX - sp1.offsetWidth / 2;
  var y = event.clientY - sp1.offsetHeight / 2;
  if(x < 0){ x = 0 }
  if(y < 0){ y = 0 }
  if(x > img1.offsetWidth - sp1.offsetWidth){
    x = img1.offsetWidth - sp1.offsetWidth;
  }
  if(y > img1.offsetHeight - sp1.offsetHeight){
    y = img1.offsetHeight - sp1.offsetHeight;
  }
  if(event.clientX < img1.offsetWidth && event.clientY < img1.offsetHeight){
    sp1.style.left = x + 'px';
    sp1.style.top = y + 'px';
    img2.style.left = -x * (img2.offsetWidth / img1.offsetWidth) + 'px';
    img2.style.top = -y * (img2.offsetHeight / img1.offsetHeight) + 'px';
  }
}
```

1、首先获取页面中的四个“盒子”;

2、前面分析过，img2 只有在鼠标放在 “选择框” （即 sp1）上时才能显示，
``` js
sp1.onmouseover = function(){
  oBox.style.display = 'block';
}
sp1.onmouseout = function(){
  oBox.style.display = 'none';
}
```
这个应该都能明白。。

3、x 和 y 是我们用来设定选择框的 left 和 top 值的，`event.clientX、event.clientY` 获取当前鼠标的坐标，在减去选择框自身的一般即为选择框左上角应该的坐标值。
``` js
var x = event.clientX - sp1.offsetWidth / 2;
var y = event.clientY - sp1.offsetHeight / 2;
```

4、再来考虑这个选择框应该在 img1 上，而不能跑出去，所以我们要对 x 和 y 做一些限制，
``` js
if(x < 0){ x = 0 }    //左侧
if(y < 0){ y = 0 }    //上侧
if(x > img1.offsetWidth - sp1.offsetWidth){    //右侧
  x = img1.offsetWidth - sp1.offsetWidth;
}
if(y > img1.offsetHeight - sp1.offsetHeight){    //下侧
  y = img1.offsetHeight - sp1.offsetHeight;
}
```
5、选择框要跟随着鼠标移动，准确来说应该是当鼠标移入 img1 后，选择框要跟着鼠标移动。
``` js
if(event.clientX < img1.offsetWidth && event.clientY < img1.offsetHeight){
  sp1.style.left = x + 'px';
  sp1.style.top = y + 'px';
}
```
6、当选择框的 left 和 top 值确定了，我们是不是只要考虑选择框和 img2 之间的关系就可以确定 img2 的 left 和 top了。

页面中四个盒子的尺寸要满足这样的关系：**sp1/img1 = box/img2**，这样才能使 **选择框 相当 于box**。你可以仔细理解一下这句话，其实就是一种比例关系。

可以回头重新设置他们四个的尺寸。。

到此，这个效果全部实现了。。。

> 文章中如果有错误欢迎指出，非常乐意和大家一起交流。
