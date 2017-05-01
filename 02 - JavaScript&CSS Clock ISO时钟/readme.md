> 给自己提出一个挑战。用纯 JavaScript 来写东西，不借助框架和库，也不使用编译器和引用。
> 每个小程序都会给出详细的教程，保证每个新手都能看懂（不出意外的话每天都会更新） <br>
>
> **项目地址：** [cwyaml](https://github.com/cwyaml)/**[JavaScript-Programs](https://github.com/cwyaml/JavaScript-Programs)**  <br>
> **教程目录：** [JavaScript 每天一个小程序](http://www.jianshu.com/p/81d379ba9003) <br>
>
> 如果喜欢的话 请给个   ★star  非常感谢！！

第二天，我们来模仿一个 ISO 上的时钟：**ISO Clock**

### 实现效果：

![ISO Clock](http://upload-images.jianshu.io/upload_images/4030390-55554df35c711242.gif?imageMogr2/auto-orient/strip)

## 项目分析
1、首先时钟嘛，肯定要获取本地客户端的时间；

2、时钟有 3 个指针，我们可以通过添加动画的方式让它们围绕中心点转动；

3、通过获取到的 hour、minute 和 second 值分别计算 时针、分针和秒针的角度值；

## HTML&CSS
### 布局
``` html
<div class="box">
  <article class="clock">
    <div class="hours-container">
      <div class="hours"></div>
    </div>
    <div class="minutes-container">
      <div class="minutes"></div>
    </div>
    <div class="seconds-container">
      <div class="seconds"></div>
    </div>
  </article>
</div>
```
1、`.box` 是为了布局的方便；

2、 然后每个指针都需要一个 `*-container` 容器 。

### 添加 CSS 样式
把背景加载进来，然后放在页面中合适的位置上。
``` css
html{
  font-size: 10px;
}
html,body{
  margin: 0;
  padding: 0;
}
.box{
 width: 35rem;
 height: 38rem;      
 background: rgb(205,205,205);
 border-radius: 1rem;
 margin: 5% auto;
 display: flex;
 justify-content: center;
 align-items: center;
}
.clock{
  width: 30rem;
  height: 30rem;
  background: #fff url(ios_clock.svg) no-repeat center;
  background-size: 88%;
  border-radius: 50%;
  position: relative;
}
```

![](http://upload-images.jianshu.io/upload_images/4030390-b39e18ef782f86c8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


1、 `width: 35rem; height: 38rem;` 这个比例比较顺眼吧；

2、 `.box` 使用 **Flex** 布局方式，并且使其中的 `.clock` 水中、垂直方向都居中。看过第一天教程应该都明白 Flex 布局的。

3、Clock 的背景使用一张图片。[获取地址](https://github.com/cwyaml/JavaScript30/blob/master/02%20-%20JavaScript%26CSS%20Clock/ios_clock.svg)

### 添加中心轴
使用 CSS3 中的 **伪元素** 为时钟添加实心小圆点，指针都围着这个点转。
``` css
.clock:after{
  content: '';
  width: 1.5rem;
  height: 1.5rem;
  background: #000;
  border-radius: 50%;
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%,-50%);   /* 向左上移动自身的50% */
  z-index: 10;
}
```

![](http://upload-images.jianshu.io/upload_images/4030390-f1b50462e7ff4cc4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

1、 这句 `content: '';` 是必须的，不然这个伪元素不会显示，即使指定了宽度和高度。

2、 由于相对定位是从元素的左上角开始计算的，所以 `top: 50%; left: 50%;` 不能使这个小圆点在 Clock 的中心，使用 `transform: translate(-50%,-50%);` 向左上方移动自身宽度或高度的 50%

3、 `z-index: 10;` 是为了使这个小圆点在视图的最上层，遮挡住指针交叉的地方

### 指针容器
``` css
.hours-container,.minutes-container,.seconds-container{
  position: absolute;
  top: 0;
  right: 0;
  bottom: 0;
  left: 0;
}
```

1、容器被放置在 Clock 的上方，但是并没有样式，接下来就可以创建指针了！

### 添加指针
```css
.hours {
  width: 3%;
  height: 20%;
  background: #000;
  transform-origin: 50% 100%;
  position: absolute;
  top: 30%;
  left: 48.5%;
}
.minutes {
  width: 2%;
  height: 37%;
  background: #000;
  transform-origin: 50% 100%;
  position: absolute;
  top: 13%;
  left: 49%;
}
.seconds {
  width: 1%;
  height: 40%;
  background: #f00;
  transform-origin: 50% 90%;
  position: absolute;
  top: 20%;
  left: 49.5%;
  z-index: 8;
}
```

![](http://upload-images.jianshu.io/upload_images/4030390-9f4642b8e6fc4796.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

1、分别添加时针、分针和秒针。

2、 使用 `%` 这种单位可以更好地适应不同的屏幕。

3、`transform-origin: 50% 90%;` 规定指针旋转的位置为：X 方向的中心线 和 Y 方向的 90% 处这条线的交叉点。（具体看图吧）

![](http://upload-images.jianshu.io/upload_images/4030390-84c8bd66e0225062.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

4、 这里在定位的时候把自身的宽度计算在内了，所以就不必在往左上角移动了。

## 动画
目前为止，这个 Clock 还是没有功能的，我们来让它动起来。
### 定义动画规则
``` css
@keyframes rotate {
  100% {
    transform: rotateZ(360deg);
  }
}
```
1、这里用 `@keyframes` 规则定义了一个动画，这个动画的名称是 `ratate` ,应用这个动画的元素会沿着某个 Z 轴（也就是上面规定好的哪个交叉点）旋转 360 度。

### 定时功能
规定每个指针旋转 360 度需要多长时间。
``` css
.hours-container {
  animation: rotate 60s infinite linear;
}
.minutes-container {
  animation: rotate 30s infinite linear;
}
.seconds-container {
  animation: rotate 10s infinite linear;
}
```

![](http://upload-images.jianshu.io/upload_images/4030390-7fdb20c34a8145e8.gif?imageMogr2/auto-orient/strip)

> 为了演示方便，这里固定的时间并没有按照真实的 Clock 来设置。时针应该是 12 小时（43200s）、分针应该是 3600s 、秒针应该是 60s 。

### 更像真实的 Clock
现实中的 Clock 大部分是秒针和分针都是会跳动的，并且伴随着滴答声，我们来尝试一下。
``` css
.hours-container {
  animation: rotate 60s infinite linear;
}
.minutes-container {
  animation: rotate 3600s infinite steps(60);
}
.seconds-container {
  animation: rotate 60s infinite steps(60);
}
```

![](http://upload-images.jianshu.io/upload_images/4030390-8bd39c46e6de648f.gif?imageMogr2/auto-orient/strip)

1、只需要将 分针 和 秒针的旋转方式调整为 `steps()` 即可。

但是这样的 Clock 每次刷新都是从 0 开始的，并不是我们需要的，怎么做一个显示真实时间的呢？？

## 正确的时间
我们首先要获取到当前的时间，然后计算每个指针应该旋转的角度即可。

#### 获取每个指针
``` js
const hourHand = document.querySelector('.hours-container');
const minuteHand = document.querySelector('.minutes-container');
const secondHand = document.querySelector('.seconds-container');
```
#### 获取当前时间
``` js
const now = new Date();
const hour = now.getHours();
const minute = now.getMinutes();
const second = now.getSeconds();
```

#### 计算每个指针应旋转的角度
在 CSS3 中角度单位一共有四种：
- `deg` (度，一个圆 360 度)、
- `grad`（梯度，一个圆共400梯度）、
- `turn` （转、圈，一个圆共1圈）、
- `rad` （弧度，一个圆共2π弧度）

它们的对应关系为：
`90deg = 100grad = 0.25turn ≈ 1.570796326794897rad`

很显然，我们这里要用到的单位是 `deg` 。
``` js
const secondDegree = second * 6 + 90;
const minuteDegree = minute * 6 + (second / 10) + 90;
const hourDegree = (hour * 30) + (minute / 2) + 90;
```
1、`+90` 是因为角度的起始位置为水平的 X 轴，为了和 Clock 指针起始位置（Y 轴）做统一；

2、秒针的计算最简单，`(second / 60) * 360 + 90;`

3、 分针要考虑秒针的影响，如过了30秒，相当于半分钟。公式为： `当前分钟数 + 秒数在分钟的映射`；即：`(( minutes/ 60) * 360) + ((seconds / 60) * 6) + 90;`

4、 时针稍微复杂一点，因为要考虑分钟的影响，如过了30分钟，相当于半小时。公式为： `当前时数 + 分钟在小时的映射` 。即：`(( hours/ 12) * 360) + ((minutes / 60) * 30) + 90;`

### 应用角度值
``` js
hourHand.style.transform = `rotateZ(${hourDegree}deg)`;
minuteHand.style.transform = `rotateZ(${minuteDegree}deg)`;
secondHand.style.transform = `rotateZ(${secondDegree}deg)`;
```
为了使页面能实时的更新，我们要把上面的这些东西封装为一个函数，然后用定时器每秒执行一次。

整个时钟的功能都完成了！

> 文章中如果有错误欢迎指出，非常乐意和大家一起交流。
