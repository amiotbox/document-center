---
title: 模块
type: docs
order: 9
---

## 前台

这里的前台指整个前台index模块，这里仅做部分前台功能使用介绍，如果需要查看前端开发文档，请查看前端章节的文档

FastAdmin的前台首页比较简单，只有一个单页面。同时在FastAdmin中我们编写了一个简单的会员中心，只有简单的注册、登录、找回密码、个人中心等。其它功能都需要自己二次开发。当然FastAdmin中提供有`CMS`和`博客`插件，这两个插件都有完整的前后台功能。

### 流程介绍

FastAdmin的前台模块完全遵循ThinkPHP5的开发规范，在此基础上我们在前台提供了一个类似后台的权限验证模块，我们可以方便快捷的控制我们的可访问权限。

### 基类解析

#### 基类控制器

前台的所有功能模块的控制器都是继承于`application/common/controller/Frontend.php`这个基类控制器

在基类控制器中我们有定义一些基础属性和通用方法，首先们我看看基础属性

```php
/**
 * 布局模板
 * @var string
 */
protected $layout = '';

/**
 * 无需登录的方法,同时也就不需要鉴权了
 * @var array
 */
protected $noNeedLogin = [];

/**
 * 无需鉴权的方法,但需要登录
 * @var array
 */
protected $noNeedRight = [];

/**
 * 权限Auth
 * @var Auth 
 */
protected $auth = null;
```

其次我们来看下基类的方法

```php
/**
 * 加载语言文件
 * @param string $name
 */
protected function loadlang($name)
{
}

/**
 * 渲染配置信息
 * @param mixed $name 键名或数组
 * @param mixed $value 值 
 */
protected function assignconfig($name, $value = '')
{
}
```

以上的属性和方法我们都可以通过在当前控制器定义来达到覆盖的目的。

### 功能模块

#### 首页模块

首页模块比较简单，只是一个单页。完全遵循ThinkPHP5的开发结构。你可以按需修改或移除此功能模块。

#### 会员模块

FastAdmin的前台自带一个简单的会员功能模块，可以进行会员的注册、登录、找回密码、会员中心、修改个人资料、修改密码等操作。

会员模块可用于进行前台会员功能开发时使用。此处的会员模块和API中的会员模块账号是相通的，但他们登录时是不会互相影响的，可以同时登录。

FastAdmin的会员模块有注册几个事件，我们可以在事件中自定义我们的操作。你可以按照以下的方式监听相应的事件行为

```php
//登录成功后的事件
Hook::add('user_login_successed', function ($user) {
});
//注册成功后的事件
Hook::add('user_register_successed', function ($user) {
});
//会员删除后的事件
Hook::add('user_delete_successed', function ($user) {
});
//会员注销后的事件
Hook::add('user_logout_successed', function ($user) {
});
```

## API

这里的API指整个API接口模块，这里仅做部分API功能模块的使用介绍，如果需要查看前端开发和后端开发文档，请查看相对应的文档

### 流程介绍

FastAdmin的API模块完全遵循ThinkPHP5的开发规范，在此基础上我们在API模块中提供了一个和前台相同的权限验证模块，我们可以方便快捷的控制我们的可访问权限。

FastAdmin的API模块采用默认的路由方式进行匹配，当然我们完全可以自定义我们的路由规则，达到个性化URL接口的目的

### 基类解析

API的所有功能模块的控制器都是继承于`application/common/controller/Api.php`这个基类控制器

在基类控制器中我们有定义一些基础属性和通用方法，首先们我看看基础属性

```php
/**
 * @var array 前置操作方法列表
 */
protected $beforeActionList = [];

/**
 * 无需登录的方法,同时也就不需要鉴权了
 * @var array
 */
protected $noNeedLogin = [];

/**
 * 无需鉴权的方法,但需要登录
 * @var array
 */
protected $noNeedRight = [];

/**
 * 权限Auth
 * @var Auth 
 */
protected $auth = null;
```

