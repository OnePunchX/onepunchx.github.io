---
title: 使用webSocket完成前后端交互
date: 2020-09-09 14:40:34
tags: webSocket
categories: 前端
---

## webSocket

### 简介

WebSocket 是 HTML5 开始提供的一种在单个 TCP 连接上进行全双工通讯的协议。
是一种网络通信协议
在 WebSocket API 中，浏览器和服务器只需要做一个握手的动作，然后，浏览器和服务器之间就形成了一条快速通道。两者之间就直接可以数据互相传送。
直白一点说，想做一个类似于消息推送的功能，大概能想到两种方法
第一种就是 ajax 轮询，整一个定时器不停的调用后端接口
另一种就是用 webSocket，让服务器端可以主动的发消息给浏览器端
所以你看，还是 webSocket 好啊

当你获取 Web Socket 连接后，你可以通过 send() 方法来向服务器发送数据，并通过 onmessage 事件来接收服务器返回的数据。
以下 API 用于创建 WebSocket 对象。
`var Socket = new WebSocket(url, [protocol] );`
以上代码中的第一个参数 url, 指定连接的 URL。第二个参数 protocol 是可选的，指定了可接受的子协议。

总觉得要自己玩一次才更好理解这个，看来对 node 的学习要提上日程了。

```
var ws = new WebSocket("wss://echo.websocket.org");

ws.onopen = function(evt) {
  console.log("Connection open ...");
  ws.send("Hello WebSockets!");
};

ws.onmessage = function(evt) {
  console.log( "Received Message: " + evt.data);
  ws.close();
};

ws.onclose = function(evt) {
  console.log("Connection closed.");
};
```

webSocket 扩展，心跳检测，数据加密，身份认证等知识点。

### 插件及使用

使用了一个 webSocket 的插件叫做 sockjs-client，封装组件如下

```
/**
 * WebSocket Client
 */

import { useEffect, useState } from 'react';
import SockJS from 'sockjs-client';
import Stomp from 'stompjs';
import { getToken } from '@/utils/Enhanced';  //  项目用获取token的方法，可以忽略不管

const Index = props => {
  const { url, options, connect = {}, observers = [], disconnect = {}, children } = props;

  const newUrl = url.includes('?') ? url : `${url}?token=${getToken()}`;

  const [client, setClient] = useState(null);

  useEffect(() => {
    const newClient = Stomp.over(new SockJS(newUrl, null, options));
    newClient.debug = () => {};

    const { onOpen, onError } = connect;

    newClient.connect(
      connect.headers || {},
      () => {
        if (onOpen) {
          onOpen();
        } else {
          console.log('CONNECTED');
        }

        setClient(newClient);
      },
      error => {
        if (onError) {
          onError();
        } else {
          console.log('ERROR', error);
        }
      },
    );

    return () => {
      if (newClient.ws.readyState === SockJS.OPEN) {
        const { onClose } = disconnect;
        newClient.disconnect(() => {
          if (onClose) {
            onClose();
          }
        }, disconnect.headers);
      }
    };
  }, []);

  useEffect(() => {
    if (client) {
      observers.forEach(observer => {
        const { destination, onMessage } = observer;
        if (destination) {
          client.subscribe(
            destination,
            data => {
              try {
                onMessage(JSON.parse(data.body));
              } catch (e) {
                onMessage(data.body);
              }
            },
            observer.headers,
          );
        }
      });
    }
    return () => {
      if (client) {
        Object.keys(client.subscriptions).forEach(item => {
          client.unsubscribe(item);
        });
      }
    };
  }, [!!client, observers.map(item => item.destination).join(',')]);

  return typeof children === 'function' ? children(client) : children || null;
};

Index.displayName = 'WsClient';
export default Index;

```

### 项目应用相关文档

问题描述：
对于 dip-tms-fe 项目的预估报价、结算报价自动分析功能，当待分析文件数量过多或
文件过大时，服务器会因网络超时而导致 500 错误，尽管此时后端分析服务还在运行中。
问题原因：
因服务器长时间未响应，请求时间过长，而引发此错误。为避免这种情况，后端可反馈
伪成功，即代表已收到请求。而后，基于 WebSocket 双工通信机制，在后端处理完请求后，
主动向前端推送处理结束消息。
解决方案： 1.解决 WebSocket 连接过慢问题，支持断后重连
在页面头部导航中引入 WebSocket，以便用户在登录成功后，立即开始建立连接。通过
查阅资料发现 sockJS 支持多种连接方式：websocket，xdr-streaming，xhr-streaming，
eventsource，iframe-eventsource，htmlfile，iframe-htmlfile，xdr-polling，xhr-polling，
iframe-xhr-polling，jsonp-polling。建立连接时会依次进行尝试，直到连接成功。在测
试时发现采用“xhr-polling”的方式连接耗时最短，因此将其设为首选连接方式。为避免
因网络断开导致的 WebSocket 通讯中断，因此支持了断后重连。
url：用于建立握手连接

```

      <WsClient
        url="/server/api/wsconnect"
        observers={[
          {
            destination: `/user/${currentUser.userId}/notice`,
            onMessage: () => {
              console.log('订阅返回');
              PubSub.publish('更新报告分析结果');
            },
          },
        ]}
      />

```

destination：统一用户定位符，能把一个用户独一无二的标识出来
onMessage：接收由后端主动向前端推送的各种消息
coverAndSend：以广播形式，将消息推送给所有用户
coverAndSendToUser：将消息推送给指定用户
