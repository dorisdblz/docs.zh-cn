# ST_Distance_Sphere

## description

### Syntax

`DOUBLE ST_Distance_Sphere(DOUBLE x_lng, DOUBLE x_lat, DOUBLE y_lng, DOUBLE x_lat)`

计算地球两点之间的球面距离，单位为 米。传入的参数分别为X点的经度，X点的纬度，Y点的经度，Y点的纬度。

## example

```Plain Text
MySQL > select st_distance_sphere(116.35620117, 39.939093, 116.4274406433, 39.9020987219);
+----------------------------------------------------------------------------+
| st_distance_sphere(116.35620117, 39.939093, 116.4274406433, 39.9020987219) |
+----------------------------------------------------------------------------+
|                                                         7336.9135549995917 |
+----------------------------------------------------------------------------+
```

## keyword

ST_DISTANCE_SPHERE,ST,DISTANCE,SPHERE
