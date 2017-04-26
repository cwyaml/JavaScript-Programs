Javasvript 30 天的挑战就从今天开始吧！！

### 简介
第一天，我们做一个小特效：Drum Kit。
模拟一个爵士鼓的效果，通过敲击键盘上不同的字母，发出不同的声音，并且页面上产生伴随着敲击的动画。

### 项目分析
1、键盘事件：监听用户按下了哪个键，为了效果更好选择使用 **keydown** 事件；
2、播放音频：用户按下按键时播放音频，用到了 `video.play()`；
3、添加样式：用户按下按键时给相应 DOM 添加 CSS样式，`Element.classList.add('className')`
4、移除样式：用户松开按键时把样式移除，`Element.classList.remove('className')`

> 原理很简单，现在动手来实现一下。

### 详细步骤
1、创建页面布局

![布局效果](http://upload-images.jianshu.io/upload_images/4030390-7386124ad9291fb0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

``` html
<div class="keys">
    <div data-key="65" class="key">
      <kbd>A</kbd>
      <span class="sound">clap</span>
    </div>
    <div data-key="83" class="key">
      <kbd>S</kbd>
      <span class="sound">hihat</span>
    </div>
    <div data-key="68" class="key">
      <kbd>D</kbd>
      <span class="sound">kick</span>
    </div>
    <div data-key="70" class="key">
      <kbd>F</kbd>
      <span class="sound">openhat</span>
    </div>
    <div data-key="71" class="key">
      <kbd>G</kbd>
      <span class="sound">boom</span>
    </div>
    <div data-key="72" class="key">
      <kbd>H</kbd>
      <span class="sound">ride</span>
    </div>
    <div data-key="74" class="key">
      <kbd>J</kbd>
      <span class="sound">snare</span>
    </div>
    <div data-key="75" class="key">
      <kbd>K</kbd>
      <span class="sound">tom</span>
    </div>
    <div data-key="76" class="key">
      <kbd>L</kbd>
      <span class="sound">tink</span>
    </div>
  </div>

  <audio data-key="65" src="sounds/clap.wav"></audio>
  <audio data-key="83" src="sounds/hihat.wav"></audio>
  <audio data-key="68" src="sounds/kick.wav"></audio>
  <audio data-key="70" src="sounds/openhat.wav"></audio>
  <audio data-key="71" src="sounds/boom.wav"></audio>
  <audio data-key="72" src="sounds/ride.wav"></audio>
  <audio data-key="74" src="sounds/snare.wav"></audio>
  <audio data-key="75" src="sounds/tom.wav"></audio>
  <audio data-key="76" src="sounds/tink.wav"></audio>
```

通过 `data-key` 将页面中的 DOM 和 `audio` 关联起来。
`date-*` 是 HTML5 中新增的自定义属性功能。
### 添加样式
``` css
    html{
      font-size: 10px;
    }
    html,body{
      margin: 0;
      padding: 0;
    }
    .keys{
      display: flex;      
      flex: 1;
      align-items: center;
      justify-content: center;
      min-height: 100vh;
      text-shadow: 0 0 .5rem #000;
    }
    .key{
      width: 10rem;
      padding: 1rem .5rem;
      margin: 1rem;
      border: .4rem solid #000;
      border-radius: .5rem;
      font-size: 1.5rem;
      text-align: center;
      color: #fff;
      background: rgba(0,0,0,.4);
      transition: all .07s ease;  //动画的过度时间和方式
    }
    kbd{
      display: block;
      font-size: 4rem;
    }
    .sound{
      font-size: 1.2rem;
      color: #ffc600;
      letter-spacing: .1rem;       
      text-transform: uppercase;      //文字转换为大写
    }

    .playing {
      transform: scale(1.1);
      border-color: #ffc600;
      box-shadow: 0 0 1rem #ffc600;
    }
```

- 使用了 **flex** 布局（CSS3中的布局方式）。具体使用方法可以参考我的这两篇文章：
[Flex布局教程 - 语法篇](http://www.jianshu.com/p/5405f9bf9996)
[Flex布局教程 - 常用Flex布局的写法](http://www.jianshu.com/p/92d8e9f708a6)

- 定义 `.palying` 类，在按键按下的时侯为该元素添加 playing 类，结束后将其移除。
- `transform: scale(1.1)` 将元素缩放为原来的 1.1 倍；
- `transition: all .07s ease;` 使整个动画过程在 0.07 秒内完成。

### 按键监听 & 播放音频 & 添加动画
``` js
  function playAudio(event){
    var num = event.keyCode;    
    const audio = document.querySelector(`audio[data-key="${num}"]`);
     if(!audio) return;  
    const key = document.querySelector(`div[data-key="${num}"]`);
    if(!key) return;

    audio.currentTime = 0;     
    audio.play();    
    key.classList.add('playing');   
  }
//给页面添加键盘事件
  window.addEventListener('keydown',playAudio);
```

- 监听页面的 `keydown` 事件，触发 `playAudio()` 函数。

- 每一个按键都有自己的键码 `KeyCode`，可以通过按键的 keyCode 来判断被按下的是哪个键。如果不清楚每一个按键的keyCode是多少，可以访问 [这个网站](http://keycode.info/) 。

- 获取到 keyCode 并赋值给 num 。然后我们用到了 *ES6* 的模板字符串，`${num}`，可以动态的将按键的 Keycode 传过去，以使 audio 动态的获取每一个按键绑定的 DOM 和 audio。
需要注意的是模板字符串一定要使用'`' (Esc下面那个键)包裹，而不是双引号。

- 我们注意到 `audio.play();` 前面一行是 `audio.currentTime = 0;`，这是为了解决，当我连续点击某一按键（或者按住按键不放）的时候，只有第一次点击会响，第二次第三次连续的点击可能没声音。所以在每一次点击之前 **重置音效** 是很有必要的。

- `key.classList.add('playing');` 可以在按键点击的同时为该元素添加playing 类，展示小动画。

- `if(!audio) return;  if(!key) return;` 因为并不是每一个按键都有音效，当用户点击了非绑定音效按键，及时退出函数是很好的习惯，避免内存浪费。

### 移除动画
``` js
function remove(event) {
    this.classList.remove('playing');
  }
  var keys = document.querySelectorAll('.key');
  keys.forEach(key => key.addEventListener('transitionend', remove));
```

- 监听每一个按键元素的 `transitionend` 事件，当按键元素的动画结束后会触发 `remove()` 函数。
**注意：** `transitionend` 事件会在CSS中的 `transition: all .07s ease;` 动画执行完毕后触发。

- 在定位元素的时候，可以使用 `this` 也可以使用 `e.target` ,可以简单这么理解，this 值的是谁出发了这次事件，也就是 key，就等同于事件的目标 e.target.

- 使用 `document.querySelector` 获取一组符合 CSS 选择符的元素快照，类型为 `NodeList`（此对象是对于文档的实时运行的动态查询），使用 `forEach()` 对其进行遍历。

### JS汇总
``` js
<script>
  //键盘监听 & 播放音频 & 添加动画
  function playAudio(event){
    var num = event.keyCode;    //获取被按下的按键的键码
    const audio = document.querySelector(`audio[data-key="${num}"]`);
    const key = document.querySelector(`div[data-key="${num}"]`);
    if(!audio) return;

    audio.currentTime = 0;     //确保按键不被松开时一直播放音频
    audio.play();              //播放音频
    key.classList.add('playing');      //添加动画
  }
  //移除动画
  function remove(event) {
    this.classList.remove('playing');  //移除动画
  }

  var keys = document.querySelectorAll('.key');
  keys.forEach(key => key.addEventListener('transitionend', remove));
  window.addEventListener('keydown',playAudio);
</script>
```
# Javascript30
