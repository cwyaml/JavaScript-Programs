> 给自己提出一个挑战。用纯 JavaScript 来写东西，不借助框架和库，也不使用编译器和引用。
> 每个小程序都会给出详细的教程，保证每个新手都能看懂（不出意外的话每天都会更新） <br>
>
> **项目地址：** [cwyaml](https://github.com/cwyaml)/**[JavaScript-Programs](https://github.com/cwyaml/JavaScript-Programs)**  <br>
> **教程目录：** [JavaScript 每天一个小程序](http://www.jianshu.com/p/81d379ba9003) <br>
>
> 如果喜欢的话 请给个   ★star  非常感谢！！

![](http://upload-images.jianshu.io/upload_images/4030390-971d23653c1b331b.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

第七天、讲一下 **弹幕** 的实现原理。

**说明：** 弹幕分两种：

1、像优酷、爱奇艺等，记录用户发送弹幕时该视频播放的位置，其他人播放这个视频时到了这个点就显示弹幕。

2、像斗鱼、熊猫这样的直播网站，用户发送弹幕直接显示在屏幕上，以后不需要再显示了。

我们就来做第二种！！！

### 实现效果

![](http://upload-images.jianshu.io/upload_images/4030390-347805d0b35dcafe.gif?imageMogr2/auto-orient/strip)

### 项目分析
1、获取用户输入信息；

2、在页面中创建一个 `<span></span>` 来放获取到的文本，并添加一些样式（字体大小、颜色等）；

3、给这个 `span` 添加一个从右向左移动的动画；

4、动画结束后，移除这个 `<span>`。

思路很清楚了，就来动手实现一下。。

### 布局
页面中要有一个输入框让用户输入信息，还要一个盒子用来显示弹幕。为了美观，我多加了一些东西。
``` html
<div class="box" id="box">
  <div id="dm"></div>
  <div class="idDom" id="idDom">
    <div id="content">
      <p class="title">吐槽:</p>
      <input type="text" class="text" id="text" placeholder="请输入你想说的话~~"/>
      <button type="button" class="btn" id="btn">发射!</button>
    </div>
  </div>
</div>
```
1、`#dm` 是显示弹幕的区域，`input` 供用户输入信息（其中 `placeholder` 属性规定输入框中默认显示内容）。

2、其它元素都是为了美观和布局。

### 样式
跟以前一样，先给出代码然后再讲解。
``` css
html,body{
  margin: 0;
  padding: 0;
  font-size: 10px;
  overflow: hidden;
}
#box{
  width: 100%;
  height: 100%;
}
#dm{
  width: 100%;
  height: 90vh;
  background: #E8F1F5;
}
#dm span{
  width: auto;
  height: 3rem;
  font-size: 2rem;
  line-height: 2rem;
  position: absolute;
  white-space: nowrap;
}
#idDom{
  width: 100%;
  height: 10vh;
  background: #666;
  position: absolute;
  bottom: 0;
  display: flex;
  align-items: center;
  justify-content: center;
}
#content{
  width: 50rem;
  height: 10vh;
  display: flex;
  align-items: center;
  justify-content: center;
}
.title{
  font-size: 2.2rem;
  color: #fff;
  line-height: #ccc
}
.text{
  width: 30rem;
  height: 2.5rem;
  border: none;
  border-radius: .5rem;
  font-size: 1.4rem;
  padding: 0 1rem;
  margin: 0 .5rem;    /*三个元素的间距*/
}
.btn{
  width: 6rem;
  height: 3rem;
  border: none;
  background: #f00;
  color: #fff;
}
```

1、首先还是格式化浏览器默认的 `margin` 和 `padding`。`overflow` 属性设置超出屏幕的部分隐藏，这样就不会出现下拉和水平的滚动条了。

2、`#dm` 用来显示弹幕，我们把它的高度设置为 `90vh`。`vh` 是 CSS3 中新增的长度单位，表示相对于视口的高度。视口高度被均分为100单位的vh，90vh 就表示当前浏览器可视区域高度的 90%。（vw 就是视口宽度）
>感觉 CSS3 中新增的内容使用起来很顺手，很有必要了解一下

3、`#dm span` 是每条弹幕的样式。你可以修改为你喜欢的样子，不过一定要设置 `position` 定位属性。

4、然后就是下面的输入部分了。使用了 **flex布局** （也是CSS3 中新增的内容，太方便了）。只需要 `align-items:center;justify-content:center;` 这两句就可以使其中的子元素在水平和垂直方向居中。所以 `#idDom` 和 `#content` 都使用了。

5、然后就是 输入框、按钮的样式了，没什么好说的，应该都能看懂。

### JS部分
先看看代码吧。。
``` js
var btn = document.getElementById('btn');
btn.onclick = function(){
  addBarrage();
}
document.onkeydown = function(evt){
  var event = evt || window.event;
  if(event.keyCode == 13){
    addBarrage();
  }
}
var colors = ['#2C3E50','#FF0000','#1E87F0','#7AC84B','#FF7F00','#9B39F4','#FF69B4'];
function addBarrage(){
  clearInterval(timer);
  var text = document.getElementById('text').value;  //获取用户输入的值
  document.getElementById('text').value = '';
  //parseInt(Math.random()*(n-m+1)+m);//生成一个从 m - n 之间的随机整数
  var index = parseInt(Math.random() * 7);  //生成一个0~6的随机数
  var screenW = window.innerWidth;
  var screenH = dm.offsetHeight;   
  var max = Math.floor(screenH / 40);
  var height = 10 + 40 * (parseInt(Math.random() * (max + 1)) - 1);
  var span = document.createElement('span');
  span.style.left = screenW +'px';
  span.style.top = height +'px';
  span.style.color = colors[index];
  span.innerHTML = text;
  var dmDom = document.getElementById('dm');
  dmDom.appendChild(span);
  timer = setInterval(move,10);
}
function move(){
  var arr= [];
  var oSpan = document.getElementsByTagName('span');
  for(var i = 0;i < oSpan.length;i++){
    arr.push(oSpan[i].offsetLeft);
    arr[i] -= 2
    oSpan[i].style.left = arr[i]+'px';
    if (arr[i] < -oSpan[i].offsetWidth) {
      var dmDom = document.getElementById('dm');
      dmDom.removeChild(dmDom.childNodes[0]);
    }
  }
}
```

1、说明：向页面添加弹幕使用 `addBarrage()` 函数，添加动画用 `move()` 函数。（把一种功能封装为一个函数是一个好习惯）

2、首先思考一下弹幕的触发事件。应该有两个：点击“发射”按钮、按下 `Enter` 按键。所以分别监听点击 和 键盘事件。
``` js
var btn = document.getElementById('btn');
btn.onclick = function(){
  addBarrage();
}
document.onkeydown = function(evt){
  var event = evt || window.event;  //兼容IE
  if(event.keyCode == 13){
    addBarrage();
  }
}
```

3、然后就要思考怎么向页面添加弹幕了？

- 先得到用户输入的信息
``` js
var text = document.getElementById('text').value;
```
- 然后在页面中创建一个 `<span></span>` ，把得到的文本放进去
``` js
var span = document.createElement('span');
span.innerHTML = text;
```
- 添加到 `#dm` 这个盒子中：
``` js
var dmDom = document.getElementById('dm');
dmDom.appendChild(span);
```
这样就可以了吗？？ 当然不是的。。

4、每条弹幕应该有不同的颜色，这样才炫酷。思路就是把预先的颜色放进一个数组，使用的时候用随机的下标，这样就获得了随机颜色。（借鉴斗鱼的 7 中颜色）
``` js
var colors = ['#2C3E50','#FF0000','#1E87F0','#7AC84B','#FF7F00','#9B39F4','#FF69B4'];
var index = parseInt(Math.random() * 7);  //生成一个0~6的随机数
span.style.color = colors[index];
```
这样每条弹幕就有不同的颜色了。
> 生成一个从 m ~ n 之间的随机整数的公式
> `parseInt(Math.random()*(n-m+1)+m);`

5、每条弹幕在页面上还要有不同的位置（高度），也就是不同的 `top` 值。我的想法是，虽然要有不同的 top ，但也不能太随意。就像下面这样：（一行是一行的）

![](http://upload-images.jianshu.io/upload_images/4030390-8c026a6e072e8b38.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

我的思路是：先判断页面可以放多少行？
``` js
var screenH = dm.offsetHeight;
var max = Math.floor(screenH / 40);
```
然后计算可以有的 top 值：（加 10 是为了不至于紧挨着屏幕顶部，最后的 -1 是为了不至于太靠下 ）
``` js
var height = 10 + 40 * (parseInt(Math.random() * (max + 1)) - 1);
```
你可以自己理解一下我的这种计算方式。。

然后应用给 `<span>` 即可：
``` js
span.style.top = height +'px';
```

6、对了，弹幕应该添加到页面的什么位置呢？因为弹幕要从右往左移动，所以应该添加到屏幕的右侧，left 值为浏览器页面的宽度。**这时候就知道为什么前面要设置 body 的 `overflow: hidden;` 了吧！！**
``` js
var screenW = window.innerWidth;
span.style.left = screenW +'px';
```

### 动画
弹幕（也就是`<span>`）被添加到页面中了，我们要让他动起来。

思路就是写一个函数，减少 `<span>` 的 left 值。每隔几毫秒执行一次这个函数，我们看起来这个 `<span>` 元素就动起来了。

7、但是有几个问题：
- 页面中有多少个弹幕（<span>）？
因为页面中只有 弹幕 使用的是 `<span>` 标签，所以这样就可以获取所有 `<span>` 的 **nodeList**（类似数组但不是数组，可以使用下标索引访问）：
``` js
var oSpan = document.getElementsByTagName('span');
```
- 怎样记录每条弹幕的 left 值？
获取到所有的 `<span>` 后，用一个 for 循环将每一个 `<span>` 的 left 值放进一个数组：
``` js
var arr = [];
for(var i = 0;i < oSpan.length;i++){
  arr.push(oSpan[i].offsetLeft);
}
```
这样 arr[] 就保存了所有弹幕的 left 值。

8、接下来我们就逐个减少每条弹幕的 left 值：
``` js
arr[i] -= 2
oSpan[i].style.left = arr[i]+'px';
```

9、最后判断如果弹幕已经移出了页面的左边，就把这条弹幕删除了吧。
``` js
if (arr[i] < -oSpan[i].offsetWidth) {
  var dmDom = document.getElementById('dm');
  dmDom.removeChild(dmDom.childNodes[0]);
}
```

我们可以在开发工具中看一下这个过程：

![](http://upload-images.jianshu.io/upload_images/4030390-19288d3b9544469d.gif?imageMogr2/auto-orient/strip)

弹幕的 left 一直减小，移出页面后 `#dm` 中就没有这个 `<span>` 了。

10、最后处理一个小细节吧！当我们点击按钮或按回车后，输入框中的文字会保留，影响我们下次输入，所以只要获取到了用户输入的内容，就把输入框清空吧！
``` js
document.getElementById('text').value = '';
```

到此，弹幕就实现了。。。

> 文章中如果有错误欢迎指出，非常乐意和大家一起交流。
