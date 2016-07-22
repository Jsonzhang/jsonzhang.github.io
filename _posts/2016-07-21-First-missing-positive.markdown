---
layout: post
title:  "First Missing Positive"
date:   2016-5-13
comments: true
---


Today's algorithm question is followed:

Given an unsorted integer array, find the first missing positive integer.

For example,
    Given **[1,2,0]** return **3**,
    and **[3,4,-1,1]** return **2**.

Your algorithm should run in O(n) time and uses constant space.


The answer came from the subconscious immediately was "Order it and find the number in the ordered array". But it was discarded soon due to its time complexity which is not qualified with the requirement, your algorithm should run in O(n) time and uses constant space. Even quick sort algorithm is a O(nlgn) algorithm.

Without ordering it , there is another idea came to me soon. I can find the missing positive number when I order it, so I can find the target even before I finish ordering this array. Though I know that must not the best way but I still want to recorded it :


```javascript
function firstMissingPositive(nums) {
    var returnValue;
    var preValue;
    var nowValue;

    nums = filterNegative(nums);


    if(nums.length === 0){
      return 1;
    }else if(nums.length === 1 && nums[0] === 1){
      return 2;
    }else if(nums.length === 1){
      return 1;
    }

    nowValue = nums.splice(findMin(nums), 1)[0];

    if(nowValue > 1){
      return 1;
    }

    do{
        preValue = nowValue;
        nowValue = nums.splice(findMin(nums), 1)[0];
        returnValue = returnJudgement(preValue, nowValue);

        if(returnValue){
          return returnValue;
        }
    }while(nums.length > 0)

    return nowValue + 1;
}

function returnJudgement(lastValue, value){
  if(value - lastValue <= 1){
    return false;
  }else{
    return lastValue + 1;
  }
}
// N
function findMin(ary){
  var minValue = ary[0];
  var returnValue = 0;
  for (var i = 1; i < ary.length; i++) {
    if(ary[i] < minValue){
      minValue = ary[i];
      returnValue = i;
    }
  }
  return returnValue;
}

// N
function filterNegative(ary){
  for (var i = 0; i < ary.length;) {
    if(ary[i] <= 0 || ary[i] > ary.length){
      ary.splice(i, 1);
    }else{
      i++;
    }
  }
  return ary;
}
```



Avoiding ordering , it is such a time-consuming job. There is a better way I can achieve this goal arose in my mind - Use Hash :


```javascript
function firstMissingPositive(nums) {
  var obj = {};
  var j = 1;
  for (var i = 0; i < nums.length; i++) {
    obj[nums[i].toString()] = true;
  }
  do {
    if(!obj[j.toString()]){
      return j;
    }
    j++;
  }while(true)
}
```

That the best way I can achieve. But I can not competitive advantage against the previous solution when I submitted it to [leetcode.com](http://leetcode.com) . Both finish all test-case in 140ms.
