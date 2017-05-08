> 给自己提出一个挑战。用纯 JavaScript 来写东西，不借助框架和库，也不使用编译器和引用。
> 每个小程序都会给出详细的教程，保证每个新手都能看懂（不出意外的话每天都会更新） <br>
>
> **项目地址：** [cwyaml](https://github.com/cwyaml)/**[JavaScript-Programs](https://github.com/cwyaml/JavaScript-Programs)**  <br>
> **教程目录：** [JavaScript 每天一个小程序](http://www.jianshu.com/p/81d379ba9003) <br>
>
> 如果喜欢的话 请给个   ★star  非常感谢！！

![](http://upload-images.jianshu.io/upload_images/4030390-7ece64c7ff57dea3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

第九天，正好我正在整理个人博客，就教大家怎么给博客添加音乐播放器。

### 效果展示
已经把这个功能添加到我的博客：http://www.cwyaml.top  点击logo看一下效果吧！！


有人就说了，添加个音乐播放器还不很容易吗？添加个 `<audio>` 标签不搞定了：
``` html
<audio controls="controls">
  <source src="#" type="audio/ogg">
  <source src="#" type="audio/mpeg">
  Please update your browser!!
</audio>
```
> <audio></audio> 之间的文字会在浏览器不支持该标签时显示出来

在网页中就会出现这个一个东西：

![](http://upload-images.jianshu.io/upload_images/4030390-9d57d9cefa47a87b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

也挺简洁的哈！！

但是，如果是这样的话，我还有更好的选择：

### 网易云音乐外链播放器
1、打开这个链接：[网易云音乐](http://music.163.com/)

2、搜一首你喜欢的歌，进入播放页面；

![](http://upload-images.jianshu.io/upload_images/4030390-acce7305041c129f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

3、已经看到了吧，专辑封面下面出现了“生成外链播放器”，我们点进去就会出现：

![](http://upload-images.jianshu.io/upload_images/4030390-326db2763eb5992b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

4、好吧（尴尬！），我们换一首赵雷的 **成都**

![2017-05-08_135957.png](http://upload-images.jianshu.io/upload_images/4030390-83b278391c697680.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

5、点击 **生成外链播放器** ，就会看到：

![](http://upload-images.jianshu.io/upload_images/4030390-64d6b68ee84cdcf0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

6、有 **播放器尺寸**、**是否自动播放** 等功能可以选择，方框中会出现预览样式。选好后，把下面的代码放在你的页面中就可以了。。

> 不仅单首歌可以生成外链播放器，歌单也可以，你可以自己尝试一下。（网易真良心企业）

这可比 `<audio>` 标签好看多了吧！但是很明显有几个缺点：

1、你已经看到了，并不是所有的音乐都可以用，有版权限制；

2、样式不能修改，可能与你页面的风格不符；

总之就是： 不能实现 **充分的可定制性** 。

自己动手丰衣足食，我们一起来写一个音乐播放器吧！！

### 实现功能
先说说做出来是什么效果吧。在页面中不出现播放器的样式，而是通过点击页面中某个元素触发，再次点击可以切换歌曲。就这些吧，具体做着再说。。

### 布局
其实并没有布局。页面只有一个按钮。
``` html
<div id="box">
  <button id="btn" type="button">点击播放音乐！</button>
</div>
```
### 样式
强行加个样式，要对得起 **强迫症** 这个称号，一个按钮也要做的漂亮一点。
``` css
*{
  margin: 0;
  padding: 0;
}
#box{
  width: 100%;
  height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
}
#btn {
  font-family: Arial;
  font-size: 17px;
  padding: 12px 35px;
  background-color: rgb(120, 146, 194);
  border: 1px solid rgb(78, 96, 150);
  border-radius: 16px;
  color: rgb(255, 255, 255);
}
#btn:hover{
  background: rgb(71,110,158);
}
```

### JS部分
思路就是，点击按钮后，创建一个 `<audio>` 标签添加到页面中。

1、要做音乐播放器，当然我们要有音乐才行。用一个数组来放音乐外链：
``` js
var songs = [
  "http://m2.music.126.net/3uHnH7uQAeFwUfuvEB9lrg==/3265549619475054.mp3",
  "http://m2.music.126.net/NnHwR2HV-1OoKZ6R5LVy4Q==/18502581673300023.mp3",
  "http://m2.music.126.net/qv3RI4K7ABKJ0TyAdb3taw==/3250156397064860.mp3",
  "......"
];
```

> 制作音乐外链的方法:
> 下载歌曲，然后上传到云端存储空间（我用的是 [七牛云存储](https://www.qiniu.com/)）, 就会自动生成外链。所以说你只要能下载到歌曲文件，就都能拿来当背景音乐，至于怎么下载，我就不说了。。

2、然后写一下怎么在页面创建 `<audio>` 标签吧。
``` js
function addAudio(){
  var e = document.getElementById('audio');
  e.setAttribute('index',0);
  e.id = 'audio';
  e.loop = false; //不循环
  e.innerHTML = 'Please update your browser!!';
  document.body.appendChild(e);
  }
}
```

> `index` 属性主要用来记录当前播放的是第几首歌、`loop` 属性不让歌曲循环播放、然后把这个 `audio` 添加到 body 中。

3、写一下按钮的 `onclick` 事件：
``` js
var btn = document.getElementById('btn');
btn.onclick = function() {
  addMusic();
}
```

现在点击按钮后 `audio` 标签就会被添加到页面中。但是歌曲并不会播放，因为它现在还是这个样子：
``` html
<audio index="0" id="audio" src="">Please update your browser!!</audio>
```

`src` 并没有值，所以肯定不会播放了。

4、下面就来给 `src` 属性赋值吧。因为有三首歌曲，我们还要判断一下添加哪个外链。
``` js
function cycle(){
  var e = document.getElementById('audio');
  if(e != null){
    var index = parseInt(e.getAttribute('index'));
    if(index > songs.length - 2){
      index = 0;
    }else{
      index++;
    }
    e.setAttribute('index',index);
  }
  e.src = i;
  e.play();
}
```

先获取到刚才添加的 `audio` 标签，如果获取到了，取得 `index` 值，即当前播放的是第几首歌（从 0 开始）。

**思考一下：** 第一次点击 index = 0、第二次 index = 1、第三次 index = 2，这时我们的三首歌都播放过了，点击第四下时 index = 3，应该停止播放的，所以 songs = [] 中要有一个占位的 **空元素** 。当点第五次时，歌曲列表要重新循环，所以让此时的 index = 0。

逻辑还是很简单的。。

### 细节处理
1、点我们第一次点击按钮时，向页面中添加了一个 `audio` 标签，第二次点击时，就不必再次添加了，只需要修改 `src` 值就可以了。所以上面的代码要修改：
``` js
function addAudio(){
  var e = document.getElementById('audio');
  if(e === null){
    e = document.createElement('audio');
    e.setAttribute('index',0);
    e.id = 'audio';
    e.loop = false; //不循环
    e.innerHTML = 'Please update your browser!!';
    document.body.appendChild(e);
    e.addEventListener('ended',function(){
      go();   //音频播放完毕时触发 ended 事件
    });
  }
}
```

添加前先判断一下 `audio` 是否存在。

2、最后做一下简单的封装：
``` js
function go() {
  var songs = [
    'http://onk2gpv5u.bkt.clouddn.com/%E5%B1%B1%E4%B8%98-%E6%9D%8E%E5%AE%97%E7%9B%9B.mp3',
    'http://onk2gpv5u.bkt.clouddn.com/%E6%88%91%E7%BB%88%E4%BA%8E%E5%A4%B1%E5%8E%BB%E4%BA%86%E4%BD%A0%20%28Live%29-%E6%9D%8E%E5%AE%97%E7%9B%9B.mp3',
    'http://onk2gpv5u.bkt.clouddn.com/%E7%8E%8B%E5%8A%9B%E5%AE%8F%20-%20%E9%9C%80%E8%A6%81%E4%BA%BA%E9%99%AA.mp3',
    ''
  ];
  function addAudio() {
    var e = document.getElementById('audio');
    if (e === null) {
      e = document.createElement('audio');
      e.setAttribute('index', 0);
      e.id = 'audio';
      e.loop = false;
      e.innerHTML = 'Please update your browser!!';
      document.body.appendChild(e);
      e.addEventListener('ended', function() {
        go()
      })
    }
  }
  function cycle() {
    var e = document.getElementById('audio');
    if (e != null) {
      var index = parseInt(e.getAttribute('index'));
      if (index > songs.length - 2) {
        index = 0
      } else {
        index++
      }
      e.setAttribute('index', index)
    }
    e.src = i;
    e.play();
  }
  addAudio();
  var SongIndex = parseInt(document.getElementById('audio').getAttribute('index'));
  var i = songs[SongIndex];
  cycle();
}
```
这就是一个完整的音乐播放器了。

3、调用其实就是执行 `go()` 函数，更新歌曲就修改一下 `songs[]` 数组就行了。。

终于写完了！！

> 文章中如果有错误欢迎指出，非常乐意和大家一起交流。
