# 导出

---

## 导出影像

在GEE中，我们可以将影像导出为`GeoTiff`或者是`TFRecord`格式(后者是用于深度学习的)。下面将以一个案例来描述如何导出影像数据。

```js
// 设置导出的影像
var landsat = ee.Image('LANDSAT/LC08/C02/T1_TOA/LC08_123032_20140515').select(["B4","B3","B2"]);
// 导出的地理几何
var geo=ee.Geometry.Rectangle([[116.2621, 39.8412, 116.4849, 40.01236]]);
```

下一步，就是需要定义投影参数。我们可以用`crs`参数来确定坐标系，并采用`crsTransform`参数来精确指定像素格网。实际上`crsTransform`是一个仿射矩阵，其数据结构为:[xScale,xShearing,xTranslation,yScale,ySheaaring,yTranslation]，图像的像元尺寸将被`xScale`与`yScale`所确定。有一点值得注意的是，导出有一个`scale`参数，这个参数是暴力采样，而不是考虑投影变换矩阵运算，如果想要保证地图上的匹配，不建议采用`scale`参数。

```js
var pro=landsat.select("B2").projection().getInfo();
```

好啦，准备工作完成啦，现在就能导出啦：

<!-- tabs:start -->

### **Drive**

```js
Export.image.toDrive({
    image:landsat,
    description:"imageToDriveExample",
    crs:pro.crs,
    crsTransform:pro.transform,
    region:geo,
    fileFormat: 'GeoTIFF', // 这个是默认的，也可以写成TFRecord
    maxPixels: 1e9 // 指定最大像素范围，有些影像分辨率高了后会导致像素数量上溢
})
```

### **Cloud Storage**

```js
Export.image.toCloudStorage({
    image:landsat,
    description:"imageToCloudExample",
    bucket:"your-bucket-name",
    fileNamePrefix:"exampleExport",
    crs:pro.crs,
    crsTransform:pro.transform,
    region:geo,
    fileFormat:'GeoTIFF',
    maxPixels: 1e9
    
})
```

### **Asset**

```js
// 请注意，导出到Asset Manager时，可以指定采样金字塔策略
// 不同的策略将决定EE如何在低分辨率上显示影像

var band4=landsat.select("B4").rename("B4_Mean")
			.addBands(landsat.select("B4").rename("B4_Sample"))
			.addBands(landsat.select("B4").rename("B4_Max"));

Export.image.toAsset({
    image:band4,
    description:"imageToAssetExample",
    assetId:'exampleExport',
    crs:pro.crs,
    crsTransform:pro.transform,
    region:geo,
    pyramidingPolicy:{
        'b4_Mean':'mean',
        'b4_Sample':'sample',
        'b4_Max':'max'
    },
    fileFormat: 'GeoTIFF',
    maxPixels: 1e9
    // 如果不想每个波段都设置，可以采用如下的方式：
    /*
    pyramidingPolicy:{'.default':"sample"}
    */
});
```



<!-- tabs:end -->

注意，由于代码编辑器使用 "EPSG:3857 "CRS，因此请在导出时指定 "EPSG:3857 "CRS，以获得与代码编辑器地图中显示的投影相同的图像。

> 当导出的图像非常大时，可能会导出多个数据，如果导出的是GeoTIFF，那么影像就会做分割，并以baseFilename-yMin-xMin存储，这个名称表示了这张影像在坐标系中的位置。

## 导出表格和矢量数据

## 导出视频和动画

## 导出地图瓦片

