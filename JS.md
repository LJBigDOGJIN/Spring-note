数组遍历

1. for循环

2. for  in   

   > 遍历数组时返回的是数组下标的值 （不能保证按数组内的顺序遍历）
   >
   > 遍历对象返回键值

3. for of

   > 遍历数组返回的是值
   >
   > 遍历对象返回对应键的值

数据类型：

Number 不区分整数和浮点数

```js
123; // 整数123
0.456; // 浮点数0.456
1.2345e3; // 科学计数法表示1.2345x1000，等同于1234.5
-99; // 负数
NaN; // NaN表示Not a Number，当无法计算结果时用NaN表示
Infinity; // Infinity表示无限大，当数值超过了JavaScript的Number所能表示的最大值时，就表示为Infinity

1/0  infinity
0/0 NaN
js的整数范围是±2^53 超出这个范围会导致精度不准确，可以使用更高精度的BigInt表示,bigint的表示方法为在数字后面加一个n
var bi1 = 9223372036854775807n;
注意：bigint可以进行正常的数值运算，但是不能将bigint和Number类型放在一起运算
```

String   用  ' ' 或 " " 包括的所有文本

```js
"aaa","sad"

ES6模板字符串
var txt='哈哈'
var txt2="ok"
var text=`aaa ，${txt},bbb,${txt2}`
```

String字符串的一些常用方法

```js
//js中对字符串的一下操作不会影响源字符串值，而是返回一个新的字符串
substring(start,end)  // 截取范围 [start,end)
slice(start,end)  // 此方法参数可以为负数
indexOf(str) //返回字符串str出现的位置
toUpperCase() // 将字符串转为大写
toLowerCase() //将字符串转为小写
```



布尔值    true  false

```js
//可以通过运算得出布尔值
2>1  //true

8<6  //false

```

数组

```js
// 可以直接 定义一个数组，也可以通过new 的方式 
new Array(1, 2, 3);  // 创建了数组[1, 2, 3]
var arr=[1,2,'3',NaN，true] //数组里面的数据类型可以不同

//js中可以改变数组的长度
pop(e) //从数组尾部删除一个元素
push(e) //往数组尾部添加一个元素
unshift() //向数组头部添加元素
shift(e) 
```

对象

```js
var person={
    name:"aaa",
    age:12
}
```