其次我们来看下基类的方法

```php


/**
 * 加载语言文件
 * @param string $name
 */
protected function loadlang($name)
{
}

/**
 * 操作成功返回的数据
 * @param string $msg   提示信息
 * @param mixed $data   要返回的数据
 * @param int   $code   错误码，默认为1
 * @param string $type  输出类型
 * @param array $header 发送的 Header 信息
 */
protected function success($msg = '', $data = null, $code = 1, $type = null, array $header = [])
{
}

/**
 * 操作失败返回的数据
 * @param string $msg   提示信息
 * @param mixed $data   要返回的数据
 * @param int   $code   错误码，默认为0
 * @param string $type  输出类型
 * @param array $header 发送的 Header 信息
 */
protected function error($msg = '', $data = null, $code = 0, $type = null, array $header = [])
{
}

/**
 * 返回封装后的 API 数据到客户端
 * @access protected
 * @param mixed  $msg    提示信息
 * @param mixed  $data   要返回的数据
 * @param int    $code   错误码，默认为0
 * @param string $type   输出类型，支持json/xml/jsonp
 * @param array  $header 发送的 Header 信息
 * @return void
 * @throws HttpResponseException
 */
protected function result($msg, $data = null, $code = 0, $type = null, array $header = [])
{
}

/**
 * 前置操作
 * @access protected
 * @param  string $method  前置操作方法名
 * @param  array  $options 调用参数 ['only'=>[...]] 或者 ['except'=>[...]]
 * @return void
 */
protected function beforeAction($method, $options = [])
{
}

/**
 * 验证数据
 * @access protected
 * @param  array        $data     数据
 * @param  string|array $validate 验证器名或者验证规则数组
 * @param  array        $message  提示信息
 * @param  bool         $batch    是否批量验证
 * @param  mixed        $callback 回调方法（闭包）
 * @return array|string|true
 * @throws ValidateException
 */
protected function validate($data, $validate, $message = [], $batch = false, $callback = null)
{
}
```

以上的属性和方法我们都可以通过在当前控制器定义来达到覆盖的目的。

### 功能模块

#### 会员模块

我们在FastAdmin的API中集成了简单的会员接口，可以进行会员的注册、登录、找回密码、会员中心、修改个人资料、修改密码等操作。

会员模块可用于进行API会员功能开发时使用。此处的会员模块和前台中的会员模块账号是相通的，但他们登录时是不会互相影响的，可以同时登录。

FastAdmin的会员模块有注册几个事件，我们可以在事件中自定义我们的操作。你可以按照以下的方式监听相应的事件行为

```php
//登录成功后的事件
Hook::add('user_login_successed', function ($user) {
});
//注册成功后的事件
Hook::add('user_register_successed', function ($user) {
});
//会员删除后的事件
Hook::add('user_delete_successed', function ($user) {
});
//会员注销后的事件
Hook::add('user_logout_successed', function ($user) {
});
```

#### 短信模块

我们在FastAdmin的API中集成了简单的短信模块，可以根据相对应的事件进行短信的发送和检测是否正确等功能。

在使用短信模块时请确保已经正确在后台正确安装并启用了第三方短信插件。

#### 公共模块

公共模块一般用于客户端应用初始化时调用，例如APP的版本检测、APP的首屏轮换图等功能。

#### 检测模块

检测模块一般用于检测客户端提交数据的有效性验证，也常用于在前台进行数据录入时的实时有效性校验。

## 后台

这里的后台指整个后台管理，在此仅做后台的流程介绍、核心类解析及相关功能模块功能使用介绍，如果需要查看前端开发文档，请前往相应章节查看文档

### 流程介绍

首先需要知道FastAdmin的后台模块是禁用了路由功能的，因此后台的操作都是根据URL进行分段解析，例如我们请求的是

```
https://demo.fastadmin.net/admin/
```

则调用的是默认控制器`Index.php`中的默认方法`index`

如果我们请求的是

```
https://demo.fastadmin.net/admin/dashboard/index
```

