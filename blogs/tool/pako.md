---
title: pako.js的使用
date: 2019-10-2
tags:
  - tag3
categories:
  - tools
---

::: tip 使用场景

在3D地图上实现路线渲染，需要从后台获取大量数据点进行渲染，由于数据过多，导致接口请求时间延长，渲染缓慢等问题，所以进行数据压缩提高接口响应速度
:::


...
## 安装
```bash
npm i pako
# or
yarn add pako

```

## 引用
```bash
import pako from 'pako'

```
## 压缩
```script
  const zip = (str) => {
     	var binaryString = pako.gzip(str, { to: 'string' });
     	return btoa(binaryString);
   }
```

## 解压
```script
const unzip = (b64Data) => {
  let strData = atob(b64Data);
  // const charData = strData.split('').map((x) => x.charCodeAt(0));
  const charData = strData.split(',').map((x) => x);
  const binData = new Uint8Array(charData);
  const data = pako.inflate(binData);
  strData = Utf8ArrayToStr(data);
  return strData;
};
/**
 * utf-8 Array 转化字符串
 * @param array Utf8Array
 * @returns {string}
 * @constructor
 */
const Utf8ArrayToStr = (array) => {
  const len = array.length;
  let out = '';
  let i = 0;
  let char1, char2, char3, char4;
  while (i < len) {
    char1 = array[i++];
    // 当单个字节时, 最大值 '01111111', 最小值 '00000000' 右移四位 07, 00
    // 当两个字节时, 最大值 '11011111', 最小值 '11000000' 右移四位 13, 12
    // 当三个字节时, 最大值 '11101111', 最小值 '11100000' 右移四位 14, 14
    if (char1 >> 4 <= 7) {
      out += String.fromCharCode(char1);
    } else if (char1 >> 4 === 12 || char1 >> 4 === 13) {
      char2 = array[i++];
      out += String.fromCharCode(((char1 & 0x1F) << 6) | (char2 & 0x3F));
    } else if (char1 >> 4 === 14) {
      char2 = array[i++];
      char3 = array[i++];
      char4 = ((char1 & 0x0F) << 12) | ((char2 & 0x3F) << 6);
      out += String.fromCharCode(char4 | ((char3 & 0x3F) << 0));
    }
  }
  return out;
};
``` 



