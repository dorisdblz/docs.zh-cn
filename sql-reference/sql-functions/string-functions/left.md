# left

## description

### Syntax

`VARCHAR left(VARCHAR str)`

它返回具有指定长度的字符串的左边部分, 长度的单位为utf8字符

## example

```Plain Text
MySQL > select left("Hello doris",5);
+------------------------+
| left('Hello doris', 5) |
+------------------------+
| Hello                  |
+------------------------+
```

## keyword

LEFT
