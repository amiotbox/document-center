---
title: 多语言
type: docs
order: 6
---

在FastAdmin中可以在任何位置(控制器、视图、JS)使用`__('语言标识');`调用语言包，如果语言标识不存在，则直接输出该语言标识

## 使用方法

FastAdmin中的`__`函数和ThinkPHP中的`lang`函数在传参上有些许区别

比如
``` php
__('My name is %s', "FastAdmin");
```

将会返回

```
My name is FastAdmin
```

而如果采用ThinkPHP中的lang中的写法则是

``` php
lang('My name is %s', ["FastAdmin"]);
```

可以看到ThinkPHP中的第二个参数必须传入数组，而FastAdmin中的__则没有这个要求，其实在多个参数时FastAdmin会忽略掉第三个参数$lang
比如

``` php
__('This is %s,base on %s', 'FastAdmin', 'ThinkPHP5');
```

则会返回

```
This is FastAdmin,base on ThinkPHP5
```

而采用lang的写法则是

``` php
lang('This is %s,base on %s', ['FastAdmin', 'ThinkPHP5']);
```

因此如果要使第三个参数$lang生效，则只能将第二个参数传为数组或采用ThinkPHP中的lang函数

如果需要在HTML视图文件中使用多语言，则需要使用`{:__('Home')}`的方式调用，而在PHP和JS中均可以使用`__('Home')`的方式发起调用。

如果我们调用的多语言不存在，则会原样输出字符。例如我们调用`__('Undefined')`，则会原样输出`Undefined`字符。



## 加载方式

在FastAdmin当中，框架会自动按照当前请求的控制器进行加载对应的语言包。例如当前我们是中文环境，如果我们请求的是

```
https://demo.fastadmin.net/admin/dashboard/index
```

则FastAdmin会自动加载

```
application/admin/lang/zh-cn.php
application/admin/lang/zh-cn/Dashboard.php
```

这两个语言包

如果我们请求的路径是

```
https://demo.fastadmin.net/admin/general/config/index
```

则FastAdmin会自动加载

```
application/admin/lang/zh-cn.php
application/admin/lang/zh-cn/general/Config.php
```

这两个语言包

可以看到FastAdmin会默认加载`zh-cn.php`这个全局语言包。

如果我们需要跨模块引入其它模块的语言包，则可以在 控制器中使用loadlang方法来引入，如

```php
$this->loadlang('模块名');
```

如果需要在JS中跨模块引入语言包，则需要修改`Ajax.php`中的`lang`这个方法

## 常见问题

1. 语言包定义是不区分大小写的
2. 如果JS中对应语言包不生效，请检查服务端语言包是否有错误
3. 如果你不希望启用多语言，则可以在`application/config.php`中关闭和设置默认语言