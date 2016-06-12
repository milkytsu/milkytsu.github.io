---
layout: post
title:  "PHP的自动载入"
date:   2016-01-17 00:14:29 +0800
categories: jekyll update
---

# PHP的自动载入

### 一、`__autoload()`
　　当PHP程序在实例化（或调用）一个在当前命名空间没有定义（或引入）的类（或类的静态方法）时，__autoload()函数实现的规则来引入该类。与提前include或require入类文件不同，该方法是在实现一个类的时候才去引入，可以减少引入不必要的类，浪费资源。
　　但 `__autoload()`有一个特点是在一次请求的生命周期中只能定义一个该函数，所以在PHP项目日渐庞大或各个项目组合并代码时，可能会造成`__autoload()`方法的冲突，或者`__autoload()`方法的实现非常臃肿。
　　PHP官方已不推荐使用该方法，而是推荐使用PHP标准库的`spl_autoload_register()`，该方法可以注册多个自动加载函数，依次执行，在大型整合项目中使用各自不同的自动加载方法很又用。

### 二、`spl_autoload_register()`
　　spl是Standard PHP Library（PHP标准库）的缩写。`spl_autoload_register()` 详细介绍请参阅PHP官方文档。该函数作用是将传入的函数注册到“自动加载函数栈”中，PHP遇到调用未定义的类的时候，会从该栈中依次尝试按照相应函数去加载类文件，直到加载成功或加载失败退出。如果已经在通过`spl_autoload_register()`注册自动加载的程序中实现了`__autoload()`函数，则`__autoload()`会失效。如果想使该函数仍然生效，需要把`__autoload()`注册至`spl_autoload_register()`：


{% highlight php %}
spl_autoload_register('__autoload');
{% endhighlight %}

### 三、`spl_autoload()`
　　在官方手册的`spl_autoload_register()`例子中，看到有人使用`spl_autoload()`而不是`require()`来加载类文件。官方手册对`spl_autoload()`的解释为：

> `__autoload()`函数的默认实现

　　不理解这个解释的意思，但看了官方手册上的一些例子，感觉spl_autoload()函数的作用就类似于include()？网上搜索到的资料说在脚本中第一次调用`spl_autoload_register()`并不传任何参数时，可以将`autoload_func`指向`spl_autoload()`，即使`spl_autoload()`生效。那是否意味着如果不调用`spl_autoload_register()`或调用但传入参数，`spl_autoload()`就不起作用？做个实验好了。两个同级目录文件：index.php和test.php
{% highlight php %}
/** index.php: */
<?php
spl_autoload('test');
$o = new Test();
$o->a();
{% endhighlight %}

{% highlight php %}
/** test.php */
<?php
class Test
{
    public function a()
    {
        echo 'test success!';
    }
}
{% endhighlight %}

　　执行`php index.php`，成功打印出`test success!`，说明`spl_autoload()`的作用即类似于`include`，回头阅读PHP源码时可以再看看`spl_autoload()`的实现。另外也说明，并不需要在第一次调用`spl_autoload_register()`时不传参数才能激活`spl_autoload()`。为了确认`spl_autoload()`不受`spl_autoload_register()`的影响，修改index.php的代码为如下：

{% highlight php %}
/** index.php: */
<?php
function autoload($classname) {
    require('../' . $classname . '.php');
}
spl_autoload_register('autoload');
spl_autoload('test');
$o = new Test();
$o->a();
{% endhighlight %}

　　执行`php index.php`，也成功输出，说明`spl_autoload_register()`并没有影响到`spl_autoload()`；虽然`spl_autoload_register()`注册的autoload函数是去上级目录引入文件，但由于`spl_autoload('test')`引入了文件（相当于`include('test.php')`），所以仍然可以调用到Test类。但有个问题是，`spl_autoload()`函数只能调用当前目录下的类，而不能指定相对路径或绝对路径。所以该函数的意义是？

### 四、总结
　　各种资料查阅下来，疑问还有很多，在segmentfault发了提问：[http://segmentfault.com/q/1010000004321436](http://segmentfault.com/q/1010000004321436)

　　有朋友已经回答了。在代码编写过程中，只要关注`spl_autoload_register()`即可。准备抽空继续看一下Yii框架和Laravel框架以及composer的自动载入的实现，搞明白后写文章来总结一下。