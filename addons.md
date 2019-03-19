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
//输出多种类型数据
var newMsg = { payload: msg.payload.length };
return [msg, newMsg];

```

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



## websocket控件(长链接)

在一些场景中，我们需要实时推送一些设备消息到服务器上(设备在线状态)或者服务器发送一些实时的指令到设备上(服务器控制设备运行)，这种实时性要求很高或者需要服务端推送到客户端指令的需求就要使用到websocket控件


**一、使用控件**

1. **inject** 周期性触发输入时间戳或者相应的字符

   ![20190319192401.png](https://i.loli.net/2019/03/19/5c90d154165f7.png)

2. **websocket in** 用于创建websocket服务或者websocket客户端连接的输入

   ![20190319192838.png](https://i.loli.net/2019/03/19/5c90d268b1d89.png)


3. **websocket out** 用于创建websocket客户端连接或者websocket服务的输出

   ![20190319193122.png](https://i.loli.net/2019/03/19/5c90d30c24bb1.png)

4. **调试** 用于在调试窗口输出调试内容

    ![20190319194748.png](https://i.loli.net/2019/03/19/5c90d6e68653a.png)



**二、配置界面**

![20190319194136.png](https://i.loli.net/2019/03/19/5c90d5725dba1.png)

**三、 节点属性配置**

- **websocket in** 本案例中用于搭建websocket服务，用于客户端调用。类型选择连接，配置服务地址为/ws/input，那么完整的连接地址ws://为本机ip:1880/ws/input。配置详情如下图

![20190319195515.png](https://i.loli.net/2019/03/19/5c90d8a58ad67.png)


![20190319195447.png](https://i.loli.net/2019/03/19/5c90d888c9004.png)




- **调试** 用于打印输出websocket in控件接收到来自客户端的信息，拉取即可使用，无需配置



- **定时器** 本案例中用于周期性发送设备在线状态，我们这边设备为每秒发送一条json，内容为`{"status":"ok"}`，交付于websocket out控件。配置详情如下

![20190319200607.png](https://i.loli.net/2019/03/19/5c90db318e121.png)


- **websocket out** 本案例这中用于创建websocket客户端连接的输出，连接上面我们搭好的websocket服务，类型选择`连接`，地址选择我们上面配置好的地址`ws://127.0.0.1:1880/ws/input`。配置详情如下

