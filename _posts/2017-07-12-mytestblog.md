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

�����������Ƕ�Score���Ȩ�أ���ͬ��ֵ����ͬ��Ȩ�أ���Ȩ�������������֡�����Ǳ�̵Ļ��϶������׽���������ŵ�SQL�����Ͳ����ú����׽���ˡ���˼·��˵������ⷨ�ܼ򵥣�ֻҪ�ȶ�score���������ǰscore��ֵ��ǰһ��score��ֵ��Ȩ�ؾ�����1�����������ǰһ��score�ͱ���ԭֵ����Ѻ�ߵ������������ǵĹ�����ʵ���Ǹ�score����������š���ʱ���ǵĲ����ͱ�ü��ˡ�

``` sql

select (@i:=@i+1) as i,table_name.* from table_name,(select @i:=0) as it
```
�����Ǵ���������ҵ�һ���Զ������ŵ���䡣���ǹ۲������SQL��䣬���������б�����SQL���Ľṹ��
���ȰѸ�����asȥ����asֻ�Ǹ���ѯ���ȡ��һ���������˴����ǲ��ؿ���as��
```
select score , (@i:=@i+1) from Scores,(select @i:=0) init order by score desc
```
�������������ǣ�
``` json
{"headers": ["score", "(@i:=@i+1)"], "values": [[4.00, 1.0], [4.00, 2.0], [3.85, 3.0], [3.65, 4.0], [3.65, 5.0], [3.50, 6.0]]}
```

��������Ľ���������Ѿ�ʵ����һ���Ҫ���ˡ�ֻ�������ڽ���е����û�����㡰��ͬ��ֵ����ͬ��Ȩ�ء����Ҫ�󡣴�ʱ������Ҫ������һ������@pre��������һ��score��
������Ȩ��ʱ���@pre����ǰscore��ȣ���@i����ԭֵ������˵+0���������1.
``` sql
select Score, @i := @i + (@pre <> ( @pre := Score)) Rank from Scores, (select @i := 0,@pre := -1) init order by Score desc
```

������Ҫע����Ǳ���@pre������������Ȩ�������ж��Ǹ���@pre��ǰһ��score��ֵ���Ƿ���ڵ�ǰscore��ֵ�����@pre�ĳ�ʼֵΪ����0�������Ϳ����жϳ������@pre�ĳ�ʼֵ����ΪС��0������