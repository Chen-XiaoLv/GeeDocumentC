# JavaScript基本语法

> 本教程主要介绍编写GEE代码时可能需要用到的JavaScript知识



## 简介

JavaScript(以下简称`js`)是一种具有函数优先的`轻量级`、`解释型`(即时编译型)的编程语言，作为一种基于`原型编程`、`多范式`的`动态`脚本语言，`js`被广泛运用于Web开发中。

`js`是1995年被设计而出，其与`Java`没有太多的关系。

## 变量

`Java`作为弱类型语言，只需要通过`var`关键字即可声明一个变量。例如：

```js
var hi="hello world";
document.getElementById('demo').innerHTML=hi;
```

实际上，`var`关键字可以先调用，后声明，这是由于`js`会做一个变量提升，将所有声明语句调用到最前。例如：

```js
x=5;
document.getElementById('demo').innerHTML=5;
var x;
```

> 值得注意的是，变量提升只会提升定义，而不会提升赋值。例如：
>
> ```js
> var y=5;
> print(y+x);
> var x=5;
> ```
>
> 就会报错。

如果不想被变量提升影响程序的可读性，可以采用严格模式或者`let`关键字。

与`var`关键字不同，`let`关键字不支持变量提升，其作用域仅仅在当前语句块(`var`可作为全局变量)

## 数据类型

`js`的数据类型可分为`基本类型(值类型)`和`引用类型(对象类型)`两种。

|     基本类型     |   引用类型    |
| :--------------: | :-----------: |
|  字符串 String   |  对象 Object  |
|   数字 Number    |  数组 Array   |
|   布尔 Boolean   | 函数 Function |
|     空 Null      |   日期 Date   |
| 未定义 Undefined |   列表 List   |
|   符号 Symbol    |  正则 RegExp  |

> 如果想查看一个变量的类型，可以通过`typeof`关键字进行。例如：
>
> ```js
> var person={name:"Han Meimei",age:18}
> typeof person
> ```

By the way, `js`的字典中`key`是不需要加上`""`的。

## 函数

通过`function`关键字来定义函数，其基本格式如下：

```js
var f=function(argument){
    // your expression
}
```

在函数中被声明的变量是局部变量，只能够在函数内部进行访问，下面举一个函数的简单栗子。

```html
<script>
function A(){
    alert("hey");
}    
</script>

<body>
    // 直接使用函数名
    <button onclick="A()">
        Click Me
    </button>
</body>
```

在GEE中，函数一般是被定义为一个变量后调用：

```js
var getNDVI=function(img){
    return img.normalizedDifferent(["B4"],["B3"]);
};
var ndvi=getNDVI(image);
```

## 语句与关键字

一条规范的`js`语句，需要以分号结尾，基本格式如下：

```js
var person="a"+"b";
```

以下是`js`的主要关键字：

|     语句     |         描述          |
| :----------: | :-------------------: |
|    break     |     中断当前循环      |
|   continue   | 跳过当前循环后续语句  |
|     try      |     执行一段语句      |
|    catch     |    在try出错时执行    |
|   finally    | try语句块最终执行语句 |
|     for      |       循环语句        |
|  for ... in  |    遍历数组或对象     |
| do ... while |         循环          |
|    while     |         循环          |
| if ... else  |         选择          |
| switch case  |      多分支选择       |
|    throw     |       抛出错误        |
|    return    |      退出并返回       |
|   function   |       定义函数        |
|     var      |       声明变量        |
|     let      |     声明局部变量      |

## 运算符

### 算术运算符

| 运算符 |                     描述                     |
| :----: | :------------------------------------------: |
|   +    |                      加                      |
|   -    |                      减                      |
|   *    |                      乘                      |
|   /    |                      除                      |
|   %    |                     取余                     |
|   ++   | 自加(根据位置不同可以先加后运算或先运算后加) |
|   --   |                  自减(一样)                  |

### 赋值运算符

