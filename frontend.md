---
title: 前端
type: docs
order: 10
---

## 基础介绍

FastAdmin的前端部分使用或涉及到主要是`RequireJS`,`jQuery`,`AdminLTE`,`Bower`,`Less`,`CSS`，其中

`RequireJS`主要是用于JS的模块化加载

`Bower`主要用于管理第三方插件。

`Less`主要是用于我们编写LESS和编译成CSS代码

在阅读接下来的文档之前最好先简单的了解下`RequireJS`和`Bower`，而`jQuery`是我们必须要掌握的工具库

FastAdmin中前端的最常用的第三方插件有`Layer`,`Toastr`，`Layer`用于弹窗，`Toastr`用于提示。

## 目录结构

```
public
├── assets
│   ├── addons
│   ├── css
│   ├── fonts
│   ├── img
│   ├── js
│   │   ├── backend
│   │   ├── frontend
│   ├── less
│   └── libs
```

`assets`主要存在我们框架所需要使用到的静态资源

`assets/js/backend`主要存在后台控制器所对应的JS模块

`assets/libs`主要存在第三方的插件

`assets/less`主要存在Less文件

`assets/img`主要存在图片相关文件

`assets/css`主要存在CSS样式相关文件

`assets/addons`主要存放插件的相关静态资源

## 标准模块

在控制器章节我们有提到每个控制器都对应一个JS模块，控制器名称和JS中模块名称是一一对应的。

比如我们的控制器是`application/admin/controller/Article.php`，则我们JS中对应的JS模块是`public/assets/js/backend/article.js`

如果我们的控制器是`application/admin/controller/Member/Teacher.php`，则我们JS中对应的JS模块是`public/assets/js/backend/member/teacher.js`

每一个控制器请求的方法对应JS模块中一个方法

比如控制器`Article.php`中的`index`方法，对应的是JS的article模块中的`Controller.index`方法，如果我们添加了自己的方法`detail`方法，则对应`Controller.detail`方法。

一个JS标准控制器模块的写法如下：

```js
define(['jquery', 'bootstrap', 'backend', 'table', 'form'], function ($, undefined, Backend, Table, Form) {

    var Controller = {
        index: function () {
            // 初始化表格参数配置
            Table.api.init({
                extend: {
                    index_url: 'category/index',
                    add_url: 'category/add',
                    edit_url: 'category/edit',
                    del_url: 'category/del',
                    multi_url: 'category/multi',
                    dragsort_url: '',
                    table: 'category',
                }
            });

            var table = $("#table");

            // 初始化表格
            table.bootstrapTable({
                url: $.fn.bootstrapTable.defaults.extend.index_url,
                escape: false,
                pk: 'id',
                sortName: 'weigh',
                pagination: false,
                commonSearch: false,
                columns: [
                    [
                        {checkbox: true},
                        {field: 'id', title: __('Id')},
                        {field: 'type', title: __('Type')},
                        {field: 'name', title: __('Name'), align: 'left'},
                        {field: 'nickname', title: __('Nickname')},
                        {field: 'flag', title: __('Flag'), operate: false, formatter: Table.api.formatter.flag},
                        {field: 'image', title: __('Image'), operate: false, formatter: Table.api.formatter.image},
                        {field: 'weigh', title: __('Weigh')},
                        {field: 'status', title: __('Status'), operate: false, formatter: Table.api.formatter.status},
                        {field: 'operate', title: __('Operate'), table: table, events: Table.api.events.operate, formatter: Table.api.formatter.operate}
                    ]
                ]
            });

            // 为表格绑定事件
            Table.api.bindevent(table);
        },
        add: function () {
            Controller.api.bindevent();
        },
        edit: function () {
            Controller.api.bindevent();
        },
        api: {
            bindevent: function () {
                Form.api.bindevent($("form[role=form]"));
            }
        }
    };
    return Controller;
});
```

我们可以看到该模块第一行为`RequireJS`标准语法，意思是此模块依赖`'jquery', 'bootstrap', 'backend', 'table', 'form'`几个模块，同时将它们加载为`$, undefined, Backend, Table, Form`这几个对应，我们就可以放心大胆在其中使用这几个对象了。

其中有定义一个`Controller` 对象，它有`index/add/edit/api`四个方法，这四个方法分别与我们控制器中的方法一一对应。

## 工具模块

在FastAdmin中我们封装了非常多实用的模块类便于我们使用。以下做详细介绍

### 表单(Form)

表单模块主要用于框架表单组件元素的渲染和事件绑定，当我们自定了一个表单后，必须使用表单模块中`Form.api.bindevent`进行绑定表单，否则不会有任何作用。

#### 引入方式

```js
require(['form'], function(Form){});
```

#### 内部方法

```js
//为表单绑定事件，将自动绑定上传/富文本/下拉框/selectpage/表单验证等功能，FastAdmin中最常用的方法，
Form.api.bindevent(form, success, error, submit);

//表单自定义事件存储对象
Form.api.custom

//提交表单的方法，在表单完成验证后进行提交
Form.api.submit(form, success, error, submit);

//以下事件为具体第三方插件实现的方法，可以在调用`Form.api.bindevent`之前修改对应的方法来修改相应功能
Form.events.bindevent(form)
Form.events.citypicker(form)
Form.events.cxselect(form)
Form.events.datetimepicker(form)
Form.events.faselect(form)
Form.events.fieldlist(form)
Form.events.plupload(form)
Form.events.selectpage(form)
Form.events.selectpicker(form)
Form.events.validator(form, success, error, submit)
```

