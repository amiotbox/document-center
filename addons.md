---
title: 插件
type: docs
order: 4
---
在FastAdmin中可收自由扩展和开发插件，这里简单整理一个插件文档。在此以[官方博客插件](https://www.fastadmin.net/store/blog.html) 为示例来介绍，因为官方博客插件是前后台都有的一套完整方案。

## 目录结构
```
blog
├── application
│   └── admin
│       ├── controller
│       │   └── blog
│       │       ├── Category.php
│       │       ├── Comment.php
│       │       └── Post.php
│       ├── lang
│       │   └── zh-cn
│       │       └── blog
│       ├── model
│       │   ├── BlogCategory.php
│       │   ├── BlogComment.php
│       │   └── BlogPost.php
│       └── view
│           └── blog
│               ├── category
│               │   ├── add.html
│               │   ├── edit.html
│               │   └── index.html
│               ├── comment
│               │   ├── add.html
│               │   ├── edit.html
│               │   └── index.html
│               └── post
│                   ├── add.html
│                   ├── edit.html
│                   └── index.html
├── assets
│   ├── css
│   │   ├── main.css
│   │   └── main.css.map
│   ├── fonts
│   ├── img
│   ├── js
│   ├── less
│   └── lib
│       ├── jquery.js
│       └── jquery.pjax.js
├── controller
│   ├── Ajax.php
│   └── Index.php
├── lang
│   └── zh-cn.php
├── model
│   ├── Category.php
│   ├── Comment.php
│   └── Post.php
├── public
│   └── assets
│       └── js
│           └── backend
│               └── blog
│                   ├── category.js
│                   ├── comment.js
│                   └── post.js
├── view
│   ├── common
│   │   ├── commentlist.html
│   │   ├── postlist.html
│   │   └── sidebar.html
│   └── index
│       ├── archieve.html
│       ├── category.html
│       ├── index.html
│       └── post.html
├── Blog.php
├── bootstrap.js
├── LICENSE
├── config.php
├── info.ini
└── install.sql
```

## 目录介绍

```php
blog
├── application	//此文件夹中所有文件会覆盖到根目录的/application文件夹
├── assets		//此文件夹中所有文件会复制到/public/assets/addons/blog文件夹
├── controller	//此文件夹为插件控制器目录
├── lang			//此文件夹为插件语言包目录
├── model			//此文件夹为插件模型目录
├── public		//此文件夹中所有文件会覆盖到根目录的/public文件夹
├── view			//此文件夹为插件视图目录
├── Blog.php		//此文件为插件核心安装卸载控制器,必需存在
├── bootstrap.js	//此文件为插件JS启动文件
├── LICENSE		//版权文件
├── config.php	//插件配置文件,我们在后台插件管理中点配置按钮时配置的文件,必需存在
├── info.ini		//插件信息文件,用于保存插件基本信息，插件开启状态等,必需存在
└── install.sql	//插件数据库安装文件,此文件仅在插件安装时会进行导入
```

其中的`application`和`public`文件夹会覆盖到根目录，这两个文件夹主要用于我们后台管理功能的开发，我们可以先在后台开发好对应的管理功能后，再将对应的功能打包进插件即可，FastAdmin在插件安装和卸载时会自动进行文件冲突检测，如果遇到冲突的文件会提醒用户是否进行覆盖或删除。

`assets`这个文件夹很关键，FastAdmin会将`assets`中的所有文件夹和文件复制到`/public/assets/addons/blog/`文件夹中去，这个`blog`即是我们的插件目录名称，`assets`文件夹中的所有文件不会进行文件冲突检测，`/public/assets/addons/blog/`这个目录下的文件，我们在视图文件中可以直接通过`__ADDON__`指向这个路径。因此在开发视图时我们可以先使用相对路径设计，完成后我们再统一加上这个`__ADDON__`的前缀

`controller`、`lang`、`model`和`view`这四个文件夹是我们插件前台功能的MVC部分，这部分文件夹不会复制或移动到其它位置。

`Blog.php`这个文件是插件的核心文件，我们可以在这个文件中编写插件安装或卸载时执行的脚本，或者在此插件中编写菜单的生成或删除，同时插件的行为方法也是编写在此文件中的，插件所支持的行为事件会在后面讲到。此文件命令规则为插件目录名称首字母大写。

`bootstrap.js`这个文件是插件的启动文件，插件在安装完启用后，FastAdmin会将此文件中的内容合并到`/public/assets/js/addons.js`中去，你可以在此编写插件核心JS或注册事件，在此JS中可以使用require依赖其它模块。同时在此插件中可以使用`Fast、Backend、Lang`等全局对象，因为在此之前此类对象已经加载且注册。

`config.php`这个文件是插件的配置文件，我们在后台插件管理中点`配置按钮`时会保存在此文件，此文件的内容格式为：

```php
<?php

return [
    [
        //配置名称,该值在当前数组配置中确保唯一
        'name'    => 'yourname',
        //配置标题
        'title'   => '配置标题',
        //配置类型,支持string/text/number/datetime/array/select/selects/image/images/file/files/checkbox/radio/bool
        'type'    => 'string',
        //配置select/selects/checkbox/radio/bool时显示的列表项
        'content' => [
            '1' => '显示',
            '0' => '不显示'
        ],
        //配置值
        'value'   => '1',
        //配置验证规则,更之规则可参考nice-validator文件
        'rule'    => 'required',
        'msg'     => '验证失败提示文字',
        'tip'     => '字段填写帮助',
        'ok'      => '验证成功提示文字'
    ],
    [
        'name'    => 'yourname2',
        'title'   => '配置标题2',
        'type'    => 'radio',
        'options' => [
            '1' => '显示',
            '0' => '不显示'
        ],
        'value'   => '1',
        'rule'    => 'required',
        'msg'     => '验证失败提示文字',
        'tip'     => '字段填写帮助',
        'ok'      => '验证成功提示文字'
    ]
];
```

`config.php`中的值在FastAdmin任何地方均可使用`get_addon_config('blog')`来获取配置值

`info.ini`这个文件仅用于保存插件基础信息和开启状态，此文件的内容格式为

```ini
name = blog
title = 博客插件
intro = 响应式博客插件，包含日志、评论、分类、归档等
author = Karson
website = https://www.fastadmin.net
version = 1.0.0
state = 1
```

`install.sql` 这个文件中只能是SQL语句，同时在此文件中可以使用`__PREFIX__`表示数据库表前缀，FastAdmin在安装导入SQL时自动进行替换。此文件的内容格式为

```sql
;我们在创建数据库时最好加上IF NOT EXISTS
CREATE TABLE IF NOT EXISTS `__PREFIX__blog_category` (
  `id` int(10) unsigned NOT NULL AUTO_INCREMENT,
  `pid` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '父分类ID',
  `name` varchar(30) NOT NULL DEFAULT '' COMMENT '分类名称',
  `nickname` varchar(50) NOT NULL DEFAULT '' COMMENT '分类昵称',
  `flag` set('hot','index','recommend') NOT NULL DEFAULT '' COMMENT '分类标志',
  `image` varchar(100) NOT NULL DEFAULT '' COMMENT '图片',
  `keywords` varchar(255) NOT NULL DEFAULT '' COMMENT '关键字',
  `description` varchar(255) NOT NULL DEFAULT '' COMMENT '描述',
  `diyname` varchar(30) NOT NULL DEFAULT '' COMMENT '自定义名称',
  `createtime` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '创建时间',
  `updatetime` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '更新时间',
  `weigh` int(10) NOT NULL DEFAULT '0' COMMENT '权重',
  `status` varchar(30) NOT NULL DEFAULT '' COMMENT '状态',
  PRIMARY KEY (`id`),
  KEY `weigh` (`weigh`,`id`),
  KEY `pid` (`pid`)
) ENGINE=InnoDB AUTO_INCREMENT=2 DEFAULT CHARSET=utf8 ROW_FORMAT=DYNAMIC COMMENT='博客分类表';
; 在执行插入时最好加上BEGIN;和COMMIT;
BEGIN;
INSERT INTO `__PREFIX__blog_category` VALUES ('1', '0', '默认分类', 'default', '', '/assets/img/qrcode.png', '', '', '', '1502112587', '1502112587', '0', 'normal');
COMMIT;
```
## 行为事件

FastAdmin中的行为支持ThinkPHP5的所有行为，同时FastAdmin自定义部分专属的行为事件，以下是所有所支持的行为事件

| 标签位                | 描述           | 类型说明      |
| ------------------ | ------------ | --------- |
| app_init           | 应用初始化标签位     | 系统        |
| app_begin          | 应用开始标签位      | 系统        |
| module_init        | 模块初始化标签位     | 系统        |
| action_begin       | 控制器开始标签位     | 系统        |
| view_filter        | 视图输出过滤标签位    | 系统        |
| app_end            | 应用结束标签位      | 系统        |
| log_write          | 日志write方法标签位 | 系统        |
| log_write_done     | 日志写入完成标签位    | 系统        |
| response_end       | 输出结束标签位      | 系统        |
| response_send      | 响应发送标签位      | 系统        |
| upload_after       | 上传成功标签位      | FastAdmin |
| login_init         | 登录标签位        | FastAdmin |
| wipecache_after    | 清除缓存后标签位     | FastAdmin |
| admin_nologin      | 管理员未登录标签位    | FastAdmin |
| admin_nopermission | 管理员无权限标签位    | FastAdmin |
| upload_config_init | 上传配置标签位      | FastAdmin |
| config_init        | 系统配置标签位      | FastAdmin |

使用行为时在`Blog.php`中添加上对应的方法，FastAdmin在安装时、禁用、启用即可自动注册行为。但一定请注意在`Blog.php`中编写行为方法是使用的是驼峰式规则，例如`upload_after`，方法名则为`uploadAfter`，如果方法名使用`upload_after`则不会注册成功

至此，FastAdmin插件开发所涉及到的文件和文件夹已经介绍完了，如果有疑问，请及时在群或论坛反馈。

## 插件示例

我们准备了一篇插件开发简明教程，请[点击这里查看](https://forum.fastadmin.net/d/324)

## 插件管理

FastAdmin插件分为在线安装和命令行安装，在线安装可以直接在后台插件管理进行安装和卸载。

命令行安装适合禁用或移除了后台插件管理功能的开发者使用，具体请参考命令行章节：[一键管理插件](https://doc.fastadmin.net/docs/command.html#一键管理插件)

## 插件市场

目前FastAdmin官方已经上线插件市场，开发者可以在插件市场下载插件进行离线安装，地址：https://www.fastadmin.net/store.html

如果你开发了一款插件需要上架到FastAdmin的插件市场，可以通过在线发布插件的形式分享或售卖你的插件，请点击https://www.fastadmin.net/postaddon.html 查看

