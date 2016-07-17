title: 创建RESTAPI神器-JsonServer
categories: Tools
date: 2015-09-24 14:52:37
tags: [Tools,NodeJs,RestApi]
---


## [JsonServer](https://github.com/typicode/json-server)
> Get a full fake REST API with zero coding in less than 30 seconds (seriously)

可以用来创建 RestApi,非常方便.

## 安装

应该还需要装NodeJs什么的,但是我已经安装过了.
```Shell
$ sudo npm install -g json-server
```
## 基本使用

1. 找个地方新建一个db.json文件
写入一段json,如:
```Json
{
  "posts": [
    { "id": 1, "title": "json-server", "author": "typicode" }
  ],
  "comments": [
    { "id": 1, "body": "some comment", "postId": 1 }
  ],
  "profile": { "name": "typicode" }
}
```

2. 启动server

```Shell
json-server --watch db.json
```

<!-- more -->

启动成功后:
![效果](http://ww2.sinaimg.cn/large/98900c07gw1ewdjux1qkyj20gv0a8abl.jpg)

接下去就可以访问了:  

![最后效果](http://ww2.sinaimg.cn/large/98900c07gw1ewdjxs30yvj208z05yweq.jpg)

## 设置别名

每次都输入`json-server --watch` 太麻烦了.  
我给它配置个别名:
```Shell
#jsw
alias jsw="json-server --watch"
```

记得`source ~/.zshrc`,这样以后就方便了:
![别名](http://ww4.sinaimg.cn/large/98900c07gw1ewdkd1t6e1j208403qmx8.jpg)


以后可以自己模拟写Api了~  
更多功能还需去[Github](https://github.com/typicode/json-server)看,不过作为一个Android程序猿,这个已经够了.
