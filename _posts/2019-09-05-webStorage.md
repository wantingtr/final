---
layout: post
title: "浏览器存储与缓存机制"
#subtitle: ""
date: 2019-09-05 18:00:00
author: "wantingtr"
tags:
    - 前端
---

#
@[toc]
# 浏览器存储与缓存机制
![](https://img-blog.csdnimg.cn/20190905151836366.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dhbnRpbmd0cg==,size_16,color_FFFFFF,t_70)


## 浏览器存储
缓存的数据类型都为**字符串**，通常需将对象**序列化**成字符串。

### cookie
 - **HTTP协议是一种无状态协议**，在前端请求服务器对任何HTTP请求都需要携带cookie,其中**包含着会话信息**，用来辨别用户身份。
- 服务端生成，客户端进行维护和存储。所以cookie的本职工作并非本地存储，而是**维持状态**

#### 原理及生成方式
![](https://img-blog.csdnimg.cn/20190905151909510.png)


#### cookie的缺陷

- 不够大，限制在4KB左右，且某些浏览器会对cookie的数量做限制
- 过多的 Cookie 会带来性能浪费（同一个域名下的所有请求都会携带 Cookie。）
    > 对于静态文件的请求，携带cookie信息根本没有用，此时可以通过**cdn（存储静态文件的）的域名和主站的域名分开**来解决。
- 在HTTP请求中的Cookie是明文传递的, 不够安全，除非用HTTPS。
	> 如果包含服务端 Session 信息的 Cookie 不想被客户端 JavaScript 脚本调用，那么就应该为其设置`HttpOnly `标记。否则，通过js的Document.cookie API可以直接访问和修改cookie, 出现XSS攻击

#### cookie与session的区别与联系
session和cookie的区别：
* Cookie 是**浏览器端**的存储对象，有容量限制，通过 HTTP 报文与后端交互
* Session 是**服务端**的存储对象，实现的方式可以有文件系统、缓存系统、数据库

session和cookie的联系:
 * Session 和 Cookie 都是为了实现 HTTP 请求带上客户端状态的方法
 * **Session 大多数情况下都是依赖 Cookie 来传递 Session Id**

#### 手写cookie
```js
let cookie = {
    //一个cookie的格式为键值对‘key=value; key=value; key=value’
    // 每个cookie以'; '分隔（分号+空格）

    //设置cookie
    // time为有效时间
    set: function(key, val, time) {
        let date = new Date();
        let expiresDays = time;
        date.setTime(date.getTime() + expiresDays * 24 * 3600 * 1000); //计算过期时间
        document.cookie = key + '=' + val + ';expires=' + date.toGMTString();
    },
    // 获取cookie
    get: function(key) {
        let getCookie = document.cookie.replace(/[ ]/g, ""); //去除不同cookie之间的分界空格
        let arrCookie = getCookie.split(";"); //将cookie保存到arrCookie数组中
        let res;
        for (let i = 0; i < arrCookie.length; i++) {
            // 格式： key=value
            let arr = arrCookie[i].split('=');
            if (key === arr[0]) {
                res = arr[1];
                break;
            }
        }
        return res;
    }
}
```

### localStorage

通过localStorage可以将数据存储在客户端,并且可以通过`localStorage.setItem / localStorage.getItem`调用。

1. localStoreage用于将大量数据（最大5M）保存在浏览器中
2. 保存后**永久存储**，除非用js手动清除。
2. **不参与网路传输**
3. 一般用于优化性能，主要用于保持大量数据。

### sessionStorage
通过`sessionStorage.setItem / sessionStorage.getItem`调用。

- 建议存储一些当前页面**刷新需要存储**，且不需要在tab关闭时候留下的信息。

- 可以用sessionStorage来检测用户是否是刷新进入的页面，例如音乐播放器恢复播放进度条


### sessionStorage 、localStorage 之间的区别
* 共同点：都是保存在浏览器端，且都遵循同源策略。
* 不同点：在于生命周期与作用域的不同
    * 作用域：`localStorage`只要在相同的协议、主机名、端口下，就能读取/修改到同一份localStorage数据。sessionStorage除此之外，还要求在同一窗口（也就是浏览器的标签页）下
    * 生命周期：localStorage 是持久化的本地存储，不会过期，只能手动删除；而 sessionStorage 是临时性的本地存储

### IndexedDB
Web Storage只能用来存储字符串，只能存储**较少量的数据**，遇到大规模且结构复杂的数据时，需要使用IndexedDB。

IndexedDB用于**客户端存储大量结构化数据**。是一个**运行在浏览器上的非关系型数据库**。是没有存储上限，不仅可以存储字符串，还可以存储二进制数据。

### cache
在进行第一次连接时，所有资源都需要客户端向服务端获取。并且浏览器会缓存一部分**资源**到**本地cache**， 当再次连接时，直接从浏览器获取即可。如果服务器更新了资源，再重新获取。怎样判断是否需要重新获取？就是下文的浏览器缓存机制的作用了。

### 总结区分
![](https://img-blog.csdnimg.cn/20190905152120173.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dhbnRpbmd0cg==,size_16,color_FFFFFF,t_70)

## 浏览器缓存机制

### 缓存过程

![](https://img-blog.csdnimg.cn/20190905152222296.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dhbnRpbmd0cg==,size_16,color_FFFFFF,t_70)

1. 浏览器每次发起请求，都会**先在浏览器缓存中查找该请求的结果以及缓存标识**
2. 拿到返回的请求结果后，都会**将该结果和缓存标识存入浏览器缓存中**
3. 浏览器对于缓存的处理是**根据第一次请求资源时返回的响应头来确定的。**

根据缓存机制的不同，分为强缓存与协商缓存。
- . 强缓存之所以叫强缓存，是因为缓存有效与否，**只看时间**。只要已过期，缓存无效，需要重新请求。
- . 协商缓存则现需与服务器交互一次，拿到最新的协商缓存响应头，比较**服务端的资源是否有更新**。

![](https://img-blog.csdnimg.cn/20190905152454739.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dhbnRpbmd0cg==,size_16,color_FFFFFF,t_70)
### 强缓存
**不会向服务器发送请求，直接从缓存中读取资源**

 - 在chrome的Network中可以看到该请求返回**200**的状态码 
 - Size显示`from disk cache`或`from memory cache`。

通过设置两种 HTTP Header 实现：`Expires` 和 `Cache-Control`。

#### Expires(HTTP/1.0)
缓存过期时间，用来指定**资源到期的时间**，是**服务器端**的具体的时间点。
- `Expires=max-age + 请求时间`，需要和Last-modified结合使用。
- Web服务器**响应消息头字段**
- 受限于本地时间，**如果修改了本地时间，可能会造成缓存失效。**


#### Cache-Control (HTTP/1.1)
Cache-Control 可以在请求头或者响应头中设置，并且可以组合使用多种指令：
![](https://img-blog.csdnimg.cn/20190905152814719.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dhbnRpbmd0cg==,size_16,color_FFFFFF,t_70)


#### 区别
 - `Expires` 是http1.0的产物 `Cache-Control`是http1.1的产物
 - `Cache-Control`优先级高于`Expires`
 - 在某些不支持HTTP1.1的环境下，才使用`Expires`。

### 协商缓存

因为强缓存根据过期时间来判断是否需要重新请求，所以会出现在有效期内，服务器资源已经更新，但获取到的仍是旧资源。

协商缓存就是**强制缓存失效(可能是cache-control设置了no-cache或no-store)后**，浏览器**携带缓存标识向服务器发起请求**，**由服务器根据缓存标识决定是否使用缓存**的过程，主要有以下两种情况：


1. 协商缓存生效，返回`304`和`Not Modified`

2. 协商缓存失效，返回200和请求结果


协商缓存可以通过设置两种 服务端的响应头来实现：`Last-Modified` 和 `ETag` 。


#### `Last-Modified`和`If-Modified-Since`  (HTTP/1.0)

1. 浏览器在第一次访问时，服务器在响应头中添加 `Last-Modified`的header，值是这个资源的**最后修改时间**

2. 浏览器下一次请求，检测到有 `Last-Modified`，添加`If-Modified-Since`，值就是`Last-Modified`中的值；服务器会根据**请求中If-Modified-Since 中的值与服务器中这个资源的最后修改时间对比**，看是否有变化。

缺陷：
- 本质上根据修改时间判断，若某资源在一秒内修改多次，则该方法会失效

#### `ETag`和`If-None-Match`  (HTTP/1.1)

Etag是服务器响应请求时，返回**资源的一个唯一标识，只要资源有变化，Etag就会重新生成。**

具体过程：
1. 浏览器在向服务器发送请求时，会**将上一次返回的Etag值放到请求头的If-None-Match里**

2. 服务器**比较If-None-Match跟服务器上该资源的ETag是否一致**，匹配不上，返回200。


#### 区别
- 精确度： `Etag > Last-Modified`
    - Last-Modified的时间单位是秒，如果某个文件在1秒内改变了多次，那么他们的Last-Modified其实并没有体现出来修改，
    - Etag每次都会改变确保了精度；如果是负载均衡的服务器，各个服务器生成的Last-Modified也有可能不一致。

- 性能： `Etag < Last-Modified`
    - Last-Modified只需要记录时间，
    - Etag需要服务器通过算法来计算出一个hash值。

- 优先级： `Etag > Last-Modified` 
    - 服务器校验优先考虑Etag



### 根据缓存机制详解返回304的全过程
![](https://img-blog.csdnimg.cn/20190905154042559.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dhbnRpbmd0cg==,size_16,color_FFFFFF,t_70)
参考文章：
1.  [深入理解浏览器的缓存机制](https://www.jianshu.com/p/54cc04190252)
2. [从输入URL到页面加载的过程？如何由一道题完善自己的前端知识体系！](https://segmentfault.com/a/1190000013662126#articleHeader30)
