> 给自己提出一个挑战。用纯 JavaScript 来写东西，不借助框架和库，也不使用编译器和引用。
> 每个小程序都会给出详细的教程，保证每个新手都能看懂（不出意外的话每天都会更新） <br>
>
> **项目地址：** [cwyaml](https://github.com/cwyaml)/**[JavaScript-Programs](https://github.com/cwyaml/JavaScript-Programs)**  <br>
> **教程目录：** [JavaScript 每天一个小程序](http://www.jianshu.com/p/81d379ba9003) <br>
>
> 如果喜欢的话 请给个   ★star  非常感谢！！

![与主题不符的封面](http://upload-images.jianshu.io/upload_images/4030390-3e77dbce36114c18.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

第八天了。做点什么呢？？

CSS3 中出现了很多非常实用的新特性，今天就带着大家做一个简单的 **图片修改器**（就这么叫吧），顺便了解几个新的知识。

### 效果预览

![](http://upload-images.jianshu.io/upload_images/4030390-23017d307bf980e5.gif?imageMogr2/auto-orient/strip)

> **说明:** 本篇文章主要讲一个重要的知识：**CSS变量** 。（原生支持的，而不是SASS、LESS等预处理器）

### 布局
``` html
<div id="title">Update CSS Variables with JS</div>
<div class="controls">
  <label for="border" class="label">Border:</label>
  <input type="range" id="border" name="spacing">

  <label for="radius" class="label">Radius:</label>
  <input type="range" id="radius" name="radius">

  <label for="blur" class="label">Blur:</label>
  <input type="range" id="blur" name="blur">

  <label for="colors" class="label">Border Color:</label>
  <input type="color" id="colors" name="baseColor">
</div>
<div id="box">
  ![](bg.jpg)
</div>
```
1、首先一个 `#title`来放标题；

2、然后是我们修改图片的按钮之类的，都放在 `.controls` 里面；

3、只做四个选项：调整图片的边框（Border）、图片边框圆角（Radius）、模糊（Blur） 和 图片边框颜色（colors）；

4、`#box` 来放图片。

### 样式
``` css
html,body{
  margin: 0;
  padding: 0;
  background: #193549;
  color: #fff;
  font-size: 10px;
}
#title{
  width: 100%;
  height: 15vh;
  font-size: 4rem;
  display: flex;
  justify-content: center;
  align-items: center;
}
.controls{
  width: 70%;
  height: 10vh;
  margin: 0 auto;
  display: flex;
  justify-content: space-around;
  align-items: center;
}
.label{
  font-size: 2rem;
}
input{
  width: 100px;
}
#box{
  width: 100%;
  height: 75vh;
  display: flex;
  justify-content: center;
  align-items: center;
}
```
1、你没有看错，我又又用了 **Flex布局** 。（这个方法有毒，用了就停不下来。。）

>如果你还不会这种方式，赶紧去看看我的这篇教程吧！！
> [Flex布局教程 - 语法](http://www.jianshu.com/p/5405f9bf9996)

2、别的都很简单，就不说了。。

### 接下来是我们今天的重点知识：
我们来为图片设置样式：
``` css
:root{
  --baseColor: #000;
  --blur: 2px;
  --spacing: 5px;
  --radius: 5px;
}
img{
  border: var(--spacing) solid var(--baseColor);
  border-radius: var(--radius);
  filter: blur(var(--blur));    /*滤镜*/
}
```
1、`:root` 表示文档树的根元素。在这里即代表 `html`。这么说吧，`:root` 就是一个 `<html>` 专属的 CSS 选择器（应该懂了吧！）。 [参考文档](https://developer.mozilla.org/zh-CN/docs/Web/CSS/:root)

2、`--XX` 这个就是 **CSS变量** 定义的方式。 `--baseColor` 是一个变量，`--blur` 也是一个变量。写在 `:root` 中就表示我们定义的这四个是全局变量。 [参考文档](https://developer.mozilla.org/zh-CN/docs/Web/CSS/Using_CSS_variables)

3、怎样使用刚才定义好的 CSS变量呢？？ 你只需要这样 `var(--XX)`就可以给了。就像上面的代码那样。

4、`filter: blur();` 是一个滤镜工具，功能非常强大。可以通过使用滤镜达到不同的视觉效果，我们这里使用了 `blur()` 模糊功能。常见的滤镜还有 透明度滤镜opacity()、灰度滤镜grayscale() 等。你可以尝试着给你的页面添加其他功能。 [参考文档](https://developer.mozilla.org/en/docs/Web/CSS/filter?v=example)

> 这样，我们只需要改变 CSS变量 图片的相应参数就会改变。

### JS部分
``` js
const inputs = document.querySelectorAll('input');
inputs.forEach(input => input.addEventListener('mousemove',updateData));
inputs.forEach(input => input.addEventListener('change',updateData));
function updateData(){
  const data = this.dataset.sizing || '';
  document.documentElement.style.setProperty(`--${this.name}`,this.value+data);
}
```
1、`inputs` 返回一个nodelist，是页面中所有的 `input` 节点。

2、使用 `forEach()` 为每个节点设置 `mousemove` 事件。可以在该滑块的值变化时实时的改变CSS全局变量。

3、之所以又监听元素 `change` 事件，是因为 `baseColor`，当改变了颜色的值时，只监听 mousemove 事件，无法实时的改变颜色，直到鼠标从color颜色块上滑过，添加 `change` 事件就可以当改变过后实时的改变。。

4、`this.dataset.sizing` 返回 `data-sizing` 属性的值。`dataset` 可以返回该元素所有自定义的 `data-*` 属性（前面的文章中我们已经使用过这个属性了）。我们用 `data-sizing` 来存放该对象的值的单位。举个例子：
``` html
<label for="border" class="label">Border:</label>
<input type="range" id="border" name="spacing" data-sizing="px">
```
border 属性的单位是 `px`，我们就把它的 `data-sizing` 属性值设置为 `px`。

5、为什么要设置单位呢？？因为通过 `this.value` 获取的值没有单位，不能直接设置为 CSS属性。而我们又不能这样写：
``` js
document.documentElement.style.setProperty(`--${this.name}`,this.value+'px');
```
因为 border 、radius 和 blur 的值都有单位，而 baseColor 并没有单位。。所以这个 `data-sizing` 属性是必须设置的。

6、还要注意一点：`--${this.name}` 外面包裹的是 **ESC** 键下面的那个键打出来的 符号，而不是单引号。。。

到这里就完了吗？？哈哈哈，当然不是。

我们还要动手设置一下这些值改变的范围，比如说：我们要让图片的边框在一定的范围内改变，而不是无限变大，那样就会影响你的布局，用户体验就非常不好。。怎么设置呢？？

新的 HTML 代码：
``` html
<div class="controls">
  <label for="border" class="label">Border:</label>
  <input type="range" id="border" name="spacing" min="1" max="30" value="5" data-sizing="px">

  <label for="radius" class="label">Radius:</label>
  <input type="range" id="radius" name="radius" min="0" max="50" value="5" data-sizing="px" data-sizing="px">

  <label for="blur" class="label">Blur:</label>
  <input type="range" id="blur" name="blur" min="0" max="10" value="2" data-sizing="px">

  <label for="colors" class="label">Border Color:</label>
  <input type="color" id="colors" name="baseColor" value="#000">
</div>
```
1、`min` 为可取的最小值，`max` 为可取的最大值，`value` 为默认值。

2、也把 `data-sizing` 这个表示单位的属性写上去了。

> 文章中如果有错误欢迎指出，非常乐意和大家一起交流。
