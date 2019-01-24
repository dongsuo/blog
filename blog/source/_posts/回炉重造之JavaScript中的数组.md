---
title: JavaScript基础整理之数组(一)
date: 2017-10-14 11:48:24
tags:
---

数组应该是日常开发中最常见的数据结构了，虽然常见，但是却不一定能优雅地处理好，JavaScript中数组的处理方法很多，各个方法的参数、返回值、是否修改原数组等也容易记混。这些方法虽然都比较简单，但是初学的时候没怎么重视，后来在实际的项目中却发现，能否灵活地运用这些方法对开发进度还是有很大的影响的，所以在此整理一下，温故知新。

<!--more-->

#### array.slice(a,b) 

- 描述：slice 接受两个参数：开始和结束的索引值，返回原数组的从a到b的一部分（包含a，不包含b），原数组不发生改变
- 原数组：不变
- 返回：原数组的从a到b的一部分（包含a，不包含b）
- 可以将slice用于数组的浅复制

```javascript
let arr = [1,2,3,4,5];
let arr2 = arr.slice(1,3);
let arr1 = arr.slice();
arr1.push([6])
let arr3 = arr1.slice();
arr3[arr3.length-1].push(7)
console.log(arr)  //[ 1, 2, 3, 4, 5 ]
console.log(arr1) //[ 1, 2, 3, 4, 5, [ 6, 7 ] ]
console.log(arr2) //[ 2, 3 ]
console.log(arr3) ////[ 1, 2, 3, 4, 5, [ 6, 7 ] ]
```

#### array.splice(a,b,item1,item2...)

- 描述：slice 接受三个参数：开始的索引值、要删除元素的个数、用于代替删除元素的内容，返回被删除的元素构成的数组，原数组
- 原数组：变
- 返回：被删除元素构成的数组

```javascript
let arr = [1,2,3,4,5]
let arr3 = arr.splice(1,2,'m','n','p')
console.log(arr);//[ 1, "m", "n", "p", 4, 5 ]
console.log(arr3);//[ 2, 3 ]
```

#### array.concat()

- 描述：concat 接受一个或多个参数，若参数为数组，则将该数组与原数组合并，若不为数组，则将该参数作为原数组的一个元素
- 原数组：不变
- 返回：合并后的新数组

```javascript
let arr = [ 1, "m", "n", "p", 4, 5 ]
let arr4 = arr.concat('abcd',[100,[101]]);
console.log(arr)//[ 1, "m", "n", "p", 4, 5 ]
console.log(arr4);// [ 1, "m", "n", "p", 4, 5, "abcd", 100, [101] ]
```

#### array.join()

- 描述：join 接受一个参数，作为返回的字符串的分隔符，

- 原数组：不变

- 返回：以参数为分隔符的字符串

  ```javascript
  let arr = [ 1, "m", "n", "p", 4, 5 ]
  let str = arr.join('-')
  console.log(arr)//[ 1, "m", "n", "p", 4, 5 ]
  console.log(str)//1-m-n-p-4-5
  ```

#### array.reverse()

- 描述：无参数，将原数组的元素前后颠倒

- 原数组：变，顺序颠倒

- 返回：颠倒位置后的原数组

  ```javascript
  let arr = [ 1, "p", 4, 5, 4, 5 ]
  let arr6 = arr.reverse()
  console.log(arr) //[ 5, 4, 5, 4, "p", 1 ]
  console.log(arr6) //[ 5, 4, 5, 4, "p", 1 ]
  ```

#### array.toString()

- 描述：无参数，将原数组转换成字符串，以 ‘,’ 分割。（不可指定分隔符，比array.join()的功能弱）

- 原数组：不变

- 返回：字符串

  ```javascript
  let arr = [ 5, 4, 5, 4, "p", 1 ]
  let str1 = arr.toString()
  console.log(arr)
  console.log(str1)
  ```

#### array.push()/array.unshift()

- 描述：将参数作为数组的元素，置于数组的尾部/头部

- 原数组：变

- 返回：新数组的长度

  ​

```javascript
  let arr = [ 1, "p", 4, 5, 4, 5, 6 ]  
  let l = arr.push(8)
  console.log(l)// 8
  console.log(arr)// [ 1, "p", 4, 5, 4, 5, 6, 8 ]

  let l2 = arr.unshift(0)
  console.log(l2) //1
  console.log(arr) // [ 0, 1, "p", 4, 5, 4, 5, 6, 8 ]
```

  

#### array.pop()/array.shift()

- 描述：分别去除数组的最后一个/第一个元素
- 原数组：变
- 返回：被删除的元素

```javascript
let arr = [ 0, 1, "p", 4, 5, 4, 5, 6, 8 ]
let ele = arr.shift();
console.log(ele)//0
console.log(arr)//[ 1, "p", 4, 5, 4, 5, 6, 8 ]

let ele2 = arr.pop();
console.log(ele2)//8
console.log(arr)// [ 1, "p", 4, 5, 4, 5, 6 ]
```

#### array.copyWithin(target,start,end)——ES6新增

- 描述：接受三个参数，target为目标位置的索引，start 为待复制数据的起始索引，默认为0，end为待复制数据的结束索引（不包含），默认等于数组长度（最后一位元素的索引+1）

- 原数组：变，但不更改原数组的长度

- 返回：改变后的数组

  ```javascript
  let arr = [ 1, "m", "n", "p", 4, 5 ]
  let arr5 = arr.copyWithin(1,3,6)
  console.log(arr) // [ 1, "p", 4, 5, 4, 5 ]
  console.log(arr5) // [ 1, "p", 4, 5, 4, 5 ]
  ```

这只是数组的一部分，还有数组迭代、查询、浅复制、深复制等很多方法没有说，下次再说吧。