---
title: 从输入URL到页面渲染
date: 2020-06-18 10:19:09
tags: http tcp
---

前言：不知道这玩意儿，前端也能干活。但是得进步啊。当学会了这玩意儿，对网络的理解会举头望明月，更上一层楼。
反正百利而无一害。嘿嘿，开始整活～

第一步，手放在键盘上敲出 url，然后回车，咔哒一敲！然后页面就出来了，是不是很简单～ 😁
让我们的学习气氛轻松起来～ 下面正式开始，我敲个 url：

https://onepunchx.github.io/archives/

输入完这个网址，敲回车！浏览器做的第一件事是 解析这个 url：

1. **通过 _DNS_（domain name system） 把域名（domain name）解析程 _IP_ 地址**

IP 地址这玩意儿，就相当于我们每个人的身份证号码，身份证号可以唯一、精准的匹配到一个人，那么 IP 地址就可以唯一、精准的匹配到对应的计算机。
但是身份证号码这玩意，你能记住几个？能记住自己的就不错了，通常你记住的都是别人的名字，那么这个域名/网址/URL 就相当于计算机的名字
形象一点的对应关系如下：

| 表头          | 表头               |
| ------------- | ------------------ |
| 名字          | 身份证号码         |
| 王昊          | 211232199608135438 |
| 域名          | IP                 |
| www.baidu.com | 220.181.111.188    |

2. **然后应用曾生成 HTTP 请求报文**
   啥是请求报文？
   请求报文 有起始行 首部 和 主体部分，说起来太不形象具体了，直接上代码！

GET https://www.baidu.com/ HTTP/1.1 （起始行）

Host: www.baidu.com
Connection: keep-alive
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/65.0.3325.181 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,_/_;q=0.8
X-Client-Data: CKm1yQEIhbbJAQijtskBCMG2yQEIqZ3KAQioo8oB
Accept-Encoding: gzip, deflate, br
Accept-Language: zh-CN,zh;q=0.9,en;q=0.8
（首部）

然后是主体内容，get 请求的主体内容为空，post 的请求主体内容就是请求体，body 里面的内容

这个 http 请求报文说起来里面学问很大，有机会的话单独写一次。这里给自己先挖个坑。。。

3. **传输层建立 TCP 连接**
   3 次握手，4 次挥手。
   这里不细说，等待后续填坑

4. **网络层使用 IP 协议来选择路线**
   处理来自传输层的数据段 segment，将数据段 segment 装入数据包 packet，填充包头，主要就是添加源和目的 IP 地址，然后发送数据。在数据传输的过程中，IP 协议负责选择传送的路线，称为路由功能

5. **数据链路层实现网络相邻结点间可靠的数据通信**
   　　为了保证数据的可靠传输，把数据包 packet 封装成帧(Frame)，并按顺序传送各帧。由于物理线路的不可靠，发出的数据帧有可能在线路上出错或丢失，于是为每个数据分块计算出 CRC(循环冗余检验)，并把 CRC 添加到帧中，这样接收方就可以通过重新计算 CRC 来判断数据接收的正确性。一旦出错就重传

将数据包 packet 封装成帧(Frame)，包括帧头和帧尾。帧尾是添加被称做 CRC 的循环冗余校验部分。帧头主要是添加数据链路层的地址，即数据链路层的源地址和目的地址，即网络相邻结点间的源 MAC 地址和目的 MAC 地址

6. **数据链路层的帧(Frame)转换成二进制形式的比特(Bit)流，从网卡发送出去，再把比特转换成电子、光学或微波信号在网络中传输**