则调用的是`Dashboard.php`中的`index`方法

框架在调用到`Dashboard.php`这个控制器的`index`方法后会自动渲染`application/admin/view/dashboard/index.html`这个视图文件。

如果需要修改显示的内容，则修改这个这个视图文件即可。

但我们会发现有些控制器并没有`index`,`add`,`edit`,`del`等方法，但其实这些方法都在控制器的父类中采用了`traits`进行引入，我们转到父类`application/common/controller/Backend.php`就可以看到有一行

```
use \app\admin\library\traits\Backend.php
```

这一行就相当于把文件`application/admin/library/traits/Backend.php`中的所有方法引入到当前控制器。如果我们需要覆盖基类定义的方法，则直接在当前控制器中定义即可。

### 基类解析

后台的所有功能模块的控制器都是继承于`application/common/controller/Backend.php`这个基类控制器

在基类控制器中我们有定义一些基础属性和通用方法，首先们我看看基础属性

```php
/**
 * 无需登录的方法,同时也就不需要鉴权了
 * @var array
 */
protected $noNeedLogin = [];

/**
 * 无需鉴权的方法,但需要登录
 * @var array
 */
protected $noNeedRight = [];

/**
 * 布局模板
 * @var string
 */
protected $layout = 'default';

/**
 * 权限控制类
 * @var Auth
 */
protected $auth = null;

/**
 * 快速搜索时执行查找的字段
 */
protected $searchFields = 'id';

/**
 * 是否是关联查询
 */
protected $relationSearch = false;

/**
 * 是否开启数据限制
 * 支持auth/personal
 * 表示按权限判断/仅限个人 
 * 默认为禁用,若启用请务必保证表中存在admin_id字段
 */
protected $dataLimit = false;

/**
 * 数据限制字段
 */
protected $dataLimitField = 'admin_id';

/**
 * 数据限制开启时自动填充限制字段值
 */
protected $dataLimitFieldAutoFill = true;

/**
 * 是否开启Validate验证
 */
protected $modelValidate = false;

/**
 * 是否开启模型场景验证
 */
protected $modelSceneValidate = false;

/**
 * Multi方法可批量修改的字段
 */
protected $multiFields = 'status';

/**
 * 导入文件首行类型
 * 支持comment/name
 * 表示注释或字段名
 */
protected $importHeadType = 'comment';
```

其次我们来看下通用的方法

```php

/**
 * 加载语言文件
 * @param string $name
 */
protected function loadlang($name)
{

}

/**
 * 渲染配置信息
 * @param mixed $name 键名或数组
 * @param mixed $value 值 
 */
protected function assignconfig($name, $value = '')
{

}

/**
 * 生成查询所需要的条件,排序方式
 * @param mixed $searchfields 快速查询的字段
 * @param boolean $relationSearch 是否关联查询
 * @return array
 */
protected function buildparams($searchfields = null, $relationSearch = null)
{

}

/**
 * 获取数据限制的管理员ID
 * 禁用数据限制时返回的是null
 * @return mixed
 */
protected function getDataLimitAdminIds()
{

}

/**
 * Selectpage的实现方法
 * 
 * 当前方法只是一个比较通用的搜索匹配,请按需重载此方法来编写自己的搜索逻辑,$where按自己的需求写即可
 * 这里示例了所有的参数，所以比较复杂，实现上自己实现只需简单的几行即可
 * 
 */
protected function selectpage()
{
}
```

以上的属性和方法我们都可以通过在当前控制器定义来达到覆盖的目的。

### 功能模块

#### 常规管理

在后台管理中一些基础配置，例如系统配置、附件管理、文件管理、数据库管理、个人配置等功能都归属到该级栏目下面

##### 系统配置

在开发中经常会遇到一些配置信息可以在后台进行修改的功能，此时我们在系统配置中进行增改操作。系统配置中的配置项不支持删除功能，如果需要删除配置项，需要删除数据库中`fa_config`中相对应的行。

