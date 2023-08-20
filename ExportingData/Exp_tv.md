# 导出

---

## 导出表格与矢量数据

> 在GEE中，我们可以将一个`FeatureCollection`对象导出为`csv/shp/kml/geoJson/TFRecord`等格式

`FeatureCollection`涵盖了地理实体和关系表，作为关系表，在导出时不应该配置地理几何。

此外，在导出数据时，需要遵循一些约定：

+ `KML`:需要将所有几何转到未投影的`WGS84`坐标系
+ `SHP`:导出shapefile时候，地理几何实体的类型必须相等，而且需要满足尺寸限制，列名会被截断为 10 个字符或更少的字符，并且不能创建重复的列名。

> 如果需要控制导出几何图形的精度，可在要导出的集合上使用 map() 函数： map(function(f) { return f.transform(targetProj, maxErr); })

<!-- tabs:start -->

### **Drive_KML**

```js
var features=ee.FeatureCollection([
    ee.Feature(ee.Geometry.Point(30.41,59.99),{name:"V"}),
    ee.Feature(ee.Geometry.Point(-73.96,40.78),{name:"T"}),
    ee.Feature(ee.Geometry.Point(6.408,50.801),{name:"D"})]
);

Export.table.toDrive({
    collection:features,
    description:"vectorsToDriveExample",
    fileFormat:"KML"
});
```

### **Drive_CSV**

```js
var img=ee.Image('LANDSAT/LC08/C02/T1_TOA/LC08_044034_20140318');
var pro=img.select('B2').projection().getInfo();
var geo=ee.Geometry.Rectangle(-122.2806,37.1209,-122.0554,37.2413);
var means=img.reduceRegion({
    geometry:geo,
    reducer:ee.Reducer.mean(),
    crs:pro.crs,
    crsTransform:pro.crsTransform
});

// 创建不带地理实体的要素
var feature=ee.FeatureCollection([ee.Feature(null,means)]);
Export.table.toDrive({
    collection:feature,
    description:'exportTableExample',
    fileFormat:"CSV"
}
);
```

### **Cloud Storage**

```js
Export.table.toCloudStorage({
    collection:feature,
    description:"vTc",
    bucket:'your-bucket-name',
    fileNamePrefix:'exampleTableExport',
    fileFormat:"KML"
})
```

### **Asset**

```js
Export.table.toAsset({
    collection:features,
    description:'exportTotableAssetExample',
    assetId;'exampleAssetId'
})
```



<!-- tabs:end -->

