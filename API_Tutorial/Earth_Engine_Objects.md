# Earth Engine对象与方法

---

## *Strings*

我们可以使用`ee.String()`容器向GEE发送字符串。例如：

```js
var aString="To the cloud!";
var eeString=ee.String(aString);
print("Where to?",ee.String);
```

在这里，`print`的第一个参数是本地的字符串，而第二个参数则会被发送至服务器端进行解析和处理，最后返回信息。

---

## *Numbers*

通过`ee.Number()`可以在服务器上创建一个数值对象，例如，我们使用Js方法中的`Math.E`向服务器创建一个常量：

```js
var serverNumber=ee.Number(Math.E);
print("e=",serverNumber);
```

`ee.Number()`与`ee.String()`都是一个构造函数，构造函数将其参数放入一个容器中(类)，并以可以在本地操作的EE对象容器进行返回。

> 任意的以`ee`开始的构造函数都将返回一个EE对象。

---

## *EE对象方法*

注意到当你创建了一个EE对象后，必须使用EE方法而不是原始的Js方法去处理它。

```js
var logE=serverNumber.log();
print('log(e)=',logE);
```

> EE方法返回的还是EE对象

---

## *Lists*

可以将原始的Js迭代器作为构造参数传入`ee.List`构造器中，这样就能得到一个EE对象。当然也可以采用GEE提供的服务器端的一些边界方法，例如：

```js
var eeList=ee.List([1,2,3,4,5]);
var sequence=ee.List.sequence(1,5);
print('Sequence:',sequence);
```

实际上当`ee.List`对象存在于服务器上后，就可以使用EE提供的函数对其进行迭代。例如，想要获取其中的元素，可以用：

```js
var eeList=ee.List([1,2,3,4,5]);
eeList=eeList.set(0,8); // 需要获取返回值
print("Value at index 2:",eeList.get(0));
```

---

## *Casting*

有时候，EE并不知道一个方法中返回对象的类型，譬如一个变量`value`返回的是数值类型，但如果作为一个方法的返回值时，EE并不能很好的分析其类型。

当我们想要对其使用某些数值型方法时，可能就会报错。例如，使用`add()`方法时，可能会得到如下结果：

> value.add is not a function

为了避免这种情况，我们可以对其进行一定的转换。

```js
print("No error:",ee.Number(value).add(3));
```

虽然没有什么实质性的改变，但是跟服务器阐明了，这是个`ee.Number`类型的数据。

---

## *Dictionaries*

可以从Js对象中构造EE的字典对象，与之前的数据结构类似。

```js
// Make a Dictionary on the server.
var dictionary = ee.Dictionary({
  e: Math.E,
  pi: Math.PI,
  phi: (1 + Math.sqrt(5)) / 2
});

// Get some values from the dictionary.
print('Euler:', dictionary.get('e'));
print('Pi:', dictionary.get('pi'));
print('Golden ratio:', dictionary.get('phi'));

// Get all the keys:
print('Keys: ', dictionary.keys());

// set value
dictionary=dictionary.set('e',67);
print('Keys: ', dictionary.get('e'));
```

值得注意的是，`get(key)`将会返回与`key`相关联的值，这个值可以是任意类型的，当你想对其做一些非`print`操作时，记得要先做一个转换。另外，`ee.keys()`将返回一个`ee.List`对象。

---

## *Dates*

这个对象用来表征时间。

```js
var date=ee.Date('2015-12-31');
print('Date:',date);

var now=Date.now();
print("Milliseconds since January 1, 1970",now);

var eeNow=ee.Date(now);
print("Now:",eeNow);
```

> `Dates`在按时间筛选数据容器时非常重要，一般伴随着方法`filterDate()`进行使用

我们可以从年月日的形式创建一个Date对象，这种创建方式可以使用位置参数或是定名参数直接传入：

```js
var aDate=ee.Date.fromYMD(2017,1,13);
print('aDate:',aDate);

var theDate=ee.Date.fromYMD({
    day:13,
    month:1,
    year:2017
});
print("theDate:",theDate);
```