| 运算符 |    描述     |
| :----: | :---------: |
|   =    |    赋值     |
|   +=   | 等同于A=A+B |
|   -=   |             |
|   /=   |             |
|   *=   |             |
|   %=   |             |

### 逻辑运算符

| 运算符 |    描述    |
| :----: | :--------: |
|   ==   |   值等于   |
| $===$  |  绝对等于  |
|  $!=$  |  值不等于  |
| $!===$ | 绝对不等于 |
|   >    |    大于    |
|   <    |    小于    |
|  $>=$  | 大于或等于 |
|  $<=$  | 小于或等于 |

### 比较运算符

| 运算符 |  描述  |
| :----: | :----: |
|   &&   | 逻辑与 |
|  \|\|  | 逻辑或 |
|   !    | 逻辑非 |

## 条件与循环

### If Else

```js
if(condition){
    条件为true
}else{
    条件为false
}
```

```js
if(condition1){
    // 语句1
}else if(condition2){
    // 语句2
}else{
	// 语句3    
}
```

### switch语句

```js
switch(n){
    case con1:
        // 语句1
    case con2:
        // 语句2
    default:
        // 语句3
}
```

### For循环

```js
for(var i=0;i<6;i++){   
}
```

这里的变量也可以在外头定义：

```js
var i=1;
for(;i<6;i++){};
```

或者我们可以把迭代过程放到别的地方：

```js
var i=0;
for(;i<6;){
    i+=1;
};
```

### for in 循环

一般用在对象或数组中

```js
var person={fname:"bill",lname:'gates',age:56};
for(x in person){
    text=""+person[x]
};
```

### while 和 do while循环

```js
while(条件){
    
}

do{}
while(条件)
```

### break和continue

`break`用来跳出循环，`continue`用来跳过当前循环

事实上，我们可以通过标签来选择性跳过。例如：

```js
cars=["BMW","Volvo","Saab","Ford"];
list: 
{
    document.write(cars[0] + "<br>"); 
    document.write(cars[1] + "<br>"); 
    document.write(cars[2] + "<br>"); 
    break list;
    document.write(cars[3] + "<br>"); 
    document.write(cars[4] + "<br>"); 
    document.write(cars[5] + "<br>"); 
}
```

---

## 错误

可以通过`throw`来抛出一个错误。而对于错误的测试则是在`try`语句块中：

```js
try{
    // 执行语句
}catch(e){
    // e为错误信息
}finally{
    // 最终执行语句
}
```

举个例子

```js
function myFunction() {
    var message, x;
    message = document.getElementById("message");
    message.innerHTML = "";
    x = document.getElementById("demo").value;
    try { 
        if(x == "")  throw "值为空";
        if(isNaN(x)) throw "不是数字";
        x = Number(x);
        if(x < 5)    throw "太小";
        if(x > 10)   throw "太大";
    }
    catch(err) {
        message.innerHTML = "错误: " + err;
    }
}
```

## 类

`js`类对象需要有一个构造函数`constructor`。

```js
class ClassName{
    constructor(){}
}
```

可以为构造函数传入参数，并通过`this`关键字存储到类中。

```js
class person{
    constructor(name,age){
        this.name=name;
        this.age=age;
    };
};
```

可以为类添加方法,此时不需要再使用`function`关键字了

```js
class person{
    constructor(name,year){
        this.name=name;
        this.year=year;
    };
    age(){
        let date=new Date();
        return date.getFullYear()-this.year;
    }
};
```

想要调用类的属性和方法也很简单，通过`class.properities`调用就行。

类可以继承自其他超类，通过`extends`方法进行继承，并通过`super`方法调用父类的构造方法，这样使得该类可以直接调用父类定义好的方法和属性。

```js
class Animal{
    constructor(){};
    eat(x){
        return "It can eat"+x;
    }
};

class Dog extends Animal{
    constructor(){
        super(Animal);
    };
	show(){
        return "?";
    }
}
```

---





