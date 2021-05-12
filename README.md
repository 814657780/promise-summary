# promise-summary
promise相关的总结

## 1、准备
### 1.1、区别实例对象与函数对象
```
1、实例对象：new函数产生的对象，称为实例对象，简称为对象
2、函数对象：将函数作为对象使用时，简称为函数对象

function Fn(){

}
const fn = new Fn()
console.log(Fn.prototype)
Fn.bind({})
$('#test')
$.get('/test')
```
### 1.2、两种类型的回调函数
#### 1.2.1、同步回调
```
1、理解：立即执行，完全执行完了才结束，不会放入回调队列中
2、例子：数组遍历相关的回调函数/promise的excutor函数
```
#### 1.2.2、异步回调
```
1、理解：不会立即执行，会放入回调队列中将来执行。
2、例子：定时器回调 / ajax回调 / Promise的成功|失败的回调
```
### 1.3、js的error处理
#### 1.3.1、错误的类型
```
1、Error：所有错误的父类型
2、ReferenceError：引用的变量不存在
3、TypeError：数据类型不正确的错误
4、RangeError：数据值不在其所允许的范围内
5、SyntaxError：语法错误
```
#### 1.3.2、错误处理
```
1、捕获错误：try ... catch
2、抛出错误：throw error
```
#### 1.3.3、错误对象
```
message属性：错误相关信息
stack属性：函数调用栈记录信息
```
## 2、Promise的理解和使用
what why how
### 2.1、Promise是什么？
#### 2.1.1、理解
```
1、抽象表达：
   Promise是JS中进行异步编程的新的解决方案(旧的是谁？)

2、具体表达：
（1）、从语法上来说：Promise是一个构造函数
（2）、从功能上来说：Promise对象用来封装一个异步操作并可以获取其结果   

```
#### 2.1.2、Promise的状态改变
```
1、pendding变为resolved
2、pendding变为rejected
说明：只有这2种，且一个promise对象只能改变一次
     无论变成成功还是失败，都会有一个结果数据
     成功的结果数据一般称为value，失败的结果数据一般称为reason
```
#### 2.1.3、Promise的基本流程
```
// 1. 创建一个新的promise对象
new Promise((resolve,reject)=>{ // 执行器函数
// 2.执行异步操作函数
  setTimeout(()=>{
     const time = Date.now() // 如果当前时间是偶数就代表成功，否则代表失败
     // 3.1 如果成功了，调用resolve(value)
     if(time%2===0){
        resolve('成功的数据，time='+time)
     }else{
        // 3.2 如果失败了，调用reject(reason)
        reject('失败的数据，time='+time)
     }
  },1000)


})
p.then(
   value =>{ // 接收得到成功的value数据  onResolved
     console.log(value)
   },
   reason =>{ // 接收得到失败的reason数据  onRejected
     console.log(reason)
   }
)
```
### 2.2、为什么要用Promise？

#### 2.2.1、指定回调函数的方式更加灵活
```
1、旧的：必须在启动异步任务前指定
2、promise：启动异步任务 => 返回promise对象 =>给promise对象绑定回调函数（甚至可以在异步任务结束后指定/多个）
```
#### 2.2.2、支持链式调用，可以解决回调地狱问题
```
什么是回调地狱？回调函数嵌套调用，外部回调函数异步执行的结果是嵌套的回调函数执行的条件
回调地狱的缺点？不便于阅读 、不便于异常处理
解决方案？promise链式调用
终极解决方案? async/await
```
### 2.3、如何使用Promise？
#### 2.3.1、API
```
1、Promise构造函数：Promise(excutor){}
(1) excutor函数：同步执行 执行器 (resolve,reject)=>{}
(2) resolve函数：内部定义成功时我们调用的函数 value=>{}
(3) reject函数：内部定义失败时我们调用的函数 reason=>{}
说明excutor会在Promise内部立即同步回调，异步操作在执行器中执行。
2、Promise.prototype.then 方法:(onResolved,onRejected)也就是((value)=>{},(reason)=>{})
  onResolved函数：成功的回调函数 (value)=>{}
  onRejected函数：成功的回调函数 (reason)=>{}
  说明：指定用于得到成功value的成功回调和用于得到失败reason的失败回调
       返回一个新的promise对象
3、Promise.prototype.catch方法：(onRejected) 也就是((reason)=>{})  
   onRejected函数：失败的回调函数(reason)=>{} 
   说明：then()的语法糖，相当于：then(undefined, onRejected)   
4、Promise.resolve方法：(value)=>{}
   value: 成功的数据或Promise对象
   说明：返回一个成功/失败的promise对象    
5、Promise.reject方法：(reason)=>{}
   reason: 失败的原因
   说明：返回一个失败的promise对象  
6、Promise.all方法：(promises)=>{}
   promises: 包含n个promise的数组
   说明：返回一个新的promise，只有所有的promise都成功才成功，只要有一个失败了就直接失败
7、Promise.race方法：(promises)=>{}
   promises: 包含n个promise的数组
   说明：返回一个新的promise，第一个完成的promise的结果状态就是最终的结果状态 

   对比展示
   // 产生一个成功值为1的promise对象
   const p1 = new Promise((resolve,reject)=>{
      resolve(1)
   })
   const p2 = Promse.resolve(2)   
   const p3 = Promse.reject(3)  
   p1.then(value=>{console.log(value)}) 
   p2.then(value=>{console.log(value)}) 
   p3.catch(reason=>{console.log(reason)}) 

   const pAll = Promise.all([p1,p2,p3])
   pAll.then(values=>{
      console.log('all onResolved()',values)
   },
   reason=>{
      console.log('all onRejected()',reason)
   })
   const pRace = Promise.race([p1,p2,p3])
   pRace.then(value=>{
      console.log('race onResolved()',value)
   },
   reason=>{
      console.log('race onRejected()',reason)
   })
```
#### 2.3.2、promise的几个关键问题

