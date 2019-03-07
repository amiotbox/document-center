---
title: 常见问题
type: docs
order: 12
---

如果你在使用FastAdmin的过程中发现任何问题,请到交流社区发贴: https://forum.fastadmin.net

## 在FastAdmin中如何开启调试模式？

开启调试模式的方法有两种：

1、打开`application/config.php`，找到`app_debug`,将它的值置为`true`即可

2、如果你有启用`.env`环境配置，修改其中`app_debug`的值为`true`即可。

## 为什么在调试模式下功能正常，但在生产环境下功能失效

如果你有修改了框架核心的JS文件或者修改了核心样式文件，需要使用命令重新压缩打包JS和CSS后才会在生产环境下生效，具体请参考：https://doc.fastadmin.net/docs/command.html#一键压缩打包-3 

## 为什么在外网访问后台速度非常慢

如果你在外网开启了调试模式，因为在调试模式下加载的文件非常多，访问速度会非常慢，请在外网或生产环境下关闭调试模式。如果有更高的响应要求，建议采用CDN部署静态资源。部署方式请参考：[#如何将静态资源采用CDN方式部署到第三方云存储-13](#如何将静态资源采用CDN方式部署到第三方云存储-13) 

## 如何启用二级菜单功能

FastAdmin从`1.0.0.20180513_beta`版本开始新增了二级菜单功能，开发者可以很方便的在配置文件中修改是否开启二级菜单功能，找到`application/config.php`文件最下方有个`multiplenav`配置，默认是`false`，如果需要启用，请将`multiplenav`置为`true`即可。

如果启用了二级菜单后，菜单规则中的第一级将作为一级菜单显示在顶部。我们可以在`权限管理`->`菜单规则`中额外添加一级菜单，然后再重新规划我们的菜单。

## 如何启用后台登录验证码功能

FastAdmin中的后台登录验证功能默认是关闭的，如果需要启用请修改`application/config.php`文件最下方的`login_captcha`，修改为`true`即可。

## 如何在生成CRUD时生成回收站功能

FastAdmin从`1.0.0.20190301_beta`版本开始已经支持自动生成回收站功能，如果需要生成回收站功能，请确保你的FastAdmin版本，其次确保你的数据表中存在`deletetime`这个字段，只需保证存在此字段，CRUD在生成时会自动生成回收站相关的前后端代码和文件。

## 如何在控制器或模型中获取当前登录的管理员或登录用户信息

在FastAdmin所提供的基类`Frontend`、`Backend`、`Api`中都有提供获取当前管理员或登录用户的信息方法

在后台管理的控制器中可以通过`$this->auth->id`来获取管理员ID，`$this->auth->字段名`获取管理员的其它信息，如果需要在后台的Model中获取当前登录的管理员ID，可以通过两种方式获取：

第一种，使用Session来获取管理员信息

```php
\think\Session::get('admin');
```

第二种，使用单例方法来获取

```php
$auth = \app\admin\library\Auth::instance();
```

在前台或API的控制器中可以通过`$this->auth->id`来获取当前登录会员的ID，`$this->auth->字段员`获取登录会员的其它信息，如果需要在前台的Model中获取当前登录的用户信息，可以能和以下的方式获取：

```php
//获取Auth对象
$auth = \app\common\library\Auth::install();
//获取会员模型
$user = $auth->getUser();
```



## 安装后提示控制器不存在:E或控制器不存在:N

出现这种情况一般是由于Web服务器的PATH_INFO未配置正确，导致服务器接收到了错误的PATH_INFO值，请检查你的PATH_INFO并修复后再重试

## FastAdmin的数据库SQL文件在哪里

FastAdmin在安装时会自动创建数据库和数据表,免除了你手动创建数据库和导入数据库的烦恼。
但很多时候我们需要构造自己的安装SQL，这就需要修改安装SQL文件。
FastAdmin的数据库安装文件保存在 [application/admin/command/Install/fastadmin.sql](https://gitee.com/karson/fastadmin/raw/master/application/admin/command/Install/fastadmin.sql)

## 如何修改后台默认皮肤

为了进一步提升加载速度，后台默认启用了绿色主题的皮肤，如何修改其它皮肤呢？

1. 找到`/public/assets/css/backend.css` 这个文件，默认是：

``` css
@import url("../css/bootstrap.css");
@import url("../css/fastadmin.css");
@import url("../css/skins/skin-green.css");
@import url("../css/iconfont.css");
@import url("../libs/font-awesome/css/font-awesome.min.css");
@import url("../libs/toastr/toastr.min.css");
@import url("../libs/fastadmin-layer/dist/theme/default/layer.css");
@import url("../libs/bootstrap-table/dist/bootstrap-table.min.css");
@import url("../libs/eonasdan-bootstrap-datetimepicker/build/css/bootstrap-datetimepicker.min.css");
@import url("../libs/bootstrap-daterangepicker/daterangepicker.css");
@import url("../libs/nice-validator/dist/jquery.validator.css");
@import url("../libs/bootstrap-select/dist/css/bootstrap-select.min.css");
@import url("../libs/fastadmin-selectpage/selectpage.css");
```

2. 其中可以看到只加载了`skin-green.css`这个皮肤，如果需要启用其它皮肤可以在文件末尾追加

``` css
@import url("../css/skins/skin-颜色标识.css");
```
`颜色标识`总共有 `black/black-light/blue/blue-light/green/green-light/purple/purple-light/red/red-light/yellow/yellow-light`总12个颜色标识
如果需要一次性加载全部的皮肤样式，则把`skin-green`改为`_all-skins`即可

3. 最后修改`application/admin/views/index/index.html`中第6行，将`skin-green`换成你需要的颜色标识
4. 注意生产环境还需要执行`php think min -m all -r all`才会生效

## 为什么前台自己创建的控制器提示你没有权限访问

这是由于控制器的基类你肯定继承的是`Frontend`，`Frontend`基类有鉴权判断，有以下两种办法可以尝试

1. 添加控制器的属性`$noNeedRight=["*"]`，表示无需再做鉴权
2. 后台会员规则中添加相应的规则，再给对应的会员组赋予相应的权限即可

## php think install报不是内部或外部命令

这是由于php.exe文件所在目录未加入到PATH环境变量导致的

找到`php.exe`文件所在的目录，将该目录加入到系统PATH环境变量中后，重启CMD即可解决


## php think install报command not found

这是由于在Linux环境下未找到php的脚本程序

有两种解决办法，首先尝试使用`which php`找到php所在的位置。
1. 找到php脚本程序所在的目录，加入到PATH环境变量中去，使用`export PATH=$PATH:php脚本程序所在目录`
2. 找到php脚本程序文件，使用`ln -s php脚本程序文件 /usr/bin/php`

## 安装后访问后台和会员中心都是显示首页

这是由于伪静态没有生效或错误导致的。请参考下方的Apache和Nginx伪静态配置

## 安装后只能访问首页，其它页均报no input file specified

这是由于伪静态没有生效或错误导致的。

这种情况一般在Apache下伪静态不工作的情况下出现，
首先确保已经启用Apache的伪静态，确保目录已经配置好权限，如下面的Directory配置
``` ini
<VirtualHost *:80>
    DocumentRoot "/Users/Karson/Project/fastadmin/public"
    ServerName fa.com 
    ServerAlias fa.com *.fa.com
    <Directory "/Users/Karson/Project/fastadmin">
        AllowOverride All
        Options Indexes FollowSymLinks
        Require all granted
    </Directory>
</VirtualHost>
```

其次伪静态规则在Apache fastcgi模式下会导致No input file specified.
请修改public目录下的.htaccess文件

默认的

``` apache
RewriteRule ^(.*)$ index.php/$1 [QSA,PT,L]
```

修改成
``` apache
RewriteRule ^(.*)$ index.php [L,E=PATH_INFO:$1]
```


## 安装后只能访问首页，其它页均报404 page not found

这是由于伪静态未配置或没有生效导致的。

这种情况一般在Nginx下未配置伪静态的情况下出现，建议将虚拟主机的root绑定至public目录

例如：

```ini
server {
        listen       80;
        server_name  www.fa.com *.fa.com;
        root   "C:/phpstudy/WWW/fastadmin/public";
        location / {
            index  index.html index.htm index.php;
            	#主要是这一段一定要确保存在
                if (!-e $request_filename) {
                    rewrite  ^(.*)$  /index.php?s=/$1  last;
                    break;
                }
                #结束
            #autoindex  on;
        }
        location ~ \.php(.*)$ {
            fastcgi_pass   127.0.0.1:9000;
            fastcgi_index  index.php;
            fastcgi_split_path_info  ^((?U).+\.php)(/?.+)$;
            fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
            fastcgi_param  PATH_INFO  $fastcgi_path_info;
            fastcgi_param  PATH_TRANSLATED  $document_root$fastcgi_path_info;
            include        fastcgi_params;
        }
}
```

请将`C:/phpstudy/WWW`改成你服务器对应所在的目录

如果你使用的是 lnmp.org 的一键安装LNMP环境，
请查阅 https://lnmp.org/faq/lnmp-vhost-add-howto.html#rewrite 的伪静态配置

## composer install时无法下载package

这是由于composer默认配置是国外的源，如遇网络故障则会导致无法下载

1. 执行命令前先开启代理
2. 使用国内的镜像源，有以下两种启用方法

**#### 方法一： 修改 composer 的全局配置文件（推荐方式）**

打开命令行窗口并执行如下命令：

```
composer config -g repo.packagist composer https://packagist.laravel-china.org
```

**#### 方法二： 修改当前项目的 composer.json 配置文件：**

打开命令行窗口，进入你的项目的根目录（也就是 composer.json 文件所在目录），执行如下命令：

```
composer config repo.packagist composer https://packagist.laravel-china.org
```

感谢：https://packagist.laravel-china.org


## 如何修改或禁用左侧菜单栏的角标

FastAdmin后台左侧菜单栏有彩色的小角标，这一般用于通知和提醒操作，在后台开发时是非常方便的一个小功能，如何修改和禁用它呢？
找到`/application/admin/controller/Index.php`中的index方法，其中有一段

```php
$menulist = $this->auth->getSidebar([
	'dashboard'  => 'hot',
	'auth'       => ['new', 'red', 'badge'],
	'auth/admin' => 12,
	'auth/rule'  => 4,
	'general'    => ['18', 'purple'],
]);
```

数组的键名是对应的左侧菜单栏的相对链接
数组的键值是需要显示的文字或数字，可以传字符串或数组

1. 如果是字符串，则角标的颜色是按照`'red', 'green', 'yellow', 'blue', 'teal', 'orange', 'purple'`的方式进行循环的。
2. 如果是数组，这三个值分别表示：[显示的文字, 颜色，展现方式(badge或label)]

如果需要删除这个小角标，则可以直接到数组置为空即可

在这里仅仅是PHP端操作小角标的方式，在JS端同样可以进行相应的操作
在你的模块中可以调用

```js
top.window.Backend.api.sidebar({
	'auth/admin':44
});
```

具体使用方法同PHP端相同
如何动态的在JS中移除一个小角标呢，采用以下的方法即可

```js
top.window.Backend.api.sidebar({
	'auth/admin':0
});
```

## 后台上传文件时提示HTTP Error.(code:-200)错误

在后台上传文件时如果遇到-200错误，首先请开启调试模式，然后请使用谷歌浏览器的开发者工具，按F12，切换到`Network`，看具体的错误信息。

## 在Windows下如何压缩打包JS和CSS

在FastAdmin中压缩打包JS和CSS文件需要NodeJS的支持
在Windows下需要手动配置Node的可执行文件,请修改`application/admin/command/Min.php`中`$nodeExec`的值
如你的Node可执行文件是`C:/Program Files/nodejs/node.exe`，则请配置`$nodeExec = '"C:/Program Files/nodejs/node.exe"'`;



## 提示你所浏览的页面暂时无法访问

如果我们在FastAdmin开发过程中遇到此错误，说明我们`application/config.php`中的`app_debug`是关闭的，必须开启`app_debug`为`true`才可以显示出详细的错误信息。如果开启`app_debug`仍然显示不出详细错误，请确保`php.ini`中的`display_error`为开启状态。




## 提示未知的数据格式或网络错误

很多时候都有可能遇到提示未知的数据格式或网络错误这个提示，产生这个错误的原因一般来说都是服务端报错，导致返回的数据不是JSON格式或直接未返回，如下图

![](https://cdn.forum.fastadmin.net/uploads/201706/02/0f650de53f0ee93ddfd658f731027d43)

准备工作：首先确保你的FastAdmin开启了调试模式`application/config.php`中的`app_debug`置为`true`
两种定位错误的方法：

1. 使用Chrome浏览器，打开开发者工具，选中`Network(网络)`选项卡,刷新一下页面或重新请求一次，定位到我们请求的URL，点击然后在`Preview`即可看到错误信息
2. 直接查看`runtime/log`目录下的错误日志

修复错误后再重试即可

FastAdmin建议运行在PHP5.5及以上版本，因此如果提示网络错误请检查你的PHP是否低于该版本

## 安装时提示无法写入application/database.php

造成此问题的原因通常有几下两种情况

1. `application/database.php`文件没有写入权限，请先查看你的apache或nginx的运行用户，然后修改文件的所有者，再添加上写入的权限，通常情况下需要执行以下命令：

   ```
   cd /yourpath/fastadmin/
   chown www-data:www-data ./ -R
   chmod a+w ./application/database.php
   chmod a+w ./runtime/ -R
   ```

2. 其次就是可能你的目录绑定到public后，`open_basedir`限制了访问父目录，可以配置下`open_basedir`，添加上级目录即可，通常情况下Linux需要在配置文件中追加的代码是：

   ```
   fastcgi_param PHP_ADMIN_VALUE "open_basedir=/yourpath/fastadmin/:/tmp/:/proc/";
   ```

   如果使用的是lnmp一键安装包，可能需要修改`.user.ini`文件，在`.user.ini`文件中添加

   ```
   open_basedir=/yourpath/fastadmin/:/tmp/:/proc/
   ```

3. 如果使用的是`宝塔面板`，请直接在`网站`->`配置`->`网站目录`中修改`运行目录`为`public`目录
​

## 如何将静态资源采用CDN方式部署到第三方云存储

FastAdmin可以将静态的资源部署到又拍云、七牛云或阿里OSS，可大大的加快网站的访问。
默认FastAdmin的静态资源是不采用CDN部署的，如果需要启用，需要修改以下两个文件的配置

1. 修改`application/extra/site.php`中`cdnurl`的值为你CDN的地址
2. 修改`application/config.php`中`__CDN__`的值为你CDN的地址

请将你的静态资源`public/assets`文件夹上传至你的CDN空间中去。

如果采用了静态资源部署CDN，在后台`插件管理`中对插件执行安装、禁用、启用、卸载后都需要将`public/assets/addons/`目录和`public/assets/js/addons.js`文件再次同步更新到CDN。

如果你需要将上传的文件直传至又拍云、七牛云或阿里OSS，请在插件管理中下载对应第三方云存储的插件并配置好即可。