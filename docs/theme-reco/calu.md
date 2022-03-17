---
title: 算法
date: 2019-09-21
tags:
  - tag1
categories:
  - js
---
## 算法专题

### 数学

#### 斐波那契

1.

```javascript
function f(n, res = []) {
  if (n < 2) {
    return n;
  }
  return f(n - 1) + f(n - 2);
}
```

2.

```javascript
对递归进行优化，保存上一次的值
function f(n, res = []) {
    if (n < 2) {
        return n
    }
    return res[n]= res[n]? res[n] : f(n - 1) + f(n - 2)

    // return res[n] ?? f(n - 1) + f(n - 2)
}
```

3.

```javascript
// 减少递归次数
function f(n) {
  if (n < 2) {
    return n;
  }
  let arr = [1, 1];
  for (let i = 2; i < n; i++) {
    arr[i] = arr[i - 1] + arr[i - 2];
  }
  return arr[n - 1];
}
```

4.

```javascript
// 优化空间问题
function f(n) {
  if (n < 2) {
    return n;
  }
  let pre = 1;
  let cur = 1;
  let sum = 0;
  for (let i = 2; i < n; i++) {
    sum = pre + cur;
    pre = cur;
    cur = sum;
  }
  return sum;
}
```

5.

```javascript
// 利用尾递归优化
function Fibonacci2(n, ac1 = 1, ac2 = 1) {
  if (n <= 1) {
    return ac2;
  }

  return Fibonacci2(n - 1, ac2, ac1 + ac2);
}
```

### 链表

#### 反转链表

```typescript
反转一个单链表。
示例:
输入: 1->2->3->4->5->NULL
输出: 5->4->3->2->1->NULL

function reverseList(head: ListNode | null): ListNode | null {
    let pre = null
    let next = null
    while (head) {
        next = head.next
        head.next = pre
        pre = head
        head = next
    }
    return pre
};
```

#### 判断是否有环

```javascript
判断给定的链表中是否有环。如果有环则返回true，否则返回false。
// 快慢指针法，一个走2布，一个走一步
function hasCycle(head){
	let p = head
    let q = head
    while(p!=null && p.next!=null){
        p = p.next.next
        q= q.next
        if(p==q){
            return true
        }
    }
    return false
}
```

### 指针

#### 无重复字符的最长子串

```typescript
给定一个字符串，请你找出其中不含有重复字符的 最长子串 的长度。

示例 1:
输入: s = "abcabcbb"
输出: 3
解释: 因为无重复字符的最长子串是 "abc"，所以其长度为 3。

function lengthOfLongestSubstring(s: string): number {
    let maxLength = 0
    //存放最长子字符串的
    let arr = []
    for (let i of s) {
        // 查看是否有重复的
        let index = arr.indexOf(i)
        if (index !== -1) {
            //有的话，去除重复的字符串
            arr.splice(0, index + 1)
        }
        arr.push(i)
        maxLength = Math.max(maxLength, arr.length)
    }
    return maxLength
};
```

### 数组

#### 两数之和

```typescript
给定一个整数数组 nums 和一个整数目标值 target，请你在该数组中找出 和为目标值 的那 两个 整数，并返回它们的数组下标。
你可以假设每种输入只会对应一个答案。但是，数组中同一个元素不能使用两遍。
你可按任意顺序返回答案。
输入：nums = [2,7,11,15], target = 9
输出：[0,1]
解释：因为 nums[0] + nums[1] == 9 ，返回 [0, 1] 。


function twoSum(nums: number[], target: number): number[] {
    let map: Map<number, number> = new Map()
    for (let i = 0; i < nums.length; i++) {
        // 9 - 2 = 7，7是需要查看有么有的值
        let cur = target - nums[i]
        // 查看是否有7，如果有就返回
        if (map.has(cur)) {
            return [map.get(cur), i]
        }
        // 添加已经遍历过的值
        map.set(nums[i], i)
    }
    return
};
```

### 排序算法

#### 冒泡排序

```javascript
function sort(arr) {
  let len = arr.length;
  for (let i = 0; i < len - 1; i++) {
    let flag = true;
    for (let j = 0; j < len - 1 - i; j++) {
      if (arr[j] > arr[j + 1]) {
        [arr[j], arr[j + 1]] = [arr[j + 1], arr[j]];
        flag = false;
      }
    }
    if (flag) {
      return arr;
    }
  }
  return arr;
}
```

#### 选择排序

```typescript
function sort<T>(arr: T[]): T[] {
  let len = arr.length;
  for (let i = 0; i < len - 1; i++) {
    let current = i;
    for (let j = i + 1; j < len; j++) {
      if (arr[current] > arr[j]) {
        current = j;
      }
    }
    if (current !== i) {
      [arr[i], arr[current]] = [arr[current], arr[i]];
    }
  }
  return arr;
}
```

#### 快速排序

```typescript
function sort<T>(
  arr: T[],
  start: number = 0,
  end: number = arr.length - 1
): T[] {
  if (end - start < 1) {
    return arr;
  }
  const target = arr[start];
  let left = start;
  let right = end;
  while (left < right) {
    while (left < right && arr[right] >= target) {
      right--;
    }
    arr[left] = arr[right];
    while (left < right && arr[left] < target) {
      left++;
    }
    arr[right] = arr[left];
  }
  arr[left] = target;
  sort(arr, start, left - 1);
  sort(arr, left + 1, end);
  return arr;
}
```
