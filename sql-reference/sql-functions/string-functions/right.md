# right

## description

### Syntax

`VARCHAR right(VARCHAR str)`

它返回具有指定长度的字符串的右边部分, 长度的单位为utf8字符

## example

```Plain Text
MySQL > select right("Hello doris",5);
+-------------------------+
| right('Hello doris', 5) |
+-------------------------+
| doris                   |
+-------------------------+
```

## keyword

RIGHT
