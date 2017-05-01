> 给自己提出一个挑战。用纯 JavaScript 来写东西，不借助框架和库，也不使用编译器和引用。
> 每个小程序都会给出详细的教程，保证每个新手都能看懂（不出意外的话每天都会更新） <br>
>
> **项目地址：** [cwyaml](https://github.com/cwyaml)/**[JavaScript-Programs](https://github.com/cwyaml/JavaScript-Programs)**  <br>
> **教程目录：** [JavaScript 每天一个小程序](http://www.jianshu.com/p/81d379ba9003) <br>
>
> 如果喜欢的话 请给个   ★star  非常感谢！！

第四天，做一个可以随便扔的重力球。

## 效果预览

![TeleGrav](http://upload-images.jianshu.io/upload_images/4030390-a5f4c22bed6d3f99.gif?imageMogr2/auto-orient/strip)

## 思路分析
1、首先我们得做一个小球，并且能使用鼠标控制（也就是拖拽功能）

2、小球在页面中反弹的效果我们可以用一个定时器不断的计算小球的 **left** 和 **top** 值来实现。

## 先实现拖拽功能
以正方形来了解一下拖拽的原理，希望自己能说清楚吧。
在屏幕上放下一个正方形
``` css
<div id="box"></div>
html,body{
  margin: 0;
  padding: 0;
}
#box{
  width: 100px;
  height: 100px;
  background: #f00;
  position: absolute;
  left: 0;
  top: 0;
}
```
先给上代码，然后在细说
``` js
var oBox = document.getElementById('box');
oBox.onmousedown = function(evt){
  var oEvent = evt || window.event;
  var iLeft = oEvent.offsetX;   
  var iTop = oEvent.offsetY;   
  maxL = document.documentElement.clientWidth - oBox.offsetWidth,
  maxT = document.documentElement.clientHeight - oBox.offsetHeight;

  document.onmousemove = function(evt){
    var oEvent = evt || window.event;

    var left = oEvent.clientX - iLeft;
    var top = oEvent.clientY - iTop;

    if(left <= 0) {left = 0}
    if(top <= 0) {top = 0}
    if(left >= maxL) {left = maxL}
    if(top >= maxT) {top = maxT}

    oBox.style.left = left+'px';
    oBox.style.top = top+'px';

    speedX = left - lastX;
    speedY = top - lastY;
    lastX = left;
    lastY = top;      //这四个值后面会用到，拖拽里没有用到
  }
  document.onmouseup = function(){
    document.onmousemove = null;
  }
}
```
1、`maxL` 和 `maxT` 前面没有 `var` 是因为声明一个全局变量，后面还要用。

2、`event` 是一个事件对象。可以通过这个事件对象获得一些信息：导致事件的元素、事件的类型等。Chrome、FF等浏览器中可以直接获得这个对象，IE 中要用 `window.event` 获得，所以做一下兼容。

3、`oEvent.offsetX` 和 `oEvent.offsetY` 用来获取点击 box 时鼠标指针的位置距离 距离 box 左侧和顶部的距离。如下图：

![](http://upload-images.jianshu.io/upload_images/4030390-f35264a73c646f13.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

4、得到上面的距离后，我们就可以间接计算出 box 左上角距离浏览器左上角的距离。即:
``` js
left = event1.clientX - event2.offsetX;
top = event1.clientY - event2.offsetY;
```

5、**注意：** `event1` 指的是 `document` 对象，`event2` 指的是 `box` 对象。所以不能这样写：
``` js
var left = oEvent.clientX - oEvent.offsetX;
var top = oEvent.clientY - oEvent.offsetY;
```
同样也不能使用 `oBox.offsetLeft` 直接得到 box 左上角距离浏览器左侧的距离，因为这是个定值，永远不会改变。

6、实际上我们得到的 left 和 top 画出了 box 左上角可变化的范围，如下图所示：

![](http://upload-images.jianshu.io/upload_images/4030390-6fa71f341c69b0b9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

所以我们要判断当前 left 和 top 是否超出了这个范围：
``` js
if(left <= 0) {left = 0}
if(top <= 0) {top = 0}
if(left >= maxL) {left = maxL}
if(top >= maxT) {top = maxT}
```

7、最后把最后的 left 和 top 应用给 box：
``` js
oBox.style.left = left+'px';
oBox.style.top = top+'px';
```

8、鼠标松开后，将 onmousemove 事件移除
``` js
document.onmouseup = function(){
      document.onmousemove = null;
}
```
### 效果：

![拖拽.gif](http://upload-images.jianshu.io/upload_images/4030390-35bde29baa47958a.gif?imageMogr2/auto-orient/strip)

这就是拖拽，这么分析下来也是挺简单的。

9、还有一点，要把这个正方形变成一个小球，很简单：
``` css
#box{
  border-radius: 50%;
}
```

##### 接着昨天的往下讲。。。。（说好的一天一个小程序呢？？）

下面来给小球加上 **“重力”** 。

### 何时开始有重力
前面说过，我们是通过定时器不断计算小球的 left 和 top 来实现这个反弹效果的。我们来思考一下什么时候触发这个定时器？当页面刷新后，小球在左上角，我们不点击它就不会有任何效果。当我们点击后，还是不应该触发，而是等拖拽动作完成，并且松开鼠标后触发。所以，我们应该这也做：
``` js
document.onmouseup = function(){
  document.onmousemove = null;
  startMove();
}
var timer = null;
function startMove(){
  timer = setTimeout(function(){
    ....
  },16);
}
```
1、`startMove()` 是我们用来给小球添加“重力”的函数。结构就是这样。

### “重力”
先给上代码吧，这样讲解比较方便
``` js
var timer = null;
function startMove() {
  var nowX = oBox.offsetLeft;
  var nowY = oBox.offsetTop;
  clearInterval(timer);    //清除定时器，这个是避免和上一次冲突
  timer = setInterval(function() {
    speedY += 2;		
    nowX += speedX;
    if (nowX > maxL) {
      speedX *= -0.8;		
      nowX = maxL;
    } else if (nowX < 0) {		
      speedX *= -0.8;				
      nowX=0;								
    }
    nowY += speedY;
    if (nowY > maxT) {
      speedY *= -0.8;
      speedX *= 0.8
      nowY = maxT;
    } else if (nowY < 0) {
      speedY *= -1;
      speedX *= 0.8;
      nowY = 0;
    }
  if (Math.abs(speedX) < 1) {
    speedX = 0;
  }
  if (Math.abs(speedY) < 1) {
    speedY = 0;
  }
  if (speedX == 0 && speedY == 0 && nowY == maxT) {
    clearInterval(timer);
  }
  oBox.style.left = nowX + 'px';
  oBox.style.top = nowY + 'px';
  }, 25)
}
```

1、首先 `var nowX = oBox.offsetLeft;var nowY = oBox.offsetTop;`获取 box 当前的坐标（被拖拽后）。

2、定时器为什么要选择 16 秒？因为肉眼可识别的帧率是60帧，所以每 16ms 改变一次小球的位置我们看起来就是连续的动画。

3、还记得拖拽中的那四个变量吗？分别是：`lastX、lastY`记录上一次拖拽 box 的坐标； `speedX、speedY`这次拖拽后 box 的坐标相对上次的变化量。

4、假如第一次 box 在 box1 处，拖拽后在 box2 处。则`lastX = a;lastY = c;` `speedX = b - a;speedY = d - c;`

![](http://upload-images.jianshu.io/upload_images/4030390-c5c6a332531ad115.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

5、你可以把 speedX 、speedY 理解为鼠标拖动小球走的速度。因为执行一次循环的时间是一定的（16ms），这个值越大，说明鼠标移动的越快。同时也是每次执行定时器 box 的 left 或 top 的改变量。
``` js
nowX += speedX;
nowY += speedY;
```

6、值得注意的是：当没有触碰到边界时，nowX 和 nowY 的改变量是不会改变的。只有当触碰到边界时，这个值才会 `*0.8 || *-0.8` 。
``` js
nowX += speedX;
//判断 box 是否超出了 可移动范围；
if (nowX > maxL) {
	speedX *= -0.8;		//反向
	//alert(speedX);
	nowX = maxL;
} else if (nowX < 0) {		//判断是否碰到左侧
	speedX *= -0.8;				//反向
	nowX = 0;								//迅速移入屏幕
}

nowY += speedY;
if (nowY > maxT) {
	speedY *= -0.8;
	//speedX *= 0.8
	nowY = maxT;
} else if (nowY < 0) {
	speedY *= -1;
	//speedX *= 0.8;
	nowY = 0;
}
```

7、那为什么小球 X 方向反弹一次就会慢慢停下来？？
这是因为 Y 方向碰到边界也会使 speedX 衰减（注意看代码）。也就是说 横向的碰撞只影响 speedX，纵向的碰撞即改变横向也改变纵向。你可以这样做试一试：
``` js
nowY += speedY;
if (nowY > maxT) {
	speedY *= -0.8;
	//speedX *= 0.8
	nowY = maxT;
} else if (nowY < 0) {
	speedY *= -1;
	//speedX *= 0.8;
	nowY = 0;
}
```
小球 X 方向需要碰撞很多次才能停下来。

8、最后判断 speedX 和 speedY 的绝对值如果小于 1 。这个该变量完全可以忽略了没就让小球停下来。
``` js
if (speedX == 0 && speedY == 0 && nowY == maxT) {
	clearInterval(timer);
}
```

基本的原理都讲完了，里面还有一些细节需要注意，比如说：何时清除定时器、作用域等问题。都是很基础的知识，就不说了。

终于讲完了，，里面也有许多不完美的地方，以后有时间在改吧！！

> 文章中如果有错误欢迎指出，非常乐意和大家一起交流。
