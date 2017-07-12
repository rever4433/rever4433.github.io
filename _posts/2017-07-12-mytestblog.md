### Description:
> Write a SQL query to rank scores. If there is a tie between two scores, both should have the same ranking. Note that after a tie, the next ranking number should be the next consecutive integer value. In other words, there should be no "holes" between ranks.
>
| Id | Score |
|---|---|
| 1 | 3.50 |
| 2 | 3.65 |
| 3 | 4.00 |
| 4 | 3.85 |
| 5 | 4.00 |
| 6 | 3.65 |
For example, given the above?Scores
?table, your query should generate the following report (order by highest score):
>
| Score | Rank |
|-------|------| 
|4.00 | 1 |
| 4.00 | 1 |
| 3.85 | 2 |
|?3.65 | 3 |
| 3.65 | 3 |
| 3.50 | 4 |

该问题让我们对Score添加权重，相同的值有相同的权重，且权重是连续的数字。如果是编程的话肯定很容易解决，不过放到SQL语句里就不见得很容易解决了。从思路来说，这个解法很简单，只要先对score排序，如果当前score的值比前一个score的值大，权重就自增1。如果不大于前一个score就保持原值如果把后边的条件化简，我们的工作其实就是给score排序后加上序号。这时我们的操作就变得简单了。

``` sql

select (@i:=@i+1) as i,table_name.* from table_name,(select @i:=0) as it
```
上述是从网上随便找到一个自动添加序号的语句。我们观察上面的SQL语句，并分析含有变量的SQL语句的结构。
首先把干扰项as去掉，as只是给查询结果取了一个别名，此处我们不必考虑as。
```
select score , (@i:=@i+1) from Scores,(select @i:=0) init order by score desc
```
这样的输出结果是：
``` json
{"headers": ["score", "(@i:=@i+1)"], "values": [[4.00, 1.0], [4.00, 2.0], [3.85, 3.0], [3.65, 4.0], [3.65, 5.0], [3.50, 6.0]]}
```

看看上面的结果，我们已经实现了一半的要求了。只不过现在结果中的序号没有满足“相同的值有相同的权重”这个要求。此时我们需要引入另一个变量@pre来保存上一个score。
当计算权重时如果@pre跟当前score相等，则@i保持原值（或者说+0），否则加1.
``` sql
select Score, @i := @i + (@pre <> ( @pre := Score)) Rank from Scores, (select @i := 0,@pre := -1) init order by Score desc
```

这里需要注意的是变量@pre。由于我们在权重那里判断是根据@pre（前一个score的值）是否等于当前score的值，如果@pre的初始值为大于0的数，就可能判断出错。最好@pre的初始值设置为小于0的数。