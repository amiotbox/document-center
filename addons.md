---
title: 应用案例
type: docs
order: 4
---
## 轻松构建HTTP接口

如何轻松构建HTTP接口。

首先我们要使用到**海创-IIoT**软件里面的控件有这些：

1、**function** 一个JavaScript函数块，用于针对节点接收的消息运行。

![20190319180824.png](https://i.loli.net/2019/03/19/5c90bfa48c1c5.png)

2、**http** 含两个模块，接收客户端请求模块，和返回客户端信息模块。

![20190319181056.png](https://i.loli.net/2019/03/19/5c90c03669c64.png)
![20190319181110.png](https://i.loli.net/2019/03/19/5c90c0442a086.png)

3、**调试** 可以将结果打印在右侧调试窗口上

![20190319181145.png](https://i.loli.net/2019/03/19/5c90c0669b0f3.png)

接下来是重点，把以上四个控件组合起来就构建好HTTP接口啦。先放出最后的效果图。

![20190319181312.png](https://i.loli.net/2019/03/19/5c90c0be770c5.png)

![20190319181734.png](https://i.loli.net/2019/03/19/5c90c1c478109.png)

接下来讲一下这四个控件如何配置：

**http in** 一个接收客户端请求模块，选择请求方式和搭建的地址。

![20190319182020.png](https://i.loli.net/2019/03/19/5c90c269c84b3.png)

**function** 一个JavaScript函数块，写上你要返回客户端的信息。

![20190319182103.png](https://i.loli.net/2019/03/19/5c90c29593424.png)

**http response** 用于返回客户端信息，按配置界面图链接即可。

**调试** 用于界面调试输出结果。我们需要将上面的程序输出结果打印在界面右侧的调试窗口，按配置界面图链接即可

好了到了这一步基本完成了我们的配置了，最后只要把四个控件用线连接起来点击部署就完成啦，就完成了搭建HTTP接口。接下来你们就可以搭建自己的HTTP服务啦。

![20190319182235.png](https://i.loli.net/2019/03/19/5c90c2f1dc256.png)

下面附上今天分享内容的代码，只需要**导入**就能使用啦。

```
[{"id":"bd0011ec.dab8e","type":"http in","z":"21e8d658.06ce4a","name":"","url":"/http","method":"get","upload":false,"swaggerDoc":"","x":215,"y":260,"wires":[["e7f5e49e.00d578","7d23f1ac.aaf0b"]]},{"id":"e7f5e49e.00d578","type":"function","z":"21e8d658.06ce4a","name":"","func":"msg.payload = {\n  \"code\" : 0,\n  \"msg\" : \"通讯成功\"\n};\nreturn msg;","outputs":1,"noerr":0,"x":375,"y":260,"wires":[["a5b609a9.d9a608"]]},{"id":"7d23f1ac.aaf0b","type":"debug","z":"21e8d658.06ce4a","name":"","active":true,"tosidebar":true,"console":false,"tostatus":false,"complete":"true","x":375,"y":160,"wires":[]},{"id":"a5b609a9.d9a608","type":"http response","z":"21e8d658.06ce4a","name":"","statusCode":"","headers":{},"x":515,"y":260,"wires":[]}]

```
## Function控件技巧

使用控件：

1、function 一个JavaScript函数块，用于流消息处理

![20190319192043.png](https://i.loli.net/2019/03/19/5c90d0914f261.png)
 
使用技巧：
1、在使用function控件时怎么接收流消息？
在function控件里预装了一个变量msg，类型为object。该变量包含了上游节点所有输出信息，主要输出信息包含在msg.payload里面。使用时不用重新定义，即可直接使用例如：

```
var dome=msg.payload;
```

2、如何向下游传递消息？
向下游传递一个objerct对象，可以使用上游传递的流对象，也可以重新创建一个objerct对象(注意：构造新的消息对象会将上游节点携带的所有消息清空，例如HTTP In / Response流需要端到端保留msg.req和 msg.res属性。通常，函数节点应该返回它们传递的消息对象，并对其属性进行了任何更改。)，例如

```
//直接使用上游流对象重新赋值后返回
msg.payload=1;
return msg;

//使用新实例的对象返回
var newMsg = { payload: msg.payload.length };
return newMsg;
```

3、如何不向下游传递流消息？
return null或不return

4、如何输出多个输出？
双击进入属性页面，在“输出”属性中配置需要多个输出的个数，然后再返回对象时将多个对象置于一个数组内返回(可用于多种信息输出，条件判断输出)，例如

```
//条件输出
if (msg.topic === "banana") {
   return [ null, msg ];
} else {
   return [ msg, null ];
}
```

//输出多种类型数据
var newMsg = { payload: msg.payload.length };
return [msg, newMsg];

5、一个节点如何输出多条信息？
某些场景下我们需要向下游输出多条消息，我们将所有消息都包装成对象，并将这些对象置于一个数组内，再将这个数组再置一层数组，如果有多个输出，在外层数组内包裹对应的数组(里面包含多条消息)或者对象(单条消息)，输出消息时会按照数组内顺序，由内到外，由前到后输出。以下示例将将展示完整用例：

界面配置：

![20190319191916.png](https://i.loli.net/2019/03/19/5c90d03aaa54f.png)

Function控件属性：

![20190319191832.png](https://i.loli.net/2019/03/19/5c90d00e9b288.png)

调试输出：

![20190319191737.png](https://i.loli.net/2019/03/19/5c90cfdc305f2.png)

6、如何发送异步消息？
如果需要在异步操作中发送流消息请使用node.send(meg)方法。请确保在函数结束时不能return消息，例如

```
doSomeAsyncWork(msg, function(result) {
    node.send({payload:result});
});
return;
```
如果您在函数中使用了异步回调代码，则有可能在重新部署时处理相应的异步操作，可以通过colse事件处理，例如
node.on('close', function() {
    // 需要处理未完成的请求以及关闭连接等操作
});

7、日志输出
如果节点需要将某些内容记录到控制台，它可以使用以下功能之一：

```
node.log("控制台输出");
node.warn("控制台以及在调试界面输出警告");
node.error("控制台以及在调试界面输出错误");
```

8、错误处理
程序运行发生错误时，我们在错误处理时使用node.error()方法进行报错处理，且程序应当被停止(不进行任何返回消息)，如需被Catch节点捕获错误信息的话，请在第二参数中填写错误提示消息

```
node.error("错误提示消息", msg);
```

9、缓存数据
在一些场景中，我们经常会需要缓存某个数据，然后再本节点或者其他节点中使用。我们可以使用能访问缓存数据的预定义变量：

**context** – 当前节点流范围内使用

**flow** – 当前流程范围内使用

**global** – 全局

示例：

```
//获取流内缓存，其他类型缓存变量使用方法相似
var count = context.get('count')||0;
count += 1;
//缓存数据
context.set('count',count);

msg.count = count;
return msg;
```

也可以一次获取多个值，获取值返回为数组,下标为缓存变量名，任何未设置的值返回都为null
```    
var values = flow.get(["count", "colour", "temperature"]);
flow.set(["count", "colour", "temperature"], [123, "red", "12.5"]);
```
如果上下文存储需要异步访问，则get和set函数需要额外的回调参数，例如
```
context.get('count', function(err, count) {
    if (err) {
        node.error(err, msg);
    } else {
        count = count || 0;
        count += 1;
        context.set('count',count, function(err) {
            if (err) {
                node.error(err, msg);
            } else {
                msg.count = count;
                // send the message
                node.send(msg);
            }
        });
    }
});
```
## 快速使用mysql数据

如何快速使用mysql。

首先我们要使用到**海创-IIoT**软件里面的控件有这些：

1、定时器 用于触发流程，可周期性触发、定义触发内容  

![20190319195006.png](https://i.loli.net/2019/03/19/5c90d776a5b3e.png)

2、function 一个JavaScript函数块，用于针对节点接收的消息运行。

![20190319195033.png](https://i.loli.net/2019/03/19/5c90d78e865ee.png)

3、mysql mysql数据库。

![20190319195057.png](https://i.loli.net/2019/03/19/5c90d7a79c0d7.png)

4、调试 可以将结果打印在右侧调试窗口上

![20190319195119.png](https://i.loli.net/2019/03/19/5c90d7bd0c3bc.png)

接下来是重点，把以上四个控件组合起来就可以使用mysql的数据啦。先放出最后的效果图。

![20190319200123.png](https://i.loli.net/2019/03/19/5c90da21abdd9.png)

接下来讲一下这四个控件如何配置：

**定时器** 用于触发或定时输出数据。这边我们只当做触发器使用，无需配置，使用时点击左侧触发按钮

**function** 一个JavaScript函数块，写上你要对mysql做什么操作的语句。下图是做查询操作的语句。（新增和删除同理）

![20190319200143.png](https://i.loli.net/2019/03/19/5c90da2e112ac.png)

**mysql** mysql数据库，需要输入要采集数据库的地址，账号，密码。

![20190319200243.png](https://i.loli.net/2019/03/19/5c90da6902391.png)

**调试** 用于界面调试输出结果。我们需要将上面的程序输出结果打印在界面右侧的调试窗口，按配置界面图链接即可

好了到了这一步基本完成了我们的配置了，最后只要把四个控件用线连接起来点击部署就完成啦，就可以取到mysql的数据了。附上一张取到数据的大图，接下来你们就可以根据自己的业务需要处理数据啦。

![20190319200526.png](https://i.loli.net/2019/03/19/5c90db113d44a.png)

![20190319200543.png](https://i.loli.net/2019/03/19/5c90db1da5a70.png)

下面附上今天分享内容的代码，只需要导入就能使用啦，提醒下mysql里面需要改成自己的ip和账号。

## 快速采集西门子PLC数据

***一、硬件环境***

1、海创Box智能网关

2、西门子PLC (ST20 S7-200)

![20190319201137.png](https://i.loli.net/2019/03/19/5c90dc7fb1d11.png)

**二、产品连接方式**

![20190319201459.png](https://i.loli.net/2019/03/19/5c90dd49a83c5.png)


***三、产品配置***

奥迈智能网关 请参考《奥迈智能网关设置》

**西门子PLC ** (ST20 S7-200) 配置好设备连接ip

![20190319201554.png](https://i.loli.net/2019/03/19/5c90dd819eb62.png)

![20190319202130.png](https://i.loli.net/2019/03/19/5c90ded7d489d.png)

***四、项目部署调试***

打开海创-IIoT。本次教程需要用到如下节点，在左侧节点栏中拖拽出使用

**定时器** 周期性触发输入时间戳或者相应的字符

![20190319202403.png](https://i.loli.net/2019/03/19/5c90df6949fa4.png)

**S7西门子** 用于读取S7西门子通信协议的设备数据

![20190319202428.png](https://i.loli.net/2019/03/19/5c90df81ea6b0.png)

**调试** 用于调试节点输出

![20190319202446.png](https://i.loli.net/2019/03/19/5c90df9408a7d.png)

配置相应节点后配置界面如下：

![20190319202520.png](https://i.loli.net/2019/03/19/5c90dfb6841f1.png)

***节点配置：***

**定时器** 设置成周期性触发，主要当触发流程使用，具体配置如下图

![20190319202810.png](https://i.loli.net/2019/03/19/5c90e063ad48f.png)

**S7西门子** 设置如下配置，即可取到M区0.1的地址数据

![20190319202849.png](https://i.loli.net/2019/03/19/5c90e0873c0fe.png)

![20190319202940.png](https://i.loli.net/2019/03/19/5c90e0babbde7.png)

**调试** 在右侧调试窗口输出调试结果，无需配置

***部署调试：***

按照以上教程配置好流程后，点击部署，部署成功后程序开始运行，就会在调试窗口输出相应的结果，调试结果如下

![20190319203021.png](https://i.loli.net/2019/03/19/5c90e0e3cbe12.png)

## 数据上传到阿里云

**一、产品**

需要用到的产品有：

1、海创-IIoT可视化开发平台

2、阿里云物联网平台

**二、产品配置**

**阿里云IoT平台**

1、注册阿里云IoT账号 在阿里云IoT 注册账号

2、创建产品 登录账号后在阿里云IoT产品页创建产品，选择高级版，填写相应信息，这边我们选择上传的是温湿度信息，节点类型选择网关，点击完成即可快速创建 

![20190319203638.png](https://i.loli.net/2019/03/19/5c90e25d3203e.png)

![20190319203657.png](https://i.loli.net/2019/03/19/5c90e26f740f8.png)


3、**创建设备** 创建产品完毕后创建相应设备 

![20190319204123.png](https://i.loli.net/2019/03/19/5c90e37bd43b7.png)

4、**查看设备详情** 点击刚才创建的设备查看ProductKey(产品key)、DeviceSecret(产品密钥)、DeviceName(设备名称)

![20190319204400.png](https://i.loli.net/2019/03/19/5c90e416eae19.png)

**三、节点流**

本次教程需要用到如下节点，在左侧节点栏中拖拽出使用

**定时器** 在输入栏目，用于读取串口二进制流

![20190319204445.png](https://i.loli.net/2019/03/19/5c90e442adbc9.png)

**function** 在功能栏目，用于配置逻辑代码

![20190319204453.png](https://i.loli.net/2019/03/19/5c90e44b96c18.png)

**阿里云IoT** 在物联网云平台栏目，用于上传数据到阿里云

![20190319204514.png](https://i.loli.net/2019/03/19/5c90e4602d181.png)


**调试** 在输出栏目，用于调试输出

![20190319204521.png](https://i.loli.net/2019/03/19/5c90e4683270e.png)

配置界面详情（快速复用请导航到文章末端）

![20190319204547.png](https://i.loli.net/2019/03/19/5c90e4817a8fc.png)


接下来我们来配置如上图的节点流，首先将左侧节点栏的**定时器**、**function**、**阿里云IoT**、**调试**节点分别拖拽到工作区，再点击相应的流节点的端口依次按配置界面所示连接起来，再双击相应流节点进入配置界面配置相应属性

**定时器** 用于触发或定时输出数据。这边我们只当做触发器使用，无需配置，使用时点击左侧触发按钮

**function** 是用于编写自定义代码的节点工具，该控件支持nodejs语法，可以实现您所有的业务逻辑。我们的框架内定义一般流的数据向下流动时都将数据存入msg.payload这个对象中，所以我们编辑一条流信息，key为在阿里云IoT上设置的产品属性，在产品详情页查看。配置如下图

![20190319204742.png](https://i.loli.net/2019/03/19/5c90e4f48b2bb.png)

![20190319204828.png](https://i.loli.net/2019/03/19/5c90e52276ae9.png)

代码如下：

```
msg.payload={    
CurrentTemperature:10,    
CurrentHumidity:11}
return msg;
```

**阿里云IoT** 用于上传数据到阿里云IoT，首先需要到阿里云IoT上注册相应的设备信息，获取到相应的ProductKey(产品key)、DeviceSecret(产品密钥)、DeviceName(设备名称)。然后再节点内填入相应配置信息，配置详情如下

![20190319204932.png](https://i.loli.net/2019/03/19/5c90e5624ce2f.png)

**调试** 用于界面调试输出结果。我们需要将上面的程序输出结果打印在界面右侧的调试窗口，按配置界面图链接即可

**四、部署调试**

经过上面所有步骤后，即可部署程序，部署后点击定时器左侧触发按钮触发后，就可以在右侧的调试窗口看到输出，如下图

![20190319205045.png](https://i.loli.net/2019/03/19/5c90e5abe03c9.png)

在阿里云平台的设备运行状况中可以看到最新上传的数据

![20190319205131.png](https://i.loli.net/2019/03/19/5c90e5d9d515d.png)

