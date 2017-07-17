---
layout:     post
title:      "MySQL中变量的用法"
subtitle:   "LeetCode 178. Rank Scores"
date:       2017-07-12 18:00:00
author:     "gaoyakang"
header-img: "img/post-bg-05.jpg"
---


### Description:
> Write a SQL query to rank scores. If there is a tie between two scores, both should have the same ranking. Note that after a tie, the next ranking number should be the next consecutive integer value. In other words, there should be no "holes" between ranks.
>

| Id| Score|
|---|------|
| 1 | 3.50 |
| 2 | 3.65 |
| 3 | 4.00 |
| 4 | 3.85 |
| 5 | 4.00 |
| 6 | 3.65 |

For example, given the above Scores
 table, your query should generate the following report (order by highest score):
>

| Score | Rank |
|-------|------| 
| 4.00  | 1    |
| 4.00  | 1    |
| 3.85  | 2    |
| 3.65  | 3	   |
| 3.65  | 3    |
| 3.50  | 4	   |

&#x8BE5;&#x95EE;&#x9898;&#x8BA9;&#x6211;&#x4EEC;&#x5BF9;Score&#x6DFB;&#x52A0;&#x6743;&#x91CD;&#xFF0C;&#x76F8;&#x540C;&#x7684;&#x503C;&#x6709;&#x76F8;&#x540C;&#x7684;&#x6743;&#x91CD;&#xFF0C;&#x4E14;&#x6743;&#x91CD;&#x662F;&#x8FDE;&#x7EED;&#x7684;&#x6570;&#x5B57;&#x3002;&#x5982;&#x679C;&#x662F;&#x7F16;&#x7A0B;&#x7684;&#x8BDD;&#x80AF;&#x5B9A;&#x5F88;&#x5BB9;&#x6613;&#x89E3;&#x51B3;&#xFF0C;&#x4E0D;&#x8FC7;&#x653E;&#x5230;SQL&#x8BED;&#x53E5;&#x91CC;&#x5C31;&#x4E0D;&#x89C1;&#x5F97;&#x5F88;&#x5BB9;&#x6613;&#x89E3;&#x51B3;&#x4E86;&#x3002;&#x4ECE;&#x601D;&#x8DEF;&#x6765;&#x8BF4;&#xFF0C;&#x8FD9;&#x4E2A;&#x89E3;&#x6CD5;&#x5F88;&#x7B80;&#x5355;&#xFF0C;&#x53EA;&#x8981;&#x5148;&#x5BF9;score&#x6392;&#x5E8F;&#xFF0C;&#x5982;&#x679C;&#x5F53;&#x524D;score&#x7684;&#x503C;&#x6BD4;&#x524D;&#x4E00;&#x4E2A;score&#x7684;&#x503C;&#x5927;&#xFF0C;&#x6743;&#x91CD;&#x5C31;&#x81EA;&#x589E;1&#x3002;&#x5982;&#x679C;&#x4E0D;&#x5927;&#x4E8E;&#x524D;&#x4E00;&#x4E2A;score&#x5C31;&#x4FDD;&#x6301;&#x539F;&#x503C;&#x5982;&#x679C;&#x628A;&#x540E;&#x8FB9;&#x7684;&#x6761;&#x4EF6;&#x5316;&#x7B80;&#xFF0C;&#x6211;&#x4EEC;&#x7684;&#x5DE5;&#x4F5C;&#x5176;&#x5B9E;&#x5C31;&#x662F;&#x7ED9;score&#x6392;&#x5E8F;&#x540E;&#x52A0;&#x4E0A;&#x5E8F;&#x53F7;&#x3002;&#x8FD9;&#x65F6;&#x6211;&#x4EEC;&#x7684;&#x64CD;&#x4F5C;&#x5C31;&#x53D8;&#x5F97;&#x7B80;&#x5355;&#x4E86;&#x3002;

