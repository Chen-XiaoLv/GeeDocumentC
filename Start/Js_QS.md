## 1. Google在线代码编辑器

> Code Editor是GEE JavaScript API的网络集成开发环境，也就是我们编写JS程序的地方

![](Js_QS/image-20230720153301871.png)

打开Google Earth Engine，点击`Platform->Code Editor`选项卡，进入代码编辑器。



![image-20230720153516397](./Js_QS/image-20230720153516397.png)

在左侧，我们可以看到`Docs`选项卡，该选项卡提供了所有方法的帮助文档。

![image-20230720153716405](./Js_QS/image-20230720153716405.png)

`Scripts`是google托管的git仓库：

通过`New`按钮，可以创建自己的文档。

![image-20230720153927577](Js_QS/image-20230720153927577.png)

`Assets`选项卡允许你上传自己的文件。

![image-20230720154251623](Js_QS/image-20230720154251623.png)

在中间窗体，`Get Link`功能可以获取脚本的统一资源定位符，方便代码的共享。

![image-20230720154411812](Js_QS/image-20230720154411812.png)

中间窗体上方的搜索栏允许检索数据集，对于找到的数据集可以直接导入：

![image-20230720154719909](Js_QS/image-20230720154719909.png)

点击导入后的代码图标，即可获得导入生成代码。

![image-20230720154804507](Js_QS/image-20230720154804507.png)

在这里可以直接删除。

![image-20230720154928182](Js_QS/image-20230720154928182.png)

加载地图后，可以打开图层管理器，调整可视化参数等：

![image-20230720155342635](Js_QS/image-20230720155342635.png)

`inspector`选项卡根据鼠标点击位置获取地图信息，可以通过十字丝互动的方式检查地图中的结果。

![image-20230720155452830](Js_QS/image-20230720155452830.png)

`console`选项卡跟浏览器里的`console`相同，用来处理JS输出和变量。

![image-20230720155641908](Js_QS/image-20230720155641908.png)

`Task`选项卡用来处理导入导出任务

![image-20230720155823941](Js_QS/image-20230720155823941.png)

`Profiler`选项卡会显示特定算法和计算所消耗的资源，如时间、CPU、内存等，可以通过按住`Ctrl+Alt+Enter`打开。

![image-20230720162625994](Js_QS/image-20230720162625994.png)

几何工具可以在地图上绘制几何图形。

![image-20230720162727529](Js_QS/image-20230720162727529.png)

可以选择添加新图层进行绘制。

![image-20230720162810420](Js_QS/image-20230720162810420.png)

---

## 2. EE数据结构

在EE中，最基本的两种地理数据结构是`Image`和`Feature`，分别对应栅格和矢量数据。在这之上，还有`ImageCollection`和`FeatureCollection`对应栅格数据集与矢量数据集。

`Image`:star: 数据由波段和属性字典构成：

![image-20230720163744160](Js_QS/image-20230720163744160.png)

`Feature`:star2: 则是由`Geometry`对象和属性字典构成。

![image-20230720164316536](Js_QS/image-20230720164316536.png)

其他的对象还有`Dictionary`,`List`,`Array`,`Date`,`Number`,`String`等。

---

## 3. 数据搜索

如果我们想导入数据集，那么可以在搜索栏上直接检索我们所需要的数据：

![image-20230720165518064](Js_QS/image-20230720165518064.png)

或者是在Earth Engine Data Catalog中检索:

![image-20230720165602580](Js_QS/image-20230720165602580.png)

---

## 4. 导入数据

导入数据相对简单，我们只需要知道所有的数据都将被存放在`Map`对象的`Layer`容器中。例如：

```js
// 从档案中选择数据
var image = ee.Image('LANDSAT/LC08/C02/T1_TOA/LC08_044034_20140318');

// 定义数据的可视化参数
var vizParams = {bands: ['B5', 'B4', 'B3'], min: 0.02, max: 0.4, gamma: 1.3};

// 设置地图中心和初始缩放等级
Map.centerObject(image, 9);

// 添加地图，可视化配置，图层名
Map.addLayer(image, vizParams, 'Landsat 8 false color');
```

![image-20230720170203370](Js_QS/image-20230720170203370.png)

对于矢量要素的导入也是如此。

```js
var counties = ee.FeatureCollection('TIGER/2018/Counties');
Map.addLayer(counties, {}, 'counties');
```

## 5. 数据处理

### 5.1 过滤与排序

> GEE 提供了多种数据筛选的接口，包括按照空间、时间、属性等进行选取

为了限制结果的数量，通常有必要按空间或时间对集合进行过滤。

例如，我们想选取Landsat 8在旧金山市的无云影像图，可以进行如下操作：

+ 创建地理定位点，定位可以通过`Inspector`选项卡来获取

  ![image-20230720171041233](Js_QS/image-20230720171041233.png)

  ```js
  var point=ee.Geometry.Point(-122.467,37.889);
  ```

+ 选择需要的时间段

  ```js
  var s=ee.Date('2014-06-01');
  var e=ee.Date('2014-10-01');
  ```

+ 导入数据，并通过`Filter`方法进行处理，这里通过`sort`方法对云量属性进行降序排序

  ```js
  var fc=ee.ImageCollection("LANDSAT/LC08/C02/T1_TOA")
  	.filterBounds(point) // 通过空间Geometry选取
  	.filterDate(s,e) // 通过起止时间选取
  	.sort("CLOUD_COVER",true); // 按照属性排序，后面那个true表示降序
  ```

+ 选择云量最多的：

  ```js
  var fir=fc.first();
  ```

  看看效果？

  ![image-20230720171803768](Js_QS/image-20230720171803768.png)

  **进阶用法**

  采用`filter()`和`ee.Filter`方法进行。

  ```js
  // 添加要素集
  var featureCollection = ee.FeatureCollection('TIGER/2016/States');
  
  // 过滤对象
  var filteredFC = featureCollection.filter(ee.Filter.eq('NAME', 'California'));
  
  // 显示
  Map.setCenter(-119.604, 37.798, 6);
  Map.addLayer(filteredFC, {}, 'California');
  ```

  ---

### 5.2 波段运算

就是跟`ArcGIS`里面的栅格计算器一样，本质上就是一些矩阵做元素位上的数字运算。

举个简单的栗子，计算`NDVI`

```js
// normalizedDifference方法是封装在EE中的NDVI计算方法
var getNDVI = function(image) {
  return image.normalizedDifference(['B4', 'B3']);
};

// 载入两个影像
var image1 = ee.Image('LANDSAT/LT05/C02/T1_TOA/LT05_044034_19900604');
var image2 = ee.Image('LANDSAT/LT05/C02/T1_TOA/LT05_044034_20100611');

// 从不同景影像里计算NDVI
var ndvi1 = getNDVI(image1);
var ndvi2 = getNDVI(image2);

// 计算NDVI差值
var ndviDifference = ndvi2.subtract(ndvi1);
```

如果不用系统定义的NDVI方法，我们也可以自己写一个。

```js
var getNDVI=function(img){
    var B4=img.select('B4');
    var B3=img.select('B3');
    return B4.subtract(B3).divide(B4.add(B3));
};
```

---

### 5.3 Mapping方法



### 5.4 Reducing方法

### 5.5 Masking方法



