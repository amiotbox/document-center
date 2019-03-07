---
title: 架构
type: docs
order: 5
---

## 架构总览

FastAdmin基于MVC的设计模式，将我们的应用分为三层（模型M、视图V、控制器C）。

## 目录结构

FastAdmin目录结构遵循ThinkPHP5官方建议的模块设计：

~~~ javascript
FastAdmin项目目录
├── addons 			     //插件存放目录
├── application           //应用目录
│   ├── admin             //后台管理应用模块
│   ├── api               //API应用模块
│   ├── common             //通用应用模块
│   ├── extra             //扩展配置目录
│   ├── index             //前台应用模块
│   ├── build.php
│   ├── command.php        //命令行配置
│   ├── common.php         //通用辅助函数
│   ├── config.php         //基础配置
│   ├── database.php       //数据库配置
│   ├── route.php          //路由配置
│   ├── tags.php           //行为配置
├── extend
│   └── fast               //FastAdmin扩展辅助类目录
├── public
│   ├── assets
│   │   ├── build			//打包JS、CSS的资源目录
│   │   ├── css				//CSS样式目录
│   │   ├── fonts			//字体目录
│   │   ├── img
│   │   ├── js
│   │   │   ├── backend
│   │   │   └── frontend     //后台功能模块JS文件存放目录
│   │   ├── libs			//Bower资源包位置
│   │   └── less			//Less资源目录
│   └── uploads				//上传文件目录
│   ├── index.php            //应用入口主文件
│   ├── install.php          //FastAdmin安装引导
│   ├── admin.php            //后台入口文件,强烈建议修改
│   ├── robots.txt
│   └── router.php
├── runtime				    //缓存目录	
├── thinkphp				//ThinkPHP5框架核心目录
├── vendor				    //Compposer资源包位置
├── .bowerrc				//Bower目录配置文件
├── LICENSE
├── README.md
├── bower.json				//Bower前端包配置
├── build.php					
├── composer.json			//Composer包配置
└── think
~~~
## 应用模块

在FastAdmin中默认有四个应用模块：`admin`、`api`、`common`、`index`，你也可以扩展开发自己的应用模块。

后台模块(admin)是FastAdmin中的核心模块，后台模块又分为`系统配置`、`附件管理`、`分类管理`、`插件管理`等多个功能模块，更多的功能模块可以在插件管理中自由的安装和卸载。

后台的前端是基于`AdminLTE`和`Bootstrap`进行了大量二次开发，采用`RequireJS`进行JS模块化管理和加载。

前台模块(index)的结构和后台功能类似，具体请参考`后台模块`的章节

公共模块(common)是一个特殊的模块，默认是禁止直接访问的，一般用于放置一些公共的类或其它模块的继承基类等。

Api模块(api)通常用于对接APP，用于向APP提供接口，目前FastAdmin暂未提供API相关的插件和文档，你可以直接参考ThinkPHP5官方的文档。

## 功能模块

功能模块指后台管理中的功能模块，比如我们的`系统配置`、`附件管理`、`分类管理`。

后台开发的每一个功能模块都是基于`MVC`的设计模式进行开发 。在FastAdmin中，我们提供了一键生成CRUD的功能，这个一键生成CRUD生成的文件也就是我们标准的MVC文件。

以下是一个标准的功能模块所涉及到的文件

```
├── application
│   └── admin
│       ├── controller
│       │   └── Test.php	    //控制器类
│       ├── lang
│       │   ├── zh-cn		    
│       │   │   └── test.php    //功能语言包,按需加载
│       │   └── zh-cn.php		//后台语言包,默认加载
│       ├── model
│       │   └── Test.php	    //模型类
│       ├── validate
│       │   └── Test.php	    //验证器类
│       └── view
│           └── test			
│               ├── index.html   //列表视图
│               ├── add.html     //添加视图
│               └── edit.html    //编辑视图
└── public
    └── assets
        └── js
            └── backend
                └── test.js      //功能模块JS文件
```

在FastAdmin中每一个功能模块至少对应一个功能模块JS文件，也就是说每一个控制器都对应一个同名的JS文件，其次每一个控制器的方法对应JS文件中同名的方法。

具体控制器详细介绍可参考`控制器`章节，JS的部分可以参考`前端`章节。
