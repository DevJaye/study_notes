# Beetl 模板引擎 制作自己的blog
## 下载源码运行起来
- 源码地址:`https://gitee.com/gavink/beetl-blog` 
- 官方文档地址:`http://ibeetl.com/guide/#/beetl/` 
- 其他笔记地址`https://baijiahao.baidu.com/s?id=1608301601154521261&wfr=spider&for=pc` 第一课笔记
### 引入依赖
- 需要引入`starter` 这个包
```xml
<dependency>
    <groupId>com.ibeetl</groupId>
    <artifactId>beetl-framework-starter</artifactId>
    <version>1.2.11.RELEASE</version>
</dependency>
```
    - Beetl Framework Starter1.1.68才正常最新版本不正常:
## Beetl简介和集成springboot2.0
### 和springboot的集成
官方文档: `http://ibeetl.com/guide/#/beetl/web?id=spring-boot%e9%9b%86%e6%88%90` 


`starter ` 自动处理以btl结尾的视图，模板根目录是Spring Boot默认的templates目录。
```

beetl-beetlsql.dev,默认为true，即自动检查模板变化
beetl.enabled 默认为true，集成beetl。
beetl.suffix 默认为btl，表示只处理视图后缀为btl的模板，比如controller里代码是“return /common/index.btl”,则能被Beetl处理，你写成"return /common/index",或者"/common/index.html",都会出现404错误。
```
## Beetl模板的基础用法
- 引入jdbc依赖
- 数据连接池
- mysql的jar包
- starter 天然支持beetlsql工具
 - 引入数据库配置文件`DBconfig` 
添加到add to watches-->选中右键add to watches在debug模式下打个断点在后面
### 模板
- 解决模板引擎不认识的问题(在springboot的配置文件内写)
`beetl.suffix=html` 
## 定义变量
### 临时变量
- 类js的变量定义的方法,使用`var` 来定义
`<% var a = "xxx";%>` 
```js
<%
var number = 1 ;
var str = "hello";
var now = date();
var isChecked = true;
var nullValue = null;
%>

number is ${number}~~tr is ${str},time is ${now}
${isChecked}
nullValue=${nullValue}
```
- 一行定义多个临时变量
```js
<%
var number = 1 str="hello" ,now = date();
%>

number is ${number},str is ${str},time is ${now}

```
- 输出的结果
```txt
number is 1,str is hello,time is Wed Jan 15 16:03:25 CST 2020
```

- 定义json变量
```js
<%
var map={name:'joel',age:18};
var list=['openclose'];
%>
${map['name']} = ${map.name} 
${list[0]} 
```

- 输出结果
`joel = joel open` 
- 定义模板变量
```js
<%
var bigNumber = 7777777777777.23;
var templateVar = {
%>
big number is ${bigNumber}
<%};%>
现在输出模板变量：
${templateVar}

```
- 输出结果
```txt
现在输出模板变量： big number is 7.77777777777723E112
```

## 全局变量

所有的整个模板都能访问的变量

```beetl
template.binding("list"~~ervice.getUserList());

//在模板里
<%
for(user in list){
%>
hello,${user.name};
<% } %> 
```


### 共享变量

### 模板变量
```js
print(c);
%>
```
## 属性引用
### 对象属性
```js
//伪模型
var user = {name:'lijz',wife:{name:'lucy',age:17}};
//模板
推荐： ${user.wife.name} 
不推荐：${user.wife["name"]} 
```
- 输出结果
`推荐： lucy 不推荐：lucy` 
### 数组或者列表
```js
//伪模型
var status =  [1,2,3];
//模板
${status[0]} 
${status.~size} 

```
- 输出结果
`1 3` 
### 集合
```js
//伪模型
var status =  [1,2,3];
//模板
${status[0]} 
```
- 输出结果
`1` 

### Map
```js
//伪模型
var map = {key1:'value1',key2:'value2'} ;
//模板
推荐 ${map["key1"]}
不推荐 ${map.key2} 

```
- 输出结果

`推荐 value1 不推荐 value2` 
??? from here until ???END lines may have been inserted/deleted
## 算数表达式
```js
<%
var a1 = 12;
var b1 = (a1+15)/3-2*a1;
var bc = -1-b1;
%>
${bc}
```

- 输出结果:

`14` -->(12+15)/3-2×12=-24=-15-->-1+15=14
## 逻辑表达式
### 逻辑表达式1
```js
<%
var a1 = 12;
var b1 = a1==12;
var b2 = a1!=12;
%>
${b1}
${b2}
```
- 输出结果

`true flase` 

### 逻辑表达式2
```js
<%
var a1 = "joeli";
var b1 = a1=="joelli";
%>
${b1}
```
- 输出结果

`flase` 
### 三元表达式
```js
<%
var a=1;
var b = a==1?"hi":"hey";
%>
${b}
```
- 输出结果: 

`hi` 

## 循环语句
### 循环数组或者List
```js
//伪模型
var userList=[{name:'lijz',age:18},{name:'lucy',age:16}];
//模板
<%
print("总共"+userList.~size+"<br>");
for(user in userList){
%>
${userLP.index}    ${user.name} <br>
<%}%>
```
- 输出结果
```js
总共2
1 lijz
2 lucy
```
### 循环Map
```js
//伪模型
var map={"001":{name:'lijz'}"002":{name:'lucy'}};"
//模板
<%
print("总共"+map.~size+"<br>");
for(user in map){
%>
${userLP.index}    ${user.key} ${user.value.name} <br>
<%}%>

```
- 输出结果
```txt
总共2
1 001 lijz
2 002 lucy
```


### 安全循环(不太理解)
```js
//伪模型
var list=null;
//模板
<%

for(user in list!){
%>
${user}   <br>
<%}%>
end
```
- 输出结果:
`end` 
### 循环控制
```js
//伪模板
var userList=[{name:'lijz',age:18},{name:'lucy',age:16}];
//模板
<%
for(user in userList){
if(user.age==18){
	print("it's lijz'");
	continue;
}else if(user.age==16){
	print("It's lucy");
	break;
}else{
	print("return ");
	return ;
}
}
print("end loop");
%>

```
- 输出结果
`it's lijz'It's lucyend loop`
### elsefor
```js
//伪模型
var userList=[];
//模板
<%
for(user in userList){
print(user.name);
}elsefor {
print("no data");
}

%>

```
- 运行结果 `no data` 

### for
```js

<%
for(var i=1;i<=10;i++){
print(i);
}

%>

```
- 输出结果




```beetl

for(blog in page.list) {

...
}
```

```beetl


}
```


### 其他循环语句
`while` 
<++>  

### 条件语句
<++> 
### 时间格式化
`${date,"yyyy-MM-dd"}` 


 
