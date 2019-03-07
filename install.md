---
title: 安装
type: docs
order: 2
---

## **环境要求**

~~~
PHP >= 5.5.0 (推荐PHP7.1版本)
Mysql >= 5.5.0 (需支持innodb引擎)
Apache 或 Nginx
PDO PHP Extension
MBstring PHP Extension
CURL PHP Extension
Node.js (可选,用于安装Bower和LESS,同时打包压缩也需要使用到)
Composer (可选,用于管理第三方扩展包)
Bower (可选,用于管理前端资源)
Less (可选,用于编辑less文件,如果你需要增改css样式,最好安装上)
~~~

## **完整包安装**

1. 前往官网下载页面([https://www.fastadmin.net/download.html](https://www.fastadmin.net/download.html?ref=docs))下载完整包解压到你的项目目录
2. 添加虚拟主机并绑定到项目中的`public`目录
3. 访问 http://www.yoursite.com/install.php 进行安装

## **命令行安装**

强烈建议使用命令行安装，因为采用命令行安装的方式可以和FastAdmin随时保持更新同步。使用命令行安装请提前准备好Git、Node.js、Composer、Bower环境，我们为Windows下开发者准备了一个简单的视频安装教程( https://www.fastadmin.net/video/install.html )，可跟着教程一步一步安装。Linux下FastAdmin的安装请使用以下命令进行安装。

1. 克隆FastAdmin到你本地
   `git clone https://gitee.com/karson/fastadmin.git `
2. 进入目录
   `cd fastadmin `
3. 下载前端插件依赖包
   `bower install `
4. 下载PHP依赖包
   `composer install`
5. 一键创建数据库并导入数据
   `php think install -u 数据库用户名 -p 数据库密码`
6. 添加虚拟主机并绑定到`fastadmin/public`目录

## **常见问题**
1. 如果使用`命令行安装`则后台管理默认密码是`123456`
2. 提示`请先下载完整包覆盖后再安装`，说明你是直接从仓库下载的代码，请从官网下载完整包覆盖后再进行安装
3. 执行`php think install`时出现`Access denied for user ...`，请确保数据库服务器、用户名、密码配置正确
4. 执行`php think install`时报不是内部或外部命令? 请将`php.exe`所在的目录路径加入到环境变量PATH中
5. 如果提示`当前权限不足，无法写入配置文件application/database.php`，请检查`database.php`是否可读，还有可能是当前安装程序无法访问父目录，请检查PHP的`open_basedir`配置
6. 如果提示`找不到fastadmin.fa_admin`表或表不存在，请检查你的MySQL是否开启了支持`innodb`。
7. 如果在Linux环境中使用的是root账户，`bower install`执行出错，请尝试添加上`--allow-root`参数
8. 如果访问后台右侧空白，请检查资源是否下载完整，可使用`bower install`多试两次或下载资源包覆盖
9. 如果`composer install`失败，请尝试在命令行进行切换配置到国内源，命令如下`composer config -g repo.packagist composer https://packagist.laravel-china.org`




遇到问题到[社区](https://forum.fastadmin.net) 或QQ群：[636393962](https://jq.qq.com/?_wv=1027&k=487PNBb) 反馈