![20190319203023.png](https://i.loli.net/2019/03/19/5c90e0e1cef24.png)

![20190319203040.png](https://i.loli.net/2019/03/19/5c90e0f2434fa.png)



**四、部署调试**

按照以上教程配置好流程后，点击菜单栏**部署**按钮进行部署程序。部署成功后程序开始运行，就会在调试窗口输出相应的结果

程序运行逻辑为：

    websocket in控件搭建websocket服务，并将接收到的消息交付于调试控件在调试窗口打印输出。websocket out控件创建websocket客户端服务连接到websocket in控件搭建的websocket服务，并定时器周期性触发发送一条设备在线消息


调试界面：

![20190319204615.png](https://i.loli.net/2019/03/19/5c90e498ce8d7.png)

也可以使用前台js代码调用websocket服务，这边我们使用谷歌浏览调试界面进行调试

调试代码：

```js

    var ws = new WebSocket("ws://127.0.0.1:1880/ws/input");
    ws.onopen = function()
    {
    console.log("打开websocket连接");
    ws.send("hello");
    };
    ws.onmessage = function(evt)
    {
    console.log(evt.data)
    };
    ws.onclose = function(evt)
    {
    console.log("WebSocketClosed!");
    };
    ws.onerror = function(evt)
    {
    console.log("WebSocketError!");
    };


```

调试结果：

![20190319210550.png](https://i.loli.net/2019/03/19/5c90e93005b6f.png)

![20190319210616.png](https://i.loli.net/2019/03/19/5c90e949c654a.png)


**五、示例代码**

以上教程可以通过拷贝下面代码实现快速复用，在新建的流程中点击界面右侧 **菜单栏**-**导入**-**剪贴板**，在文本框中粘贴下面代码后点击确定，即可快速复用


```js

[
    {
        "id": "792d5460.ccc41c",
        "type": "websocket in",
        "z": "41f61d2.fbe09e4",
        "name": "",
        "server": "b0127548.86f728",
        "client": "",
        "x": 215,
        "y": 140,
        "wires": [
            [
                "5d716e38.8d8f7"
            ]
        ]
    },
    {
        "id": "5d716e38.8d8f7",
        "type": "debug",
        "z": "41f61d2.fbe09e4",
        "name": "",
        "active": true,
        "tosidebar": true,
        "console": false,
        "tostatus": false,
        "complete": "payload",
        "x": 420,
        "y": 140,
        "wires": []
    },
    {
        "id": "66f23f0f.0b1cf",
        "type": "websocket out",
        "z": "41f61d2.fbe09e4",
        "name": "",
        "server": "",
        "client": "c5e8ab54.85c258",
        "x": 495,
        "y": 280,
        "wires": []
    },
    {
        "id": "7f0bdfdd.5ba3d",
        "type": "inject",
        "z": "41f61d2.fbe09e4",
        "name": "",
        "topic": "",
        "payload": "{\"status\":\"ok\"}",
        "payloadType": "json",
        "repeat": "",
        "crontab": "",
        "once": false,
        "onceDelay": 0.1,
        "x": 235,
        "y": 280,
        "wires": [
            [
                "66f23f0f.0b1cf"
            ]
        ]
    },
    {
        "id": "b0127548.86f728",
        "type": "websocket-listener",
        "z": "",
        "path": "/ws/input",
        "wholemsg": "false"
    },
    {
        "id": "c5e8ab54.85c258",
        "type": "websocket-client",
        "z": "",
        "path": "ws://127.0.0.1:1880/ws/input",
        "tls": "",
        "wholemsg": "false"
    }
]

```
## 时序数据库的使用

**配置前准备**

1、安装好“海创-IIoT”平台

海创-IIoT下载地址在这里,请加QQ群**219496645**，在文件中可以获得有“海创-IIoT”免费使用版本,下载后点击安装包，按界面提示安装好软件

2、在你的电脑或局域网内已经安装好**Influxdb数据库**，还没有安装的，（安装Influxdb数据库，本文略过安装过程）

**使用控件：**

1、**Influxdb：** 一个JavaScript函数块，用于编输入Influxdb语句

![20190319213416.png](https://i.loli.net/2019/03/19/5c90efe369dee.png)

2、**定时器：** 用于触发一个数据提交指令
   
![20190319213437.png](https://i.loli.net/2019/03/19/5c90eff2df49f.png)

3、**Function：** JavaScript函数块，传入编写Influxdb语句代码

![20190319213454.png](https://i.loli.net/2019/03/19/5c90f0042b6bd.png)

4、**调试：** 用于输出Influxdb查询、插入的返回值
 	
![20190319213515.png](https://i.loli.net/2019/03/19/5c90f018f2ef0.png)

开始配置：

先看下配置好的整体应用流程

![20190319213619.png](https://i.loli.net/2019/03/19/5c90f05a9d251.png)


可以看到开发界面中配好了两个流，其中第一行为**Influxd**b插入数据，第二行为Influxdb查询数据。

同时右侧的调试窗口显示两条数据包，第一条是定时器触发插入的数据信息，第二条是定时器触发返回查询的数据信息。

插入Influxdb数据配置

![20190319213642.png](https://i.loli.net/2019/03/19/5c90f07024e8c.png)

按上图的第一行，从节点中拉取Influxdb、Function控件，摆放出插入数据流。

1、双击Influxdb控件，填写连接地址与库名等Influxdb控件属性，如下图所示：

![20190319213733.png](https://i.loli.net/2019/03/19/5c90f0a372f8f.png)

2、双击双击命名为“写入最新数据”的Function控件，编写插入语句：

![20190319213743.png](https://i.loli.net/2019/03/19/5c90f0ad2a15c.png)

查询Influxdb数据配置

![20190319213822.png](https://i.loli.net/2019/03/19/5c90f0d47b1d2.png)


按上图，从节点中拉取Influxdb、Function控件，摆放出查询数据流。

1、在插入Influxdb数据配置流中我们已经配置过Influxdb控件，可把使用CTRL+C复制出Influxdb控件，复制出的控件包含原有的控件属性。

2、双击双击命名为“查询数据（前10个）”的Function控件，编写插入语句：

![20190319213853.png](https://i.loli.net/2019/03/19/5c90f0f3f05b8.png)


3、双击双击命名为“输出JSON”的Function控件，编写插入语句：

![20190319213922.png](https://i.loli.net/2019/03/19/5c90f11064bdd.png)

配置代码：

```
[{
	"id": "475013ad.4e839c",
	"type": "function",
	"z": "1f72dc38.e10dc4",
	"name": "写入最新数据",
	"func": "\n\n//写入数据库\nmsg.payload = [{\n    measurement: \"SW_QTH\",  //表名\n    tags:{\n        eid: 'PH值检测仪',\n        type: 'TCP'\n    },\n    fields: {\n        ph: 7,  //PH值\n    },\n}]\n\n\nreturn msg;\n\n",
	"outputs": 1,
	"noerr": 0,
	"x": 195,
	"y": 250,
	"wires": [
		["a32b204c.dcc24"]
	]
}, {
	"id": "3c8e6cd2.a97a24",
	"type": "inject",
	"z": "1f72dc38.e10dc4",
	"name": "",
	"topic": "",
	"payload": "",
	"payloadType": "date",
	"repeat": "",
	"crontab": "",
	"once": false,
	"onceDelay": 0.1,
	"x": 94,
	"y": 250,
	"wires": [
		["475013ad.4e839c"]
	]
}, {
	"id": "a32b204c.dcc24",
	"type": "influxdb batch",
	"z": "1f72dc38.e10dc4",
	"influxdb": "f43366f0.c357e8",
	"precision": "",
	"retentionPolicy": "",
	"name": "influxDB",
	"x": 335,
	"y": 250,
	"wires": [
		["b458e170.cc73a"]
	]
}, {
	"id": "b458e170.cc73a",
	"type": "debug",
	"z": "1f72dc38.e10dc4",
	"name": "",
	"active": true,
	"tosidebar": true,
	"console": false,
	"tostatus": false,
	"complete": "false",
	"x": 475,
	"y": 251,
	"wires": []
}, {
	"id": "a9725b0f.4f8d68",
	"type": "function",
	"z": "1f72dc38.e10dc4",
	"name": "查询数据（前10个）",
	"func": "\n\nmsg.query=\"select * FROM SW_QTH where (type='sz') and (eid='shuizhi1') ORDER BY time DESC LIMIT 10\"; \n\n\n\nreturn msg;",
	"outputs": 1,
	"noerr": 0,
	"x": 189,
	"y": 369,
	"wires": [
		["c23475ce.3d6508"]
	]
}, {
	"id": "c23475ce.3d6508",
	"type": "influxdb in",
	"z": "1f72dc38.e10dc4",
	"influxdb": "f43366f0.c357e8",
	"name": "influxDB",
	"query": "",
	"rawOutput": false,
	"precision": "",
	"retentionPolicy": "",
	"x": 331,
	"y": 369,
	"wires": [
		["1b2dbc08.cdea04"]
	]
}, {
	"id": "3fa972b4.d36f2e",
	"type": "inject",
	"z": "1f72dc38.e10dc4",
	"name": "",
	"topic": "",
	"payload": "",
	"payloadType": "date",
	"repeat": "",
	"crontab": "",
	"once": false,
	"onceDelay": 0.1,
	"x": 94,
	"y": 370,
	"wires": [
		["a9725b0f.4f8d68"]
	]
}, {
	"id": "1b2dbc08.cdea04",
	"type": "function",
	"z": "1f72dc38.e10dc4",
	"name": "输出JSON",
	"func": "\nmsg.payload={\n    \"type\":\"shuizhi_Today\",\n    \"value\":{\n        \"data\":[\n            msg.payload[0].ph\n        ],\n    }\n}\n\nreturn msg;",
	"outputs": 1,
	"noerr": 0,
	"x": 435,
	"y": 370,
	"wires": [
		["89d071ff.89ca7"]
	]
}, {
	"id": "89d071ff.89ca7",
	"type": "debug",
	"z": "1f72dc38.e10dc4",
	"name": "",
	"active": true,
	"tosidebar": true,
	"console": false,
	"tostatus": false,
	"complete": "false",
	"x": 595,
	"y": 370,
	"wires": []
}, {
	"id": "f43366f0.c357e8",
	"type": "influxdb",
	"z": "",
	"hostname": "192.168.7.155",
	"port": "8086",
	"protocol": "http",
	"database": "equipment",
	"name": "",
	"usetls": false,
	"tls": ""
}]

```

## 快速使用MSSQL数据库

首先我们要使用到**海创-IIoT**软件里面的控件有这些：

1、定时器 用于触发流程，可周期性触发、定义触发内容

![20190319214258.png](https://i.loli.net/2019/03/19/5c90f1e86513d.png)

2、function 一个JavaScript函数块，用于针对节点接收的消息运行。

![20190319214309.png](https://i.loli.net/2019/03/19/5c90f1f344779.png)

3、MSSQL 连接Microsoft SQL Server数据库控件。

![20190319214325.png](https://i.loli.net/2019/03/19/5c90f202d2612.png)

4、调试 可以将结果打印在右侧调试窗口上

![20190319214341.png](https://i.loli.net/2019/03/19/5c90f2133c728.png)


接下来是重点，把以上四个控件组合起来就可以使用MSSQL数据库。先放出最后的效果图。

![20190319214417.png](https://i.loli.net/2019/03/19/5c90f237adcb6.png)

![20190319214434.png](https://i.loli.net/2019/03/19/5c90f249400b8.png)

接下来讲一下这四个控件如何配置：

**定时器** 用于触发或定时输出数据。这边我们只当做触发器使用，无需配置，使用时点击左侧触发按钮

**function** 一个JavaScript函数块，写上你要对MSSQL做什么操作的语句。下图是做查询操作的语句。（新增和删除同理）。

![20190319214515.png](https://i.loli.net/2019/03/19/5c90f27178932.png)


**MSSQL** Microsoft SQL Server数据库，需要输入要连接数据库的地址，用户，密码，数据库。

![20190319214530.png](https://i.loli.net/2019/03/19/5c90f28072857.png)


**调试** 用于界面调试输出结果。我们需要将上面的程序输出结果打印在界面右侧的调试窗口，按配置界面图链接即可


好了到了这一步基本完成了我们的配置了，最后只要把四个控件用线连接起来点击部署就完成啦，就可以访问MSSQL数据库。

```
[{"id":"4a4e9d31.65a024","type":"tab","label":"快速使用MSSQL数据库","disabled":false,"info":""},{"id":"ca230a6e.9e68f8","type":"MSSQL","z":"4a4e9d31.65a024","mssqlCN":"3d582271.06591e","name":"MSSQL","query":"","outField":"payload","x":435,"y":140,"wires":[["f6d9db95.4a7bf8"]]},{"id":"f65a6516.4ba1a8","type":"inject","z":"4a4e9d31.65a024","name":"","topic":"","payload":"","payloadType":"date","repeat":"","crontab":"","once":false,"onceDelay":0.1,"x":170,"y":140,"wires":[["59cc2eb3.45546"]]},{"id":"f6d9db95.4a7bf8","type":"debug","z":"4a4e9d31.65a024","name":"","active":true,"tosidebar":true,"console":false,"tostatus":false,"complete":"true","x":575,"y":140,"wires":[]},{"id":"59cc2eb3.45546","type":"function","z":"4a4e9d31.65a024","name":"","func":"msg.payload=\"select * from test\";\nreturn msg;","outputs":1,"noerr":0,"x":315,"y":140,"wires":[["ca230a6e.9e68f8"]]},{"id":"3d582271.06591e","type":"MSSQL-CN","z":"","name":"","server":"192.168.7.47,1433","encyption":false,"database":"test"}]
```