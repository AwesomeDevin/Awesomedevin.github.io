---
title: 【Nodejs】复制文件并获取进度 
date: 2021-04-21
excerpt: 来自阿里的面试题 - NodeJS 复制文件并获取进度 
index: 1
categories:
- nodejs
tag:
- nodejs
---


```javascript
const fs = require('fs')

const input = 'test.mp4'
var file = fs.createReadStream(input);
var out = fs.createWriteStream('./test1.mp4');

let totalSize = fs.statSync( input ).size  // 通过 fs.statSync 获取文件大小
let curSize = 0

file.on('data',function(chunk){
  curSize += chunk.length
  const percent = (curSize / totalSize * 100)
  out.write(chunk)
  console.log(`读取中，当前进度：${percent}`)
});
file.on('end',function(){
	out.end();
})
```