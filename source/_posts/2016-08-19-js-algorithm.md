title: js-algorithm
toc: false
date: 2016-08-19 18:51:42
tags:
  - js-algorithm
categories:
  - algorithm
---

js算法
<!--more-->

# 数组
### 重复数组
```javascript

function  delRepeat1(arr) {
    var result = [], hash = {}, length = arr.length;
    for (var i = 0; i < length; i++) {
        if (!hash[arr[i]]) {
          result.push(arr[i]);
          hash[arr[i]] = 1;
        } else {
          hash[arr[i]]++;
        }
    }
    return result;
}

function delRepeat2(arr) {
  var arr1 = [];    //定义一个临时数组  
  for(var i = 0; i < this.length; i++){
    if(arr1.indexOf(this[i]) == -1){  
        arr1.push(this[i]);  
    }  
  }  
  return arr1;  
}

function delRepeat3(arr) {
    var result = [], length = arr.length;
    for (var i = 0; i < length; i++) {
      var repeat = false;
      for (var j = i + 1; j < length - 1; j++ ) {
        if (a[i] === a[j]) {
          repeat = true;
        }
      }
      if (!repeat) {
        result.push(arr[i]);
      }
    }
    return result;
}

// 返回重复最多的元素以及数量
function  countRepeatMax(arr) {
    var result = [], hash = {}, length = arr.length;
    for (var i = 0; i < length; i++) {
        if (!hash[arr[i]]) {
          result.push(arr[i]);
          hash[arr[i]] = 1;
        } else {
          hash[arr[i]]++;
        }
    }
    // 遍历 res
    var keys = Object.keys(hash);
    var maxNum = 0, maxEle;
    for (var i = 0, l = keys.length; i < l; i++) {
        if (hash[keys[i]] > maxNum) {
            maxNum = hash[keys[i]];
            maxEle = keys[i];
        }
    }
    return maxNum;
}

```

# A2BC3D1 => AABCBCBCD

```
var numReg = /\d+/g;
    letterReg = /[a-zA-Z]+/g;
    str = 'A9BC10D1';
    numArr = str.match(numReg);
    letterArr = str.match(letterReg);
var newStr = '';
for(var i = 0; i < numArr.length; i++) {
  newStr += letterArr[i].repeat(parseInt(numArr[i]));
}

function strRepeat(str, num) {
  return num > 0 ? new Array(num + 1).join(str) : '';
}

```
