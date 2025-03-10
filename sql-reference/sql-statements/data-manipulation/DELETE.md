# DELETE

## description

该语句用于按条件删除指定 table（base index） partition 中的数据。

该操作会同时删除和此 base index 相关的 rollup index 的数据。

语法：

```sql
DELETE FROM table_name [PARTITION partition_name]
WHERE
column_name1 op { value | value_list } [ AND column_name2 op { value | value_list } ...];
```

说明：

1) op 的可选类型包括：=, >, <, >=, <=, !=, in, not in
2) 只能指定 key 列上的条件。
3) 当选定的 key 列不存在于某个 rollup 中时，无法进行 delete。
4) 条件之间只能是“与”的关系。
若希望达成“或”的关系，需要将条件分写在两个 DELETE 语句中。
5) 如果为RANGE分区表，则必须指定 PARTITION。如果是单分区表，可以不指定。

注意：

该语句可能会降低执行后一段时间内的查询效率。
影响程度取决于语句中指定的删除条件的数量。
指定的条件越多，影响越大。

## example

1. 删除 my_table partition p1 中 k1 列值为 3 的数据行

    ```sql
    DELETE FROM my_table PARTITION p1
    WHERE k1 = 3;
    ```

2. 删除 my_table partition p1 中 k1 列值大于等于 3 且 k2 列值为 "abc" 的数据行

    ```sql
    DELETE FROM my_table PARTITION p1
    WHERE k1 >= 3 AND k2 = "abc";
    ```

## keyword

DELETE
