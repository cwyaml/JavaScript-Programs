> 给自己提出一个挑战。用纯 JavaScript 来写东西，不借助框架和库，也不使用编译器和引用。
> 每个小程序都会给出详细的教程，保证每个新手都能看懂（不出意外的话每天都会更新） <br>
>
> **项目地址：** [cwyaml](https://github.com/cwyaml)/**[JavaScript-Programs](https://github.com/cwyaml/JavaScript-Programs)**  <br>
> **教程目录：** [JavaScript 每天一个小程序](http://www.jianshu.com/p/81d379ba9003) <br>
>
> 如果喜欢的话 请给个   ★star  非常感谢！！

第三天，来做一个有关文字的特效吧！不知道该取什么合适的名字，索性就叫做 **typewriter** (打字机) 吧！

## 实现效果：

![typewriter](http://upload-images.jianshu.io/upload_images/4030390-9747e6753c08577f.gif?imageMogr2/auto-orient/strip)

## 思路
1、把要显示的文字放在一个容器中，做好样式并放在合适的位置；
2、提取这个容器中的文本内容；
3、把获取到的文本拆分成一个个字符串，逐个显示。

原理貌似很简单，来实现一下看会遇到什么问题。

## HTML&CSS
### 准备要显示的文字
随便找了几句话比较喜欢的话。就这样放在页面中了：
``` html
1. Whatever is worth doing is worth doing well.<br />  任何值得做的，就把它做好。
<br /><br />
2. Happiness is a way station between too much and too little.<br />  幸福是太多和太少之间的一站。
<br /><br />
3. In love folly is always sweet.<br />  恋爱中，干傻事总是让人感到十分美妙。
<br /><br />
4. Your happy passer-by all knows, my distressed there is no place hides.<br />  你的幸福路人皆知，我的狼狈无处遁形。
<br /><br />
5. You may be out of my sight, but never out of my mind.<br />  你也许已走出我的视线，但从未走出我的思念。
```
加些简单的样式：
``` css
.typewriter{
  width: 50rem;
  padding: 1.5rem 3rem;
  border: 1px solid #ccc;
  line-height: 1.5;
  font-size: 1.6rem;
  color: #333;
  margin: 5% auto;
}
```

整个文本外有一个边框，放在了页面的中心位置。

![](http://upload-images.jianshu.io/upload_images/4030390-5c57a710b99300ab.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 需要一个光标
通过 **伪元素** 来实现这个光标。
``` css
@keyframes flash {
  0%{
    visibility: visible;
  }
  100%{
    visibility: hidden;
  }
}
.typewriter:after{
  content: '_';
  animation: flash .6s steps(2,start) infinite;
}
.typewriter.gameover:after {
    display: none;
}
```

1、光标就长这个样子 `content:'_';` 吧！

2、使用 `@keyframes` 关键字定义一个动画，效果为从 **显示** 状态 过度到 **隐藏** 状态。

3、 `animation` 属性为光标应用一个动画。动画的名称为 `flash`、这个动画要在 **0.6s** 完成、动画过渡类型 和 执行次数（infinite为无限循环）

4、`.typewriter.gameover:after{}` 当文字全部显示完毕后，将光标隐藏。

## JS部分

### 获取文本内容
获取整个 `typewriter` 容器中的文本，将其保存在变量 `code` 中。
``` js
var typewriter = document.querySelector('.typewriter');
var code = typewriter.innerHTML;
```
既然文本已经保存了起来，那么页面中的文本就可以隐藏（或者删除）了。因为我们最后的效果是 **从无到有**。
``` js
typewriter.innerHTML = '';
```

这时你会发现一个问题，我们保存在 `code` 中的内容是这样的：

![code内容弹窗](http://upload-images.jianshu.io/upload_images/4030390-a6b1ec5173a17541.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

里面包含了 HTML 字符，而这些 HTML 字符是不需要显示的，所以打印的时候要对字符进行判断，跳过 `<...>` 和 `&...;` 这一部分。

我们用一个函数来实现这个过程：
``` js
var i = 0;
function typewriting(){
  i++;
  if(i < code.length){
    switch (code.charAt(i)){
      case '<':
      i = code.indexOf('>',i);
      break;
      case '&':
      i = indexOf(';',i);
      break;
  }
  typewriter.innerHTML = code.substring(0,i);
    setTimeout(typewriting,100);
  }
  else{
    typewriter.classList.add('gameover');
  }
}
```
1、三个操作字符串的方法：
`charAt()` 方法可返回指定位置的字符
`indexOf()` 方法返回某个指定字符在字符串中首次出现的位置。
`substring()` 方法用于提取字符串中介于两个指定下标之间的字符。

2、 `typewriter.innerHTML = code.substring(0,i);` 将 `code` 中保存的字符的 0 ~ i 打印出来。

> **注：** 每次执行循环其实都是从 0 ~ i 打印一遍，只不过从 0 ~ i-1 已经存在，再视觉上就被认为只打印了第 i 个字符

3、`switch` 语句判断第 **i** 个字符是否为 `<` 或 `&`？如果是其中之一，则从这个字符开始往后寻找 `>` 或 `;`，找到后将其 **i** 值赋给 **i** （这时的 code[i] 已经是 `<br>` 或 ` ` 之后的那个字符）。也就是打印跳过了 `<br>` 或 ` ` 。

> 感觉自己说不明白了，还是举个例子吧！
> 假如 `code = 'ab<br>cd';`
> 当第一次循环，打印出 `a`、第二次循环打印出 `ab` 、第三次循环打印出 `ab<br>c`（<br>虽然被打印出来了，但是不会显示的，所以你只看到打印出来了 c） 、第四次循环打印出 `ab<br>cd`。

4、 `setTimeout()` 定时器，每隔 100ms 执行一次这个循环，也就是控制打印的速度。

5、 当 `i > code.length` 时，给光标添加 `.gameover` 样式，将其隐藏。

到此这个效果全部实现！！

> 文章中如果有错误欢迎指出，非常乐意和大家一起交流。
