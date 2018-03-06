---
layout: post
title: "FairyGUI With XLua"
description: "FairyGUI, XLua"
category: Game
tags: [GameLife]
---

{% include JB/setup %}


--------------------------

主要对自己接入 `FairyGUI` 和 `xLua` 过程中遇到的问题进行总结, 完整的 `Demo` 放到 [https://github.com/Garretthh07/FairyGUIWithXLua](https://github.com/Garretthh07/FairyGUIWithXLua)

从开始使用 `Unity` 也有一段时间了, 我之前做过几年的 `Cocos2d` 几乎都是使用 `Lua` 写代码比较多。

这几天在做 `Unity` 接入 `FairyGUI` 和　`XLua` 的工作, 其实说是接入有点不准确, 在 `Unity` 中使用 `FairyGUI` 或 `XLua` 提供的功能只要导入相应的包就可以了。

目前我使用的是：

- [FairyGUI](https://github.com/fairygui/FairyGUI-unity/releases "FairyGUI") 下载最新版本导入就可以了，我用的是 `FairyGUI-u5.5-1_9_3.unitypackage`
- [xLua](https://github.com/Tencent/xLua/releases "xLua") 下载最新版本导入就可以了，我用的是 `xlua_v2.1.10.zip`


`FairyGUI` 官网上有详细的介绍怎么接入 `Lua` 的例子,　目前我接入的是 `xLua` 所以我主要看这部分

[http://www.fairygui.com/guide/unity/lua.html](http://www.fairygui.com/guide/unity/lua.html)

## 实际的接入过程

参照 `FairyGUI` 官网上的介绍，还有 `xLua` 的文档我们很容易就可以把 `FairyGUI` 中使用的类和接口通过 `xLua` 导出到 `Lua` 那边来写

完整的代码参考：[CSharp2LuaConfig](https://github.com/Garretthh07/FairyGUIWithXLua/blob/master/Assets/Scripts/CSharp2LuaConfig.cs)

```
/// <summary>  
/// Fariry导出XLua接口  
/// </summary>  
public static class FairyGUIToXLuaConfig
{
    [LuaCallCSharp]
    public static List<Type> LuaCallCSharp = new List<Type>() {
        (typeof(EventContext)),
        (typeof(EventDispatcher)),
        (typeof(EventListener)),
        (typeof(InputEvent)),
        (typeof(DisplayObject)),
        (typeof(Container)),
        (typeof(Stage)),
        (typeof(Controller)),
        (typeof(GObject)),
        (typeof(GGraph)),
        (typeof(GGroup)),
        (typeof(GImage)),
        (typeof(GLoader)),
        (typeof(PlayState)),
        (typeof(GMovieClip)),
        (typeof(TextFormat)),
        (typeof(GTextField)),
        (typeof(GRichTextField)),
        (typeof(GTextInput)),
        (typeof(GComponent)),
        (typeof(GList)),
        (typeof(GRoot)),
        (typeof(GLabel)),
        (typeof(GButton)),
        (typeof(GComboBox)),
        (typeof(GProgressBar)),
        (typeof(GSlider)),
        (typeof(PopupMenu)),
        (typeof(ScrollPane)),
        (typeof(Transition)),
        (typeof(UIPackage)),
        (typeof(Window)),
        (typeof(GObjectPool)),
        (typeof(Relations)),
        (typeof(RelationType)),
    };
}
```

我这边拷贝 `FairyGUI` 官网上的介绍：

    1. 将`LuaUIHelper.cs`放入你的工程。`TweenUtils.cs`是`DOTween`的一个辅助工具类，如果你需要就放入，不需要就不放，不是必须的。
    3. 将`FairyGUI.lua`放入你的lua文件存放目录。

好的, 花了大概5分钟把这几个文件下载下来放入工程里面 (这里吐个槽, 后来我发现`ToLua`和`uLua`都有自己的`FairyGUI.lua`文件, 当时我没仔细看。)

仔细看看 `ToLua`和`uLua` 相应的 `FairyGUI.lua`：

- 把一些类变量重新定义，这样做有两个好处 1.　不需要每次都先　`FairyGUI.xxx` 2. 用法和 `C#` 里面统一
- 重写 `Add`，`Remove`等一系列方法。 至于为啥那么多的函数为啥就重写这几个函数我贴一段代码看看

```
    public void Add(EventCallback0 callback);
    public void Add(EventCallback1 callback);
```

- 发现没有? 这些函数都有参数为 `EventCallback0`, `EventCallback1` 的两个重载类型，由于 `Lua` 没有类型的概念不重写就不知道要找那个了
- `XLua` 的实现不需要在`FairyGUI.lua`中重写, 后面会说

按照示例的代码用 Lua 写断代码来测试一下：

```
UIPackage.AddPackage('TurnCard')
local view = UIPackage.CreateObject('TurnCard', 'Main')
GRoot.inst:AddChild(view)

local _c0 = view:GetChild("c0")
local _c1 = view:GetChild("c1")

print("=== 11 ===", _c0, _c1)

local function OnClick0(context)
    print("test click 00", context)
end

_c0.onClick:Add(OnClick0)

local function OnClick1(context)
    print("test click 11", context)
end

_c1.onClick:Add(OnClick1)
```

发现打印出来两个个对象了， 表示到处成功了，很开心的以为就此结束了， 那么简单就结束了也不会写总结了。
对象的答应后面还跟了一段： Lua：nil

这事怎么回事？ 发现点击按钮也没有反应。马上翻看 xLua 的文档看看自己是不是有什么地方配置错误了。 [xLua FAQ](https://github.com/Tencent/xLua/blob/master/Assets/XLua/Doc/faq.md) 里面介绍了 [重载含糊如何处理]. 好吧, 应该是重载模糊了, 从 `FairyGUI.lua` 重写这些方法就能得到这些信息。用 `Unity` 提供的 `Extension Methods`， 给这两个重载方法取一个别名 (想着如果成功把剩下的几个重载方法都用`Extension Methods`取别名, 心里想这样做太麻烦了吧, 以后 `FairyGUI` 接口有变动我不是悲剧了? 嗯,要省力就抱着稳定版不更新了), 大致写法如下：


```
[LuaCallCSharp]
[ReflectionUse]
public static class FairyGUIEventListenerExtension
{
    public static void Add0(this EventListener o, EventCallback0 callback)
    {
        o.Add(callback);
    }

    public static void Add1(this EventListener o, EventCallback1 callback)
    {
        o.Add(callback);
    }
}
```

运行程序，还是相同的结果一点变化也没有。这时候后者脸皮在xLua群里开始问大家了，经过群主和管理的热心解答，我发现 [xLua的配置](https://github.com/Tencent/xLua/blob/master/Assets/XLua/Doc/configure.md) 里面有很短的两句话：

    XLua.CSharpCallLua
    如果希望把一个lua函数适配到一个C# delegate（一类是C#侧各种回调：UI事件，delegate参数，比如List<T>:ForEach；另外一类场景是通过LuaTable的Get函数指明一个lua函数绑定到一个delegate）。或者把一个lua table适配到一个C# interface，该delegate或者interface需要加上该配置。

不得不吐槽下, 这句话下面像静态列表一样加个代码的示例可以帮助大家更快的发现问题。对于我的问题的大致意思就是 `luafunction` 要知道对应的是 `EventCallback0` 还是 `EventCallback1`， 需要使用 `[CSharpCallLua]` 注册一下, 好好好, 你说的都对，马上加上下面这段代码(这就是为啥对于`xLua`不需要`FairyGUI.lua`的原因)：

```
[CSharpCallLua]
public static List<Type> CSharpCallLua = new List<Type>()
{
    (typeof(EventCallback0)),
    (typeof(EventCallback1)),
};
```

运行测试，神奇的事情发生了，点击的测试逻辑通了。 后来我观察了下 `ToLua`和`uLua`的`FairyGUI.lua`都只是用到了`EventCallback1`这种委托来处理回掉, 想想也可以理解, 有对象总比没有好， 写UI有不用太在乎效率。把 `Extension Methods` 相关全部删除，能正确获取到回调传来的`EventContext`参数。 经过一天多的写了几个界面的测试，发现没有啥大的问题，写法也和 C# 里面及其的相似。

最后感谢 xLua作者和 FairyGUI作者, 还有群里码友的热心解答。

### 完整的Demo
完整的 `Demo` 放到 https://github.com/Garretthh07/FairyGUIWithXLua
