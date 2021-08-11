# strright

## description

### Syntax

`VARCHAR strright(VARCHAR str,INT len)`

它返回具有指定长度的字符串的右边部分, 长度的单位为utf8字符

## example

```Plain Text
MySQL > select strright("Hello doris",5);
+--------------------------+
|strright('Hello doris', 5)|
+--------------------------+
| doris                    |
+--------------------------+
```

## keyword

STRRIGHT
