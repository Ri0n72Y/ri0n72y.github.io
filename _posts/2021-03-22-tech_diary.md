---
title: Unity按帧移动角色的实现方案思考
categories: [开发]
comments: true
---

## 朴素的思路

如果使用的是纯代码的引擎的话，例如之前食用的Haxe，我会创建一个保存所有帧的数组，在移动时开始不断按顺序将渲染的图片替换掉。其中指针使用MOD数据类型实现。

MOD 数据类型是 ADA 语言中的一个特殊的 INT 类型，定义时需要额外提供一个范围，这个数字会在0到给定的自然数范围内进行循环。当给定 range 10 的时候，对这个数字 +1 会使其变成 0，同样 0-1 会变成10。是个很方便的循环指针。ADA语言会在每次操作时对其取余，使其维持准确性。

此时实现一个 Anim 对象，将其绑定在 GameObject 上面。Anim 对象会在播放时每帧更新计数器，同时将 GameObject 的对应 Sprite 替换为 Anim 的 CurrentFrame。并且设定在停止播放时将 Anim 重置为第一帧。

这当然会出现很多的问题，但为了应对“帧动画+逐帧移动”这个需求，上述实现已经够了，此时我们造好了这样一个轮子（笑）。

但很显然，我们可以简化这个过程，使用 Unity 自带的 Anim 类型，尝试达成和需求中一样的功能。

## 使用轮子

在 Awake Session 使用万能的 GetComponent 拿到 Animator 对象。

Animator 对象可以使用 SetBool, SetInteger, SetFloat 来调整内部的 parameter，同样还有对应的 get 方法。

使用了 Vector2 来计算角色面对的方向，速度。不打算设计走路与跑步的不同动画，对于速度的判断仅保留是否为空闲状态。

附上部分代码

```
    void Update()
    {
        float v = Input.GetAxisRaw("Vertical");
        float h = Input.GetAxisRaw("Horizontal");
        if (vec.magnitude < 0.01) {
            animator.SetInteger("velocity", 0);
        } else {
            animator.SetInteger("velocity", 1);
            animator.SetInteger("direction", 
                Mathf.Abs(h) < Mathf.Abs(v) ? v < 0 ? 2 : 0 : h < 0 ? 3 : 1); // 根据速度切换面对的方向
        }
    }
```
