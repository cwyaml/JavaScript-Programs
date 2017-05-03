> 给自己提出一个挑战。用纯 JavaScript 来写东西，不借助框架和库，也不使用编译器和引用。
> 每个小程序都会给出详细的教程，保证每个新手都能看懂（不出意外的话每天都会更新） <br>
>
> **项目地址：** [cwyaml](https://github.com/cwyaml)/**[JavaScript-Programs](https://github.com/cwyaml/JavaScript-Programs)**  <br>
> **教程目录：** [JavaScript 每天一个小程序](http://www.jianshu.com/p/81d379ba9003) <br>
>
> 如果喜欢的话 请给个   ★star  非常感谢！！

![](http://upload-images.jianshu.io/upload_images/4030390-e2319aac51b540df.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

第六天，做点什么呢？？想必大家都看过 **黑客帝国** 系列电影吧！先放一张图片致敬一下经典。

我们就来做一下里面的背景特效吧。。。

### 效果预览

![](http://upload-images.jianshu.io/upload_images/4030390-d91d804e7e25ba91.gif?imageMogr2/auto-orient/strip)

是不是很赞！！一起来实现一下吧！！

### 项目分析
1、用到 HTML5 中的 canvas 知识；

2、使用 js 生成随机的字符；

3、把整个页面当做一个 canvas 画布，把字符画上去；

4、重复这个动作：生成字符、画到页面上。需要考虑的是每次字符在页面的位置。

### HTML&CSS
页面中只有一个 canvas 画布，所以不需要什么布局。
``` html
<!-- html -->
<canvas id="content" width="1366px" height="768px"></canvas>
<!-- css -->
*{
  margin: 0;
  padding: 0;
}
```

1、因为 canvas 尺寸的改变会使里面的内容重新绘制，所以为了不影响最后的效果，最好将这个值设置为本机的屏幕分辨率。（至于怎么影响效果后面会讲）

2、取消浏览器默认的 `margin` 和 `padding`。

### JS部分
同样先上代码：
``` js
var cav = document.getElementById('content');
var w = window.screen.width;
var h = window.screen.height;
var yPositions = Array300).join(0).split('');   
var ctx = cav.getContext('2d');  //初始化画布
var draw = function(){
  ctx.fillStyle = 'rgba(0,0,0,.05)';
  ctx.fillRect(0,0,w,h);
  ctx.fillStyle = 'green';
  ctx.font = '20px';
  yPositions.map(function(y,index){  
    text = String.fromCharCode(1e2 + Math.random() * 330);  //生成随机字母
    x = index * 10;
    cav.getContext('2d').fillText(text,x,y);
    if (y > Math.random() * 1e4) {
        yPositions[index] = 0;
    } else {
        yPositions[index] = y + 10;
    }
  });
}
setInterval('draw()',30);
```
1、首先获取画布；

2、获取本机显示器的分辨率：
``` js
var w = window.screen.width;
var h = window.screen.height;
```

3、然后要在页面中画出一个矩形（这个就是整个特效的范围）。前面说过，canvas 的尺寸会影响最后的效果，是因为，我们不能动态的改变 canvas 的尺寸（那样会使 canvas 重新绘制），所以当页面绘制好后我们再去调整浏览器的尺寸是就会产生白边，你可以去试一下。。。

所以解决办法就是直接把 canvas 的尺寸设置为屏幕的分辨率。这样不管怎么调整，浏览器的尺寸都不可能比屏幕还大吧。
``` js
ctx.fillStyle = 'rgba(0,0,0,.05)';
ctx.fillRect(0,0,w,h);
```

4、然后就应该考虑怎样来生成随机字符了。我们用了这样一段代码：
``` js
text = String.fromCharCode(1e2 + Math.random() * 330);
```
`fromCharCode(num)` 会返回一个 `Unicode` 编码为`num` 字符。所以这句话理论上可以写成 `fromCharCode(Math.random * n)` 。（只要 num 的范围在 `0000 - FFFF`）
> 对于 `Unicode` 编码，他为世界上所有的字符进行了编码。十六进制范围为从 `0000 - FFFF`，其中中文字段在 `4E00-9FA5`。

5、`map(callback())` 方法创建一个新数组，其结果是该数组中的每个元素调用一个提供的函数。callback 函数会被自动传入三个参数：数组元素，元素索引，原数组本身。所以没执行一次 `draw()` 函数会在 X 方向上生成一排的字符。

6、用 x 和 y 来指定字符在页面中的位置。x 指定没列字符占 10px 的宽度，y 同样每次增加 10px，为每个字符在 y 方向占 10px 高度。当然每列字符不可能数量都相同，所以只要 y 的值大于一个随机数之后新的字符就生成在页面顶部。
``` js
x = index * 10;
if (y > Math.random() * 1e4) {
  yPositions[index] = 0;
} else {
  yPositions[index] = y + 10;
}
```

7、忘记说了，再次之前要生成一个值全部为 0 的数组，主要控制字符的列数。
``` js
var yPositions = Array(300);
yPositions.fill(0);
//也可以这样写
//var yPositions = Array(300).join(0).split('');
```

完成。。。

> 文章中如果有错误欢迎指出，非常乐意和大家一起交流。
