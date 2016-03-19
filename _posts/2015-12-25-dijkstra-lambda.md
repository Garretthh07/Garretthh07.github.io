---
layout: post
title: "Dijkstra Lambda"
description: "Lambda I learned"
category: [Lambda]
tags: [Lambda,Life]
---

{% include JB/setup %}

> 今天稍微看了下 `Java` 中 `Lambda` 的实现，`Lambda` 是 `Java 8` 中添加的新特性, 如果要我评价这个发明是不是伟大，答案一定是肯定的：


#### 为什么需要 Lambda

<br>

	
Java 中增加 Lambda 说实话并没有增加什么新的功能。只是可以使用更少的代码，更优雅的实现相关的功能。俗话说的好：

> 越少的代码实现相关功能，才能犯越少的错误。就算犯错了也能快速的定位，并修改掉。


参考： [Java programming with lambda expressions](http://www.javaworld.com/article/2092260/java-se/java-programming-with-lambda-expressions.html) 

Java 不像 C++ 支持函数指针传递给另外的函数调用，也不像 Lua 函数本身就是一种基本类型。 想要实现类似于传递函数给另外的函数在内部调用，一般都是先实现一个只有一个方法的接口（**计为 A**），调用方式如下：

- 写一个类以 `A` 作为接口，实现 `A` 接口的方法， 以 `A` 作为函数的参数，把 new 出来的该类传递过去。这样实现有几个弊端：
	- 需要这个接口实现另外的功能就需要另外新建一个类继承这个接口，需求越多重复的工作就越多
	- 接口只能有一个方法显得累赘
	
- 使用匿名函数可以不用每次有新功能就增加一个类，但是代码冗长一点都不优雅
- Lambda 用如下语法来扩展了接口，一句话就可以实现，把编码中心真的放在功能的实现上，结果也是一目了然。
	
	
		(double x) -> Math.sin(x)

- **java.util.function** 的引入可以不用新增接口就可以实现相关工作

<br>


#### 总结


<br>


[Java SE 8: Lambda Quick Start](http://www.oracle.com/webfolder/technetwork/tutorials/obe/java/Lambda-QuickStart/index.html) 里面有更佳详细的介绍，纪录一下：

> Runnable, 按钮的点击回调这些需要实现一个 **Listener** 接口方法的都可以使用 Lambda 来大大的简化， Lambda 表达式还可以嵌套使用，感觉还是可以比较清晰的实现相关功能的，而且可以写的比较优雅。


<br>


#### Life Tips


<br>


[![Dijkstra](http://7xpgi9.com1.z0.glb.clouddn.com/dijkstra.png "Dijkstra")](http://v.youku.com/v_show/id_XNjc1MDMzNDg4.html)

> 现在很多人都喜欢把比自己厉害的人叫做大牛，感觉这是一种很不好的风气。我感觉应该分为另自己尊敬的人和和自己无关的人，这个划分完全按照个人而定，所以我也没有资格来评价别人啦。 Dijkstral 的采访视屏有很多获益匪浅的话在这里记录一下，诚然他是令我尊敬的人：

- As long as you can fool people into buging it, you can always try to make better versions later. Even with decimals, version 2.6 or 2.6.That nonsense, while version 1 should have been the finished product.

- The universities lack the courage to teach hard science. The will continue to misguide the students and each stage in the infantilization of the curriculum will be hailed as educational progress

- There are very different programming styles. I tend to see them as Mozart versus Beethoven. When Mozart started to write, the composition was finished. He wrote the manuscript in one go.In beautiful handwriting, too. Beethoven was a doubter and a struggler, who started writing before he finished the composition and then glued corrections onto the page. In one place he did this nine times, when they peeled them, the last version proved identical to the first one.

- People thought that those languages `(Fortran, Pascal, C++ and such like)` would solve the programming problem. But when you look closely, the trivial aspects of programming had been automated, while the hard ones remained.

- If in physics there's something you don't understand, you can always hide behind the uncharted depths of nature. You can always blame God. You didn't make it so complex yourself. But if your program doesn't work, there is no one to hide behind. You cannot hide behind an obstinate nature. A zero is a zero, a one is a one. If it doesn't work, you've messed up.

- I knew that each Apollo flight required some 40,000 new lines of code. I don't know what unit a line of code is, but 40,000 is a lot. I was duly impressed that they got so many lines of code correct:
	- He said that in one of the calculations of the orbit of the lunar module, the moon had been defined as repelling instead of attracting.
	
	- They  had discovered that error by accident, five days before the shot.

- Program testing can convincingly show the presence of bugs, but is is hopelessly inadequate to show their absence.

- Elegance is not a dispensable luxury, but a factor that decides between success and failure. **Elegent** may be meant as **ingeniously simple and effective** -> the most elegant programs are often the most efficient.

- Make sure you know all the formulae by heart, and if you need more than five lines, you're on the wrong track.

- Elegance has the disadvantage, if that's what it is, that hard work is needed to achieve it and a good education to appeaciate it.


> 记录那么多像是再做毫无益处的苦力，我可以安慰自己这又学习了英语又加深了理解，其实所有的都是假象，当我发现自己花了一个小时做了一件蠢事的时候，已经做完了， 剩下的自由懊悔不已。 温习还得点击图片看视屏。