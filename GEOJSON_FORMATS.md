# 支持的 GeoJSON 格式

程序现在支持多种 GeoJSON 格式，会自动尝试不同的解析方式。

## 支持的格式

### 1. 标准 FeatureCollection (推荐)

最常见的 GeoJSON 格式：

```json
{
  "type": "FeatureCollection",
  "features": [
    {
      "type": "Feature",
      "properties": {},
      "geometry": {
        "type": "Polygon",
        "coordinates": [[[lng, lat], [lng, lat], ...]]
      }
    }
  ]
}
```

**示例文件**: `geojson/china.geojson`, `geojson/jiangsu.geojson`

### 2. OpenStreetMap Nominatim 格式

从 OpenStreetMap Nominatim API 导出的格式，JSON 数组包含 `geojson` 字段：

```json
[
  {
    "place_id": 276619318,
    "name": "几内亚",
    "display_name": "几内亚",
    "geojson": {
      "type": "Polygon",
      "coordinates": [[[lng, lat], [lng, lat], ...]]
    }
  }
]
```

**示例文件**: `geojson/几内亚.genjson`

**使用场景**: 从 Nominatim API 下载的边界数据
- API: `https://nominatim.openstreetmap.org/search?q=几内亚&format=json&polygon_geojson=1`

### 3. 单个 Geometry

直接的几何对象，没有 Feature 包装：

```json
{
  "type": "Polygon",
  "coordinates": [[[lng, lat], [lng, lat], ...]]
}
```

## 解析顺序

程序会按以下顺序尝试解析：

1. **FeatureCollection** - 标准 GeoJSON
2. **Nominatim Array** - OpenStreetMap 格式
3. **Geometry** - 单个几何对象

如果所有格式都失败，程序会报错并显示尝试过的格式。

## 配置示例

在 `conf.toml` 中使用这些文件：

```toml
[[lrs]]
  min = 1
  max = 20
  # 标准 GeoJSON
  geojson = "./geojson/china.geojson"

[[lrs]]
  min = 1
  max = 20
  # OpenStreetMap Nominatim 格式
  geojson = "./geojson/几内亚.genjson"
```

## 获取边界数据

### 从 OpenStreetMap Nominatim 获取

```bash
# 使用 curl 下载
curl "https://nominatim.openstreetmap.org/search?q=几内亚&format=json&polygon_geojson=1" -o "./geojson/几内亚.genjson"

# 或使用 wget
wget "https://nominatim.openstreetmap.org/search?q=几内亚&format=json&polygon_geojson=1" -O "./geojson/几内亚.genjson"
```

### 从 GeoJSON.io 创建

1. 访问 https://geojson.io
2. 在地图上绘制或导入边界
3. 导出为 GeoJSON
4. 保存到 `./geojson/` 目录

## 注意事项

1. **文件扩展名**: 支持 `.geojson`, `.json`, `.genjson` 等任意扩展名
2. **文件编码**: 建议使用 UTF-8 编码
3. **坐标系**: 必须使用 WGS84 (EPSG:4326)，即经纬度坐标
4. **坐标顺序**: [经度, 纬度] (longitude, latitude)

## 示例数据结构

### Nominatim 完整响应
```json
[
  {
    "place_id": 276619318,
    "licence": "Data © OpenStreetMap contributors, ODbL 1.0",
    "osm_type": "relation",
    "osm_id": 192778,
    "lat": "10.7226226",
    "lon": "-10.7083587",
    "category": "boundary",
    "type": "administrative",
    "name": "几内亚",
    "display_name": "几内亚",
    "boundingbox": ["7.1906045", "12.6756300", "-15.5680508", "-7.6379222"],
    "geojson": {
      "type": "Polygon",
      "coordinates": [[[-15.5680508, 10.6313833], ...]]
    }
  }
]
```

程序会自动提取 `geojson` 字段中的几何数据。