在系统配置中的`添加`一栏，我们可以自定义添加系统配置。以下是添加项的详细解释

| 类型     | 介绍                                                         |
| -------- | ------------------------------------------------------------ |
| 类型     | 主要是字符、文本、数字、日期时间、列表、图片、文件、复选、单选、数组等类型 |
| 分组     | 配置所属的分组                                               |
| 变量名   | 变量名，只能使用数字、字母、下划线定义。在视图中可以使用`{$site.变量名调用}`，在PHP中可以使用`config('site.变量名')`调用 |
| 变量标题 | 配置对应显示的中文名称                                       |
| 变量值   | 配置项的基础值                                               |
| 提示信息 | 当配置项获得焦点时提示的文字信息                             |
| 校验规则 | 校验规则使用的是`nice-validator`的规则，可以查看：https://validator.niceue.com/docs/core-rules.html，多个规则使用`;`进行分隔 |
| 扩展属性 | 用于给生成的DOM元素添加额外的扩展属性                        |

系统配置支持多种数据类型，下面依次做简单介绍

| 类型    | 介绍                      |
| ----- | ----------------------- |
| 字符    | 生成单行文本框                 |
| 文本    | 生成多行文本框                 |
| 数字    | 生成单行数字文本框               |
| 日期    | 生成只可日期的日期选择框            |
| 时间    | 生成输入时间的时间选择框            |
| 日期时间  | 生成文本框且自动生成日期时间选择器       |
| 列表    | 生成下拉列表框                 |
| 列表(多) | 生成多选下拉列表框               |
| 图片    | 生成单图文本框且上传或选择单图，带图片预览   |
| 图片(多) | 生成多图文本框且可上传或选择多张图，带图片预览 |
| 文件    | 生成文本框且可上传或选择文件          |
| 文件(多) | 生成文本框且可上传或选择多个文件        |
| 复选    | 生成复选框                   |
| 单选    | 生成单选框                   |
| 数组    | 生成一维数组输入列表且可动态添加和排序     |
| 自定义   | 可以直接自定义元素的HTML代码        |

##### 分类管理

分类模块是我们在开发中经常会使用到的一个模块，FastAdmin集成了一个简单的通用分类功能模块，我们可以进行无限级分类的录入与管理。以便于在前台功能模块开发时可以调用分类中的数据。

##### 附件管理

附件管理可以管理后台上传的文件资源，也可以在此上传资源到服务器

附件管理中的删除只会删除数据库的记录，并不会对应的文件

当我们配置了第三方去储存插件时，附件管理中的添加将出现`上传到第三方`的按钮，此时我们的上传就是上传到第三方存储。

#### 插件管理

插件管理是FastAdmin的插件的控制面板，在插件管理中可以在线免费或付费购买安装FastAdmin插件商店中的插件，也可以在插件管理中安装本地插件、配置、禁用、启用、卸载、升级插件。

在插件管理中购买付费插件时会提醒是否登录FastAdmin官网，此时如果登录的情况下，所有插件购买的记录都将保存在此账号下，如果不登录直接购买将会是以你当时的IP地址作为购买依据。

如果我们安装完插件是需要启用、刷新插件缓存、清除后台缓存才会生效。部分插件是没有后台管理菜单或前台访问页面的。

## 公共

### Token验证

#### 功能介绍

Token验证主要用于会员登录状态信息的维护和验证，通常情况下不需要我们调用此类的方法，在一些特殊情况下我们可以手动调用。

#### 使用示例

##### 获取Token信息

获取Token的详情、关联的会员ID、过期时间、有效期等信息

```php
Token::get('c2259a37-5fee-4c4b-93b0-1d7313e1d1ac');
```

##### 设置会员的Token信息

新增会员Token并更新，且有效期为3600秒

```php
Token::set('c2259a37-5fee-4c4b-93b0-1d7313e1d1ac', 1, 3600);
```

##### 判断会员Token是否可用

通过Token和会员ID来判断Token是否可以使用