``` sql

select (@i:=@i+1) as i,table_name.* from table_name,(select @i:=0) as it
```
&#x4E0A;&#x8FF0;&#x662F;&#x4ECE;&#x7F51;&#x4E0A;&#x968F;&#x4FBF;&#x627E;&#x5230;&#x4E00;&#x4E2A;&#x81EA;&#x52A8;&#x6DFB;&#x52A0;&#x5E8F;&#x53F7;&#x7684;&#x8BED;&#x53E5;&#x3002;&#x6211;&#x4EEC;&#x89C2;&#x5BDF;&#x4E0A;&#x9762;&#x7684;SQL&#x8BED;&#x53E5;&#xFF0C;&#x5E76;&#x5206;&#x6790;&#x542B;&#x6709;&#x53D8;&#x91CF;&#x7684;SQL&#x8BED;&#x53E5;&#x7684;&#x7ED3;&#x6784;&#x3002;
&#x9996;&#x5148;&#x628A;&#x5E72;&#x6270;&#x9879;as&#x53BB;&#x6389;&#xFF0C;as&#x53EA;&#x662F;&#x7ED9;&#x67E5;&#x8BE2;&#x7ED3;&#x679C;&#x53D6;&#x4E86;&#x4E00;&#x4E2A;&#x522B;&#x540D;&#xFF0C;&#x6B64;&#x5904;&#x6211;&#x4EEC;&#x4E0D;&#x5FC5;&#x8003;&#x8651;as&#x3002;
```
select score , (@i:=@i+1) from Scores,(select @i:=0) init order by score desc
```
&#x8FD9;&#x6837;&#x7684;&#x8F93;&#x51FA;&#x7ED3;&#x679C;&#x662F;&#xFF1A;
``` json
{"headers": ["score", "(@i:=@i+1)"], "values": [[4.00, 1.0], [4.00, 2.0], [3.85, 3.0], [3.65, 4.0], [3.65, 5.0], [3.50, 6.0]]}
```

&#x770B;&#x770B;&#x4E0A;&#x9762;&#x7684;&#x7ED3;&#x679C;&#xFF0C;&#x6211;&#x4EEC;&#x5DF2;&#x7ECF;&#x5B9E;&#x73B0;&#x4E86;&#x4E00;&#x534A;&#x7684;&#x8981;&#x6C42;&#x4E86;&#x3002;&#x53EA;&#x4E0D;&#x8FC7;&#x73B0;&#x5728;&#x7ED3;&#x679C;&#x4E2D;&#x7684;&#x5E8F;&#x53F7;&#x6CA1;&#x6709;&#x6EE1;&#x8DB3;&#x201C;&#x76F8;&#x540C;&#x7684;&#x503C;&#x6709;&#x76F8;&#x540C;&#x7684;&#x6743;&#x91CD;&#x201D;&#x8FD9;&#x4E2A;&#x8981;&#x6C42;&#x3002;&#x6B64;&#x65F6;&#x6211;&#x4EEC;&#x9700;&#x8981;&#x5F15;&#x5165;&#x53E6;&#x4E00;&#x4E2A;&#x53D8;&#x91CF;@pre&#x6765;&#x4FDD;&#x5B58;&#x4E0A;&#x4E00;&#x4E2A;score&#x3002;
&#x5F53;&#x8BA1;&#x7B97;&#x6743;&#x91CD;&#x65F6;&#x5982;&#x679C;@pre&#x8DDF;&#x5F53;&#x524D;score&#x76F8;&#x7B49;&#xFF0C;&#x5219;@i&#x4FDD;&#x6301;&#x539F;&#x503C;&#xFF08;&#x6216;&#x8005;&#x8BF4;+0&#xFF09;&#xFF0C;&#x5426;&#x5219;&#x52A0;1.
``` sql
select Score, @i := @i + (@pre <> ( @pre := Score)) Rank from Scores, (select @i := 0,@pre := -1) init order by Score desc
```

&#x8FD9;&#x91CC;&#x9700;&#x8981;&#x6CE8;&#x610F;&#x7684;&#x662F;&#x53D8;&#x91CF;@pre&#x3002;&#x7531;&#x4E8E;&#x6211;&#x4EEC;&#x5728;&#x6743;&#x91CD;&#x90A3;&#x91CC;&#x5224;&#x65AD;&#x662F;&#x6839;&#x636E;@pre&#xFF08;&#x524D;&#x4E00;&#x4E2A;score&#x7684;&#x503C;&#xFF09;&#x662F;&#x5426;&#x7B49;&#x4E8E;&#x5F53;&#x524D;score&#x7684;&#x503C;&#xFF0C;&#x5982;&#x679C;@pre&#x7684;&#x521D;&#x59CB;&#x503C;&#x4E3A;&#x5927;&#x4E8E;0&#x7684;&#x6570;&#xFF0C;&#x5C31;&#x53EF;&#x80FD;&#x5224;&#x65AD;&#x51FA;&#x9519;&#x3002;&#x6700;&#x597D;@pre&#x7684;&#x521D;&#x59CB;&#x503C;&#x8BBE;&#x7F6E;&#x4E3A;&#x5C0F;&#x4E8E;0&#x7684;&#x6570;&#x3002;

