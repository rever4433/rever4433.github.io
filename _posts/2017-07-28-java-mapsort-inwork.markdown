---
layout:     post
title:      "[Java]Hashmap排序与生成并列序号"
date:       2017-07-28 14:25:30
author:     "YAKANG GAO"
header-img: "img/post-bg-05.jpg"
---


最近做的项目有这样一个需求：要求对map中的值进行排序并生成序号。如果值相等则序号不变；如果不相等序号为该数数值在所有元素中的索引。如下表所示：

|Key(String)|Value(Float)|Idx|
|---|---|---|
|23|12.4 | 1|
|10|3.2 | 2|
|11|2.2|  3|
|9|2.2|  3|
|1|1.9 | 5|
|6|0.5|  6|



``` java

public static Integer getSort(Map<String, Float> hashMap, String string) {
		// 转换
		ArrayList<Entry<String, Float>> arrayList = new ArrayList<Map.Entry<String, Float>>(
				hashMap.entrySet());
		// 排序
		Collections.sort(arrayList, new Comparator<Map.Entry<String, Float>>() {
			public int compare(Map.Entry<String, Float> map1,
					Map.Entry<String, Float> map2) {
				return ((map2.getValue() - map1.getValue() == 0) ? 0 : (map2
						.getValue()
						- map1.getValue() > 0) ? 1 : -1);
			}
		});
		Map<String, Integer> idxex = new HashMap<String, Integer>();
		// 输出
		int size = 1;
		for (int i = 0; i < arrayList.size(); i++) {
			Entry<String, Float> entry = arrayList.get(i);
			if (i > 0) {
				float curr = arrayList.get(i).getValue();
				float last = arrayList.get(i - 1).getValue();
				if (curr != last) {
					size = i;
					idxex.put(entry.getKey(), (i + 1));
					size++;
				} else {
					idxex.put(entry.getKey(), size);
				}
			} else {
				idxex.put(entry.getKey(), size);
			}
		}

		for (Map.Entry<String, Integer> entry : idxex.entrySet()) {
			String key = entry.getKey();
			if (string.equals(key)) {
				return entry.getValue();
			}
		}
		return 0;
	}

```