```
Token::check('c2259a37-5fee-4c4b-93b0-1d7313e1d1ac', 1);
```

##### 删除单个会员Token

删除指定的Token

```php
Token::delete('c2259a37-5fee-4c4b-93b0-1d7313e1d1ac');
```

##### 删除指定会员的所有Token

删除会员ID为1的所有Token

```php
Token::clear(1);
```



### 邮件发送

#### 功能介绍

FastAdmin中的邮件发送采用phpmailer进行邮件发送，在使用邮件发送功能前请先在后台常规管理->系统配置中配置好邮件的相关信息。

#### 使用示例

首先我们需要采用单例或实例化一个Email对象

```php
$email = new \app\common\library\Email;
```

其次我们可以设置邮件主题正文、接收者、标题等信息，比如

```php
$email->subject('这里是邮件标题')->to('youremail@163.com')->message('这里是邮件正文')->send();
```

如果我们邮件发送失败，想获取错误的详情，可使用

```php
$email->getError();
```

来获取到错误详情





### 短信发送

#### 功能介绍

在我们开发过程中经常会用到短信发送和短信推广的功能，FastAdmin提供了一个简单实用的短信接口供开发者调用。

在使用短信发送之前，务必在后台安装好我们短信服务商的插件，如果我们要使用的服务商未提供有FastAdmin的插件，我们则需要自己开发一个，或注册一个`sms_send`和`sms_check`的事件用于我们的发送和检测操作。

#### 使用示例

首先最常用的是发送短信，比如我们发送一个注册验证码

```php
Sms::send('13888888888', '1234', 'register');
```

发送以后我们有时需要检测验证码是否正确，则可以使用

```php
Sms::check('1388888888', '1234', 'register');
```

当然有些时候我们还需要发送营销短信或通知，则可以使用

```php
Sms::notice('1388888888', '消息内容', 'SMS_10001');
```

如果我们需要清空指定手机号的验证码，则可以使用

```php
Sms::flush('13888888888', 'register');
```



### 辅助函数

在FastAdmin中我们有提供几个常用的辅助函数。

```php

/**
 * 获取语言变量值
 * @param string    $name 语言变量名
 * @param array     $vars 动态变量值
 * @param string    $lang 语言
 * @return mixed
 */
function __($name, $vars = [], $lang = '')
{
}

/**
 * 将字节转换为可读文本
 * @param int $size 大小
 * @param string $delimiter 分隔符
 * @return string
 */
function format_bytes($size, $delimiter = '')
{
}

/**
 * 将时间戳转换为日期时间
 * @param int $time 时间戳
 * @param string $format 日期时间格式
 * @return string
 */
function datetime($time, $format = 'Y-m-d H:i:s')
{
}

/**
 * 获取语义化时间
 * @param int $time 时间
 * @param int $local 本地时间
 * @return string
 */
function human_date($time, $local = null)
{
}

/**
 * 获取上传资源的CDN的地址
 * @param string $url 资源相对地址
 * @return string
 */
function cdnurl($url)
{
}

/**
 * 判断文件或文件夹是否可写
 * @param   string $file 文件或目录
 * @return  bool
 */
function is_really_writable($file)
{
}
/**
 * 删除文件夹
 * @param string $dirname 目录
 * @param bool $withself 是否删除自身
 * @return boolean
 */
function rmdirs($dirname, $withself = true)
{
}

/**
 * 复制文件夹
 * @param string $source 源文件夹
 * @param string $dest 目标文件夹
 */
function copydirs($source, $dest)
{
}

function mb_ucfirst($string)
{
}

/**
 * 附加关联字段数据
 * @param array $items 数据列表
 * @param mixed $fields 渲染的来源字段
 * @return array
 */
function addtion($items, $fields)
{
}

/**
 * 返回打印数组结构
 * @param string $var   数组
 * @param string $indent 缩进字符
 * @return string
 */
function var_export_short($var, $indent = "")
{
}
```



