title: js-algorithm
toc: false
date: 2016-08-19 18:51:42
tags:
  - js-algorithm
categories:
  - algorithm
---

js算法

# 数组防重复
```javascript

function unique(arr) {
    var result = [], hash = {};
    for (var i = 0, elem; (elem = arr[i]) != null; i++) {
        if (!hash[elem]) {
            result.push(elem);
            hash[elem] = true;
        }
    }
    return result;
}
```
