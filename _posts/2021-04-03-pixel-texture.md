---
title: Unreal Engine Paper2D 像素画素材的导入
categories: [杂谈]
comments: true
---

问题场景：尝试使用UE来开发一个2D像素游戏时（什么？高射炮打蚊子？我听不见！），素材导入变糊。原因当然都知道，为了看起来更舒服，引擎通常都会把素材过滤（filter）一下。但像素游戏当然不适合默认的处理方式。本文目的在于取消这个filter

按照正常流程导入png素材，一般放在工程文件夹的textures文件夹下面。

双击打开纹理界面，属性栏中找到 层次细节 标签，调整下面的 纹理组 项目，选择2d像素（未过滤）

这样可以使像素不被过滤，直接使用其细节。

纹理组|texture group 以及相关的设置详情可以在官方文档中详细了解。

补充知识：

关于 MIP生成设置 [MIP贴图是什么](2021-04-03-tech-tips.md)

关于 纹理组 参考[官方文档](https://docs.unrealengine.com/zh-CN/RenderingAndGraphics/Textures/Properties/index.html)
