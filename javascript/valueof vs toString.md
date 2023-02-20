valueOf 与 toString

-   1、`valueOf`偏向于运算，`toString`偏向于显示
-   2、对象转换时，优先调用`toString`
-   3、强转字符串优先调用`toString`，强转数字优先调用`valueOf`
-   4、正常情况下，优先调用`toString`
-   5、运算操作符情况下优先调用`valueOf`

#### 调用valueOf

| 调用者 | 返回值 | 返回值类型 |
| --- | --- | --- |
| Array | 数组本身 | Array |
| Boolean | 布尔值 | Boolean |
| Date | 毫秒数 | Number |
| Function | 函数本身 | Function |
| Number | 数字值 | Number |
| Object | 对象本身 | Object |
| String | 字符串 | String |

#### 调用toString

| 调用者 | 返回值 | 返回值类型 |
| --- | --- | --- |
| Array | 数组转字符串，相当于Array.join() | String |
| Boolean | 转字符串'true'、'false' | String |
| Date | 字符串日期，如'Fri Dec 23 2016 11:24:47 GMT+0800 (中国标准时间)' | String |
| Number | 数字字符串 | String |
| Object | '\[object Object\]' | String |
| String | 字符串 | String |