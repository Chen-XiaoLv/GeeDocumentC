# 函数式编程概念

---

## 简介

Earth Engine使用并行处理系统来为大量的计算机提供计算服务，为了实现这一高并过程，Earth Engine使用了函数式语言常用的标准技术，如参照透明和惰性评估等，以显著优化和提升效率。

函数式编程有别于过程式编程的主要概念在于其无显著副作用(the absence of side effects),这意味着用户定义的函数并不依赖或更新于函数之外的数据，我们可以很好的对函数进行重构、复用，大大提升程序的可读性、可复用性乃至健壮性。

---

## For 循环

Earth Engine并不鼓励直接使用`for`循环，使用`map()`操作即可获得相同的结果，`map`将函数映射到容器中的每一个元素中，其可读性和稳定性要更高。实际上，当制定一个函数并应用`map`接口，系统会将处理工作分配给不同的机器(如果可用)。

```js
var myList=ee.List.sequence(1,10);
var computeSquares=function(number){
    return ee.Number(number).pow(2);
}

var squares=myList.map(computeSquares);
print(squares);
```

---

## If/ Else 条件

实际上Earth Engine也不推荐使用`ee.Algorithms.If()`算法，因为本身服务器有一定的延迟，可能会导致占用额外的计算资源。

这确实比较奇怪。

在案例中，官方文档给出了一种只计算奇数平方的方法：

```js
var getOddNumbers=function(number){
    n=ee.Number(number);
    var r=number.mod(2);
    return number.multiply(r);
}

var newList=myList.map(getOddNumbers);
// 移除零值
var oddN=newList.removeAll([0]);
var squares=oddN.map(computeSquares);
```

这种模式尤其适用于处理集合。如果要根据某些条件对集合应用不同的算法，首选的方法是先根据条件过滤集合，然后为每个子集使用不同的函数 map()。这样系统就能并行处理操作。例如下面一个案例，将影像中太阳高度角小于四十的值乘二：

```js
var collection=ee.ImageCollection("LANDSAT/LC08/C02/T1_TOA").filterDate('2018-01-01','2019-01-01');

var sub1=collection.filter(ee.Filter.lt("SUN_ELEVATION",40));
var sub2=collection.filter(ee.Filter.gte("SUN_ELEVATION",40));

// 应用方法
var p1=sub1.map(function(image){
    return image.multiply(2);
});
var p2=sub2;

// 合并图层
var fina=p1.merge(p2);
print("Original collection size",collection.size());
print("Processed collection size",fina.size());
```
> Earth Engine更希望看到用户按条件拆分、map后合并的操作


---

## 累计迭代

这个操作是将上一次迭代的结果作为下一次的输入，Earth Engine为此类任务提供了一个`iterate()`接口，这个接口与`map`并行处理不同，它是顺序执行，对于大型操作会比较慢。

我们现在用这个方法来编写一个斐波那契数列：

```js
var algorithm=function(cur,pre){
    pre=ee.List(pre);
    var n1=ee.Number(pre.get(-1));
    var n2=ee.Number(pre.get(-2));
    // 注意，虽然List与Number都是add接口
    // 但是结果完全不同
    return pre.add(n1.add(n2));
};

var numberIteration=ee.List.repeat(1,10);
var start=[0,1];
var sequence=numberIteration.iterate(algorithm,start);
```

> 我个人的理解是，首先是迭代方法上，需要传入一个当前位置参数`cur`，一个迭代去向参数（当然这个有没有参数不重要），返回的值如果是一个引用类型，那么就会在原始位置上做修改，而这里的迭代次数则是由`cur`做控制，但在实际计算中，`cur`并不参与反应。

按照这个理解，我们可以提取出模板：

```js
var f=function(cur,pre){
    return pre;
};

var n=6;
var pre=[0];
var nIter=ee.List.repeat(1,6);
var res=nIter.iterate(f,pre);

```