#### 使用示例

```js
Form.api.bindevent($("form[role=form]"), function(data, ret){
    //如果我们需要在提交表单成功后做跳转，可以在此使用location.href="链接";进行跳转
	Toastr.success("成功");
}, function(data, ret){
  	Toastr.success("失败");
}, function(success, error){
    //bindevent的第三个参数为提交前的回调
    //如果我们需要在表单提交前做一些数据处理，则可以在此方法处理
    //注意如果我们需要阻止表单，可以在此使用return false;即可
    //如果我们处理完成需要再次提交表单则可以使用submit提交,如下
    //Form.api.submit(this, success, error);
    return false;
});
```

以上代码表格当表单提交处理成功后提示成功，处理失败提示失败

### 上传(Upload)

上传模块主要用于框架JS端的上传逻辑，默认采用`plupload`进行上传，也可以采用Ajax的方法进行上传，`plupload`的方法上传支持上传进度展示。

#### 引入方式

```js
require(['upload'], function(Upload){});
```

#### 内部方法

```js
//为表单中的上传按钮绑定plupload上传事件
Upload.api.plupload(element, onUploadSuccess, onUploadError);

//上传自定义事件存储对象
Upload.api.custom

//采用Ajax的方法进行上传文件
Upload.api.send(file, onUploadSuccess, onUploadError);

//plupload上传组件配置
Upload.config.classname
Upload.config.container
Upload.config.previewtpl

//upload上传事件配置
Upload.event.onBeforeUpload(up, file);
Upload.event.onFileAdded(up, files);
Upload.event.onPostInit(up);
Upload.event.onUploadError(ret, onUploadError, button);
Upload.event.onUploadResponse(response);
Upload.event.onUploadSuccess(ret, onUploadSuccess, button);
```

#### 使用示例

```js
//使用Plupload上传
Upload.api.plupload($(".plupload"), function(data, ret){
	Toastr.success("成功");
}, function(data, ret){
  	Toastr.success("失败");
});
//使用Ajax上传
Upload.api.send(file, function(data, ret){
	Toastr.success("成功");
}, function(data, ret){
  	Toastr.success("失败");
});
```

以上代码表格当表单提交处理成功后提示成功，处理失败提示失败

### 表格(Table)

表格模块主要用于渲染数据列表，对数据列表进行排序、过滤、筛选、绑定事件、增删改查等等操作。我们在CRUD一键生成后看到的列表就是使用表格模块进行完成的。

#### 引入方式

```js
require(['table'], function(Table){});
```

#### 内部方法

```js
//为渲染的表格绑定上增删改查等事件
Table.api.bindevent(table);
//生成按钮链接
Table.api.buttonlink(column, buttons, value, row, index, type);
//表格格式化对象集，包含多个格式化数据的方法
Table.api.formatter
//根据行获取行数据
Table.api.getrowdata(table, index)
//初始化表格配置
Table.api.init(defaults, columnDefaults, locales)
//表格指量操作的方法
Table.api.multi
//替换URL链接中的{变量名}
Table.api.replaceurl
//获取选中的ID
Table.api.selectedids

//Bootstrap-table表格默认列的配置
Table.columnDefaults
//表格相关DOM元素类配置
Table.config
//Bootstrap-table默认config配置
Table.defaults
//已经渲染的表格对象
Table.list
```

#### 使用示例

```js
// 初始化表格参数配置
Table.api.init({
	extend: {
		index_url: 'category/index',
		add_url: 'category/add',
		edit_url: 'category/edit',
		del_url: 'category/del',
		multi_url: 'category/multi',
		dragsort_url: '',
		table: 'category',
	}
});

var table = $("#table");

// 初始化表格
table.bootstrapTable({
	url: $.fn.bootstrapTable.defaults.extend.index_url,
	escape: false,
	pk: 'id',
	sortName: 'weigh',
	pagination: false,
	commonSearch: false,
	columns: [
		[
			{checkbox: true},
			{field: 'id', title: __('Id')},
			{field: 'type', title: __('Type')},
			{field: 'name', title: __('Name'), align: 'left'},
			{field: 'nickname', title: __('Nickname')},
			{field: 'flag', title: __('Flag'), operate: false, formatter: Table.api.formatter.flag},
			{field: 'image', title: __('Image'), operate: false, formatter: Table.api.formatter.image},
			{field: 'weigh', title: __('Weigh')},
			{field: 'status', title: __('Status'), operate: false, formatter: Table.api.formatter.status},
			{field: 'operate', title: __('Operate'), table: table, events: Table.api.events.operate, formatter: Table.api.formatter.operate}
		]
	]
});

// 为表格绑定事件
Table.api.bindevent(table);
```

表格的详细介绍可以查看一张图解表格：https://forum.fastadmin.net/d/323

