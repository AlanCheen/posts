title: Html入门学习
subtitle: "Android程序猿学Html"
categories: Web
date: 2016-07-05 19:43:37
tags: [Html]
---

## 前言

前几天玩了React Native,([文章](http://yifeiyuan.me/2016/06/28/%E5%88%9D%E8%AF%95ReactNative/)),发现如果想真的学好的话,还需要学习一下Html,Css,JavaScript,Node,ES6什么的,感觉东西好多好多     
一样一样来吧,从Html开始涉猎吧!  

<!-- more -->

## Html基础

Html似乎都是标签,数量有不少,但是估计常用的就那么些个,比如img,a什么的,所以打算粗略的都过一遍,了解了解,不打算深究  

因为不知道怎么去学,也没有买书看,所以暂时在慕课网上挑了个基础课程学习,有空闲时间的时候看看,发现还不错!!~~    

暂时记录一点笔记吧!毕竟还在写Android,容易忘记,还是笔记靠谱!~~    


固定结构:
```
<!DOCTYPE HTML>
<html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=utf-8">
        <title>Html</title>
    </head>
    <body>
        <h1>Hello Html!</h1>
    </body>
</html>
```

- `<html>`为根标签
- `<head>`标签用于定义文档的头部，它是所有头部元素的容器。头部元素有 `<title>`、`<script>`、`<style>`、`<link>`、 `<meta>` 等标签。
- 在`<body>`和`</body>`标签之间的内容是网页的主要内容,在这里的标签中的内容会在浏览器中显示出来。  

html,yml都跟xml差不多一样的语法格式,看起来也不难,恩,这是好事!~~  

希望自己能触类旁通!~  

## 基本标签  

**PS:这主题的表格丑的不能看啊!排版都乱套了~**  

标签名     |      作用   |       更多属性      | 样例 |备注
:---------|:----------:|:------------------:|:---:|:---:
`<h1></h1>`|标题|h2-6||h1最大
`<p></p>`| 段落|||
`<img src=“imageUrl”>` |显示图片|width="200" height="200",`alt="失败后显示的文本"`;`title="鼠标hover显示的文本"`||
`<a></a>`|链接|`href`指定链接;`title`属性,当鼠标hover的时候显示的文字;`target="_blank"`表示连接在新的网页打开;`mailto`发送邮件|`<a href="http://yifeiyuan.me">点我跳转到我的博客</a>`|
`<em>斜体效果</em>`||||
`<strong>加粗效果</strong>`||||
`<span></span>`|标签是没有语义的，它的作用就是为了设置单独的样式用的。|||
`<q>引用的文本</q>`|表示对文本的引用(少量)|||会自动加上双引号,不需要自己再加了!
`<blockquote>引用文本</blockquote>`|表示对文本的引用,不过是大量的文本|||浏览器对`<blockquote>`标签的解析是缩进样式,并且也不需要双引号
`<br>`|分行/换行|||
`&nbsp;`|空格|||在Html里直接输入空格/换行是不支持的
`<address>地址(默认自动斜体)</address>`|地址|||默认斜体
`<code>代码</code>`||||
`<pre>语言代码段</pre>`|大段代码|||预格式化的文本。被包围在 pre 元素中的文本通常会保留空格和换行符
`<ul><li>无序列表信息item</li></ul>`|无序列表|||
`<ol><li>有序列表信息item</li></ol>`|有序列表,从1开始|||
`table、tbody、tr、th、td`|表格|summary 摘要;caption 标题(显示在表格上方),方便搜索引擎||th表头,tr-行,td列


## div

似乎在Html中div的作用挺大,就分开记录了,也不知道对不对.  

在网页制作过程过中，可以把一些独立的逻辑部分划分出来，放在一个`<div>`标签中，这个`<div>`标签的作用就相当于一个容器。 相当于Android中的`ViewGroup`?

可以给div设置`id`  

## 表单标签&表单控件
```
<form method="post" action=""> </form>

<form    method="post"   action="save.php">
        <label for="username">用户名:</label>
        <input type="text" name="username" />
        <label for="pass">密码:</label>
        <input type="password" name="pass" />
</form>

```
属性`action` ：浏览者输入的数据被传送到的地方,比如一个PHP页面(save.php)
属性`method` ： 数据传送的方式（get/post）。

label,input都是`表单控件`

下面稍微记录一下,肯定会忘记...  

- label 用于显示文字,差不多相当于TextView  
`for`: 表示应用给哪个控件,?  
`name`: 控件名,跟id一样?  
- input 输入文本框   
`type`: 值为`submit`的时候是提交,当用户需要提交信息给服务器的时候使用;`reset`为重置;`text`时是文本;`password`是密码;`radio`单选;`checkbox`复选框;  
`value`: 可以用作默认值  
- textarea:文本域，支持多行文本输入,支持配置行列cols="10",rows="10"  
- select:下拉列表框   
`option`可以表示一个选项,如`<option value="看书" selected="selected">看书</option>` selected属性表示默认选中  
select的`multiple`属性,`multiple="multiple"`可以支持多选  


## 标签的一些属性

`class`属性,可以为一个标签设置多个`class属性`,中间用空格隔开,比如`class="people knight"`,可以理解为接口,一个标签可以实现多个接口(class),拥有不同接口(class)的样式.        

`id`属性,为一个标签设置一个id属性,同Android,css里的`id`是唯一的,即不能为不同的标签设置同一个`id`  


例子:  
```
<p class="dev bloger" id="yifeiyuan">程序亦非猿</p>
```

## 小结&感想

html还算是很好理解的嘛!~  

感觉写起来有点麻烦,内容多了估计排版会很乱吧!!!   

以后能看得懂Html的代码了 😄~

有没有发现,我的博客右边的SideBar部分,多了个 **打赏支持** 的模块,那就是我自己改的,哈哈哈,学以致用的感觉真棒!!!!  

## 资料  
[HTML+CSS基础课程](http://www.imooc.com/learn/9)  