##### 2.3.2.1、如何改变promise的状态？
```
(1)、resolve(value):如果当前是pendding就会变为resolved
(2)、reject(reason):如果当前是pendding就会变为rejected
(3)、抛出异常：如果当前是pendding就会变为rejected
const p = new Promise((resolve,reject)=>{
   // throw new Error('出错了') // 抛出异常，promise就会变为rejected，reason为抛出的error
   throw 3 // 抛出异常，promise就会变为rejected，reason为抛出的3
})
console.log(p)
```

##### 2.3.2.2、一个promise指定多个成功/失败回调函数，都会调用吗？
```
   会，当promise改变为对应状态时都会调用
```
##### 2.3.2.3、改变promise状态和指定回调函数谁先谁后？
```
(1)、都有可能，正常情况下是先指定回调函数再改变状态，但也可以先改状态再指定回调
(2)、如何先改状态再指定回调？
在执行器中直接调用resolve()/reject()
延迟更长时间才调用then()

// 常规：先指定回调函数，后改变的状态
new Promise((resolve,reject)=>{
   setTimeout(()=>{
      resolve(1)  // 后改变的状态（同时指定数据），异步执行回调函数
   },1000)
}).then( // 先指定回调函数，保存当前的回调函数
   value=>{},
   reason=>{console.log('reason',reason)}
)
new Promise((resolve,reject)=>{
      resolve(1)  // 先改变的状态（同时指定数据），保存当前的状态
}).then( // 后指定回调函数，异步执行回调函数
   value=>{console.log('value',value)},
   reason=>{console.log('reason',reason)}
)
(3) 什么时候才能得到数据？
如果先指定的回调，那当状态发生改变时，回调函数就会调用，得到数据
如果先改变的状态，那当指定回调时，回调函数就会调用，得到数据
(4)
```
##### 2.3.2.4. promise.then()返回的新的promise的结果状态由什么决定？
```
(1)简单表达：由then()指定的回调函数执行的结果决定
(2)详细表达：
  如果抛出异常，新promise变为rejected，reason为抛出的异常
  如果返回的是非promise的任意值，新的promise变为resolved，value为返回的值
  如果返回的是另外一个新的promise，此promise的结果就会成为新promise的结果
  如果返回的是一个异步函数，要用new promise()进行处理

new Promise((resolve,reject)=>{
   resolve(1)
}).then(
   value=>{
      console.log('onResolved1()',value)
      // return 2  // resolve
      // return Promise.resolve(3) // resolve
      // return Promise.reject(4)  // reject
      throw 5  // reject
   },
   reason=>{
      console.log('onRejected1()',reason)
   }
).then(
   value=>{
      console.log('onResolved2()',value)
   },
   reason=>{
      console.log('onRejected2()',reason)
   }
)
```

##### 2.3.2.5.promise如何串联多个操作任务
```
（1）promise的then()返回一个新的promise，可以看成then()的链式调用
（2）通过then的链式调用串联多个同步/异步任务
new Promise((resolve,reject)=>{
   setTimeout(()=>{
      console.log('执行任务1（异步）')
      resolve(1)
   },1000)
}).then(
   value=>{
      console.log('任务1的结果：',value)
      console.log('执行任务2（同步）')
      return 2
   }
).then(
   value=>{
      console.log('任务2的结果：',value)
      return new Promise((resolve,reject)=>{
         // 启动任务3（异步）
         setTimeout(()=>{
           console.log('执行任务3（异步）')
           resolve(3)
         },1000)
      })
   }
).then(
   value=>{
      console.log('执行任务3的结果：',value)
   }
)
```