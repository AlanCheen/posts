title: OkHttp和Retrofit2学习笔记
categories: Android
date: 2015-11-23 15:26:53
tags: [Retrofit,OkHttp,开源项目使用]
---




## 前言
<!-- more -->

## [OkHttp](http://square.github.io/okhttp/)


## 资料
1. [github](https://github.com/square/okhttp)
2. [wiki](https://github.com/square/okhttp/wiki/Calls)
3. [OkHttp-完全解析](http://blog.csdn.net/lmj623565791/article/details/47911083)
4. [OkHttp-Https](http://blog.csdn.net/lmj623565791/article/details/48129405)


## [Retrofit](http://square.github.io/retrofit/)

### 添加日志

```
HttpLoggingInterceptor logging = new HttpLoggingInterceptor();  
// set your desired log level
logging.setLevel(Level.BODY);

OkHttpClient httpClient = new OkHttpClient();  
// add your other interceptors …

// add logging as last interceptor
httpClient.interceptors().add(logging);  // <-- this is the important line!

```
参考自[这里](https://futurestud.io/blog/retrofit-2-log-requests-and-responses)

## 资料
[用 Retrofit 2 简化 HTTP 请求
](https://realm.io/cn/news/droidcon-jake-wharton-simple-http-retrofit-2/?hmsr=toutiao.io&utm_medium=toutiao.io&utm_source=toutiao.io)  
[Retrofit 2.0：有史以来最大的改进](http://www.jcodecraeer.com/a/anzhuokaifa/androidkaifa/2015/0915/3460.html)  
[Retrofit 2 — Upgrade Guide from 1.9
](https://futurestud.io/blog/retrofit-2-upgrade-guide-from-1-9/)  
[Retrofit 2 — Log Requests and Responses](https://futurestud.io/blog/retrofit-2-log-requests-and-responses)
[Love-working-with-APIS-on-Android](https://futurestud.io/books/retrofit)
[Retrofit2 源码解析](http://www.jianshu.com/p/c1a3a881a144)
