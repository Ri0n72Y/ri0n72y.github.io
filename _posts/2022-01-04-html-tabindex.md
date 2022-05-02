---
title: 彻底搞清楚 onKeyDown 与 tabIndex
categories: [开发]
comments: true
---

场景：需要对网页的div元素窗口使用快捷键，按Enter确认，Esc触发onCancel。

交互：点击 button 元素触发事件，弹出一个对话框。

按照[这篇](https://blog.csdn.net/harvic880925/article/details/8524720)的说法，需要在div元素上加入tabindex就能够响应onKeyDown事件。但当然，像绝大部分国内文章一样，一如既往地没有reference。今天梯子断了雪上加霜，只能在几个官方文档里面捞针。

当然，这篇文章的说法是有效的，在为div元素添加了tabindex之后，事件可以被成功监听了。

~~摆渡？狗都不用~~

对于官方文档的定义：[Mozilla官网](https://developer.mozilla.org/zh-CN/)

菜鸟教程是个好网站，在我稍微修改了一下实例代码后运行，确实可以完成监听事件的功能。示例如下：

```html
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>菜鸟教程(runoob.com)</title>
<script>
function myFunction(){
	alert("你在输入栏内按下一个键");
}
</script>
</head>
<body>

<p>当你在输入框内按下一个按键是函数被触发</p>
<div tabindex=0 type="text" onkeydown="myFunction()">测试</div>

</body>
</html>
```

引用Runoob文档的说明：

>支持该事件的 HTML 标签：
```onkeydown 属性可用于所有的 HTML 元素，除了：<base>, <bdo>, <br>, <head>, <html>, <iframe>, <meta>, <param>, <script>, <style>, 和 <title>.```

表示```<div><span>```等标签应该也能支持这个属性。但为什么没有被触发是个值得了解的问题。

tabindex属性的功能在html5中是用于可以通过tab选中某个特定的元素。
>定义和用法
tabindex 属性规定当使用 "tab" 键进行导航时元素的顺序。

>HTML 4.01 与 HTML5之间的差异
在 HTML5 中, tabindex 属性可用于任何的 HTML 元素 (它会验证任何HTML元素。但不一定是有用)。

>在 HTML 4.01中, tabindex 属性可用于: ```<a>, <area>, <button>, <input>, <object>, <select>, 和 <textarea>。```

那么这里猜测应该是当元素被选中后，这个元素的onKeyDown事件才会被监听。此处缺失参考

既然提到了元素选中。那么这里就会尝试去选中元素，使用HTMLDom自带的focus()函数。引用文档：
>[focus()](https://www.runoob.com/jsref/met-html-focus.html) 方法用于为元素设置焦点（如果可以设置）。

“如果可以”在这里就显得很迷惑，事实上这个页面上并没有定义什么可以被设置什么不可以。

如果你想说“能不能参考onfocus事件的属性来确定哪些可以被设置焦点？”，那么[这个页面](https://www.runoob.com/jsref/event-onfocus.html)回答了这个问题。

>[hasFocus()](https://www.runoob.com/jsref/met-document-hasfocus.html) 方法返回布尔值，用于检测文档(或文档内的任一元素)是否获取焦点。

一定程度上这个函数在我们的场景下作用非常有限。也许有其他函数能告诉我到底是哪个元素获取到了焦点？

>[document.activeElement](https://www.runoob.com/jsref/prop-document-activeelement.html)确实提供了这个方法。

上述操作确实起到了效果。代码中发现，当点击 button 元素弹出 dialog，并使用 focus() 函数尝试将焦点设置到对话框上时，被选中的元素仍然是之前点击的 button，并没有设置到 dialog 上面。

测试中，div元素设置了onfocus事件，使用ref.current.focus()并没有能够触发该事件。

感谢[这篇文章](https://www.douban.com/note/238463483/?_i=1273705sFZWKot)的翻译，给出了一个通往答案的[地址](https://www.barryvan.com.au/2009/01/onfocus-and-onblur-for-divs-in-fx/)。

但当然，我们仍然没能找到任何官方文档给出的相关说明。最后一个地址中，作者标注的来源通向的站点也并非技术站点。

目前的进度如下：

onKeyDown的检测只能在focus的元素上触发，结论来自于测试，未找到官方文档；

div在设置tabindex前不能被focus, 但官方文档说onfocus可以应用在几乎所有element上面。矛盾待查证。

