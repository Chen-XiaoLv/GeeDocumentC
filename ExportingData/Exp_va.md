# 导出

---

## 导出视频与动画

在GEE中，我们可以根据`ImageCollection`中图像的顺序，将其导出为视频。

> 视频需要3通道8bit，且导出时间较长，导出格式为MP4

下面这个例子将导出二十年的`Landsat`图像集合：

```js
var collection=ee.ImageCollection('LANDSAT/LT05/C02/T1_TOA')
			// 旧金山大街
			.filter(ee.Filter.eq('WRS_PATH',44))
			.filter(ee.Filter.eq("WRS_ROW",34))
			// 过滤云区
			.filter(ee.Filter.lt('CLOUD_COVER',30))
			// 获得时间序列
			.filterDate('1991-01-01','2011-12-30')
			// 创建 8-bit RBG图像
			.map(function(image){
                return image.visualize({bands:['B4',"B3","B2"],min:0.02,max:0.35});
            });

var polygon=ee.Geometry.Rectangle([-122.7286,37.6325,-122.0241,373.9592]);
```

<!-- tabs:start -->

### **Drive**

```js
Export.video.toDrive({
    collection:collection,
    description:'sfVideoExample',
    dimensions:720,// 清晰度
    framesPerSecond:12, // 帧率
    region:polygon
})
```

### 

### **Cloud Storage**

```js
Export.video.toCloudStorage({
  collection: collection,
  description: 'sfVideoExampleToCloud',
  bucket: 'your-bucket-name',
  dimensions: 720,
  framesPerSecond: 12,
  region: polygon
});
```



<!-- tabs:end -->