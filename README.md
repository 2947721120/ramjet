# ramjet

![ramjet](https://cloud.githubusercontent.com/assets/1162160/7279487/5d668dea-e8ea-11e4-9b0d-a9ba2f1165cc.gif)


## 安装

`npm install ramjet`, 或下载[ramjet.js](http://www.rich-harris.co.uk/ramjet/ramjet.js).


## 快速使用

```html
<div id='a' style='background-color: red; font-size: 4em; padding: 1em;'>a</div>
<div id='b' style='background-color: blue; font-size: 4em; padding: 1em;'>b</div>

<script src='ramjet.js'></script>
<script>
  var element1 = document.getElementById('a'),
      element2 = document.getElementById('b');

  //重复，从控制台运行此
  ramjet.transform( element1, element2 );
</script>
```


## 好了，所以......这是什么呢？

冲压发动机使它看起来像你的DOM元素都能够转化成另一个。它通过克隆的元素（和他们的孩子），转化的第二个元素（我们改变一个做到这一点 *to*) 使其完全与第一重叠，然后动画两个元件一起，直到第一元件（我们转换所述一个 *from*)有完全一样的位置和尺寸，作为第二个元素。

它基本上是用于iOS 8看起来好像每个应用程序在其图标相同的技术。

![ios8-effect](https://cloud.githubusercontent.com/assets/1162160/7281378/4f949858-e8f7-11e4-8acf-9a1d90049a92.gif)

在现代的浏览器，它使用CSS动画，所以一切都发生在主线程。结果是
**buttery-smooth performance**, 即使在移动设备上。

## API

### ramjet.transform( a, b[, options] )

* `a` is the DOM node we're transitioning from
* `b` is the DOM node we're transitioning to
* `options` can include the following properties:
    * `done` - a function that gets called once the transition completes
    * `duration` - the length of the transition, in milliseconds (default 400)
    * `easing` - a function used to control the animation. Should take a number between 0 and 1, and return something similar (though it can return a number outside those bounds, if you're doing e.g. an [elastic easing function](http://easings.net/#easeOutElastic)). I highly recommend [eases](https://www.npmjs.com/package/eases) by [Matt DesLauriers](https://github.com/mattdesl), which is a handy collection of these functions
    * `easingScale` - if defined it will use a different easing function for scaling. It can be used to create cartoonish effects.
    * `useTimer` - by default, ramjet will use CSS animations. Sometimes (when transitioning to or from SVG elements, or in very old browsers) it will fall back to timer-based animations (i.e. with `requestAnimationFrame` or `setTimeout`). If you want to always use timers, make this option `true` - but I don't recommend it (it's much more juddery on mobile)
    * `overrideClone` (advanced) - look at the section `Advanced options`
    * `appendToBody` (advanced) - look at the section `Advanced options`

### ramjet.hide( ...nodes )

方便的功能，用于设置每个节点的不透明度为0（临时禁用任何过渡，否则可能会干扰）。
### ramjet.show( ...nodes )

与之相对 `ramjet.hide`.

### ramjet.linear, ramjet.easeIn, ramjet.easeOut, ramjet.easeInOut

A handful of easing functions, included for convenience.


## Browser support

Successfully tested in IE9+, Chrome (desktop and Android), Firefox, Safari 6+ and mobile Safari - please raise an issue if your experience differs!


## Developing and testing

Once you've cloned this repo and installed all the development dependencies (`npm install`), you can start a development server by running `npm start` and navigating to [localhost:4567](http://localhost:4567). Any changes to the source code (in the `src` directory) will be immediately reflected, courtesy of [gobble](https://github.com/gobblejs/gobble).

To build, do `npm run build`.

Reliable automated tests of a library like ramjet are all but impossible. Instead `npm test` will start the development server and navigate you to [localhost:4567/test.html](http://localhost:4567/test.html), where you can visually check that the library behaves as expected.


## Advanced options
The option `overrideClone` (function) overrides the function used to clone nodes (the default implementation uses a simple node.cloneNode()). It takes as a parameters the current node and the depth of this node compared to the original element (it is called recursively on the node subtree). It can be useful for removing annoying attributes or children from the cloned node. For example if a node contains a video element with autoplay, this can be excluded because it may be heavy to animate and you can heard the audio of it. Examples:

```js
// cloning only the root node
ramjet.transform( element1, element2, {
  overrideClone: function (node, depth){
    if (depth == 0){
      return node.cloneNode(); // copy only the root node
    }
  }
});

// cloning everything but the id attribute
ramjet.transform( element1, element2, {
  overrideClone: function (node, depth){
    var clone = node.cloneNode();
    clone.removeAttr('id');
  }
});

// not cloning the video element
ramjet.transform( element1, element2, {
  overrideClone: function (node, depth){
    if (node.nodeType === 1 && node.tagName === "VIDEO"){
      return;
    }
    return node.cloneNode();
  }
});
```

By default the cloned nodes are appended to the parent to the original node. Inheriting the positioning and css inherited rules, they can behave in an unexpected way. For this reason you can use the flag `appendToBody` to append these nodes to the boby instead. I invite everyone to set this to true and open an issue if it doesn't work, it may become the default in one of the next releases.

## License

MIT.
