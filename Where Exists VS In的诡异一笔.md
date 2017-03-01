

# Where Exists VS In的诡异一笔

这几天在调试Oracle存储过程时，发现了一个`Where Exists`和`In`的诡异问题……

使用相同输出的`where exists`与`in`，结果集竟然不一样。正因如此，导致了执行结果的不准确。

具体表象为：

使用`where exists`语句得到的数据集是0条，但使用相同输出的`in`语句，得到了正确的数据集，如3条。

幸好经过步步调试，精确定位到了出现问题的异常代码片段位于`where exists`语句块，果断将其替换为`in`语句，结果输出正常，诡异问题消失了。

通常情况下，`where exists`、`in`、`join`都可以达到同样的输出效果，可是我一般会选用`where exists`而非`in`。原因就在于`in`不会使用到索引，在某些情况下效率会明显低于`where exists`。比如普遍认知的二者的选用场景：

`where exists`: 适用于subquery语句中数据量很大的场景；效率更高。

`in`与`where exists`: 都适用于subquery语句中数据量较小的场景；二者效率相当。

但没想到，这次出现的问题是项目中最无法接受的正确性问题。看来以后在选用二者时，要多长个心眼了。

这个问题比较奇怪，目前我还没有时间深究，估计要具体了解下`where exists`的机制才行。先不求甚解下，记录在案。顺便把二者的1个区别也记录一下：

``` --创建一张测试用表
--创建一张测试用表
create table testFromWhereExistsAndIn
(
ID INT NULL,
NAME VARCHAR2(64) NULL
);
--插入一些测试用数据
INSERT INTO testFromWhereExistsAndIn (ID, NAME) VALUES ('1', 'RIDLEY SCOTT');
INSERT INTO testFromWhereExistsAndIn (ID, NAME) VALUES ('1', 'TOM FORD');
INSERT INTO testFromWhereExistsAndIn (ID, NAME) VALUES ('1', 'TOM HARDY');
COMMIT;
--使用where exists的查询
select * from testFromWhereExistsAndIn
where exists (select null from testFromWhereExistsAndIn);
--使用in的查询
select * from testFromWhereExistsAndIn
where NAME in (select null from testFromWhereExistsAndIn);
```

结果集1：

```ID	NAME
ID	NAME
1	RIDLEY SCOTT
1	TOM FORD
1	TOM HARDY 
```

结果集2：

```ID	NAME
ID	NAME

```

**需要注意一下NULL值**。