# array_length

## description

### Syntax

`array_length(any_array)`

返回数组中元素个数，结果类型是INT，如果参数是NULL，结果也是NULL.

## example

```plain text
mysql> select array_length([1,2,3]);
+-----------------------+
| array_length([1,2,3]) |
+-----------------------+
|                     3 |
+-----------------------+
1 row in set (0.00 sec)

mysql> select array_length([[1,2], [3,4]]);
+-----------------------------+
| array_length([[1,2],[3,4]]) |
+-----------------------------+
|                           2 |
+-----------------------------+
1 row in set (0.01 sec)
```

## keyword

ARRAY_LENGTH,ARRAY
