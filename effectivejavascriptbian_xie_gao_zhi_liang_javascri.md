# EffectiveJavaScript编写高质量JavaScript的68方法

# 第零章 总结

其实这本书,可能可读性相对较差

第二章说作用域和第四章说原型的时候,相对感兴趣一点

其他的话,可能笔者在实践中基本也不会这么写,也就没怎么细读了

总体感觉3点5星吧(满5)

# 第一章 让自己习惯JavaScript

## 第3条 当心隐式的强制转换

验证一个值是否是真正的NaN

isNaN不能判断是否为真的NaN

```javascript

var x = NaN
isNaN(x)//true
isNaN('foo')//true
```

验证是否为真的NaN....怪异的来了

```javascript
function isReallyNaN(x) {
  return x!==x
}
```

## 第5条 避免对混合类型使用==运算符

对于类型转换

1. Date对象会先尝试toString再尝试valueOf
2. 非Date就会先尝试valueOf再尝试toString

## 第6条 了解分号插入的阶段

1. 仅在"}"标记之前,一行的结束和程序的结束处推导分号
2. 仅在紧接着的标记不能被解析时推导分号
3. 在以`( [ + - /`字符开头的语句前决不能省略分号
4. 当脚本连接时,在脚本之间显式地插入分号
5. 在return throw break continue ++ --参数之前决不能换行
6. 分号不能作为for循环的头部或空语句的分隔符而被推导出

# 第二章 变量作用域

## 第8条 尽量少用全局对象

先这样写,以后再调整.虽然会比较方便

但是优秀的程序员会不断留意程序结构,持续地归类相关的功能以及分离不相关的组件,并将这些行为作为变成过程中的一部分

使用全局对象来做平台特性检测

## 第12条 理解变量声明提升

把变量理解为两个部分 声明和赋值

```javascript
function k (){

  ...
  if (...) {
    var a = 2 
  }
}

//等效于

function k (){
  var a;
  ...
  if (...) {
    a = 2 
  }
}

```

## 第16条 避免使用eval创建局部变量

如果eval函数代码可能创建全局变量,将此调用封装到嵌套函数中以防止作用域污染

```javascript
var y = 'global'
function test(src) {
  eval(src);
  return y
}

test('var y = local') //local

//防止域污染
var y = 'global'
function test(src) {
  (function(){eval(src);})()
  return y
}

test('var y = local') //global

```

# 第17条 间接调用eval优于直接调用


直接调用eval,编译器需要确保执行程序具有完全访问调用者作用域的权限,我们可以通过间接调用令eval失去所有局部作用域的访问能力

```javascript
var x = 'global'
function test() {
  var x = 'local'
  var f = eval
  return f('x')
}

test()//global


```

还有一些奇怪的代码可以实现这种方案

(0,eval)(src)

0被求值被忽略掉了,括号表示的序列表达产生的结果是eval函数,但是其没有访问作用域的能力

# 第三章 使用函数

## 第23条 永远不要修改arguments对象

永远不要修改arguments对象

使用 [].slice.call(arguments)将arguments对象复制到一个真正的数组中再进行修改



# 第四章 对象和原型

## 第30条 理解prototype getPrototypeOf和__proto__之间的不同

1. C.prototyoe: 用于建立由new C()创建的对象原型
2. Object.getPrototypeOf(obj): ES5获取obj对象原型的标准方法
3. obj.__proto__: 获取obj对象的原型非标准方法

```javascript
function User(){}
var u = new User()
Object.prototype(u) === User.prototype
```

## 第33条 使构造函数和new无关

```javascript
function User(){
  this.name = 'k'
}

var u = User()
u //undefiend
this.name//k
```

这样不适用new的情况下

this默认为全局,而在严格模式下 this为undefined

所以在webpack+babel打包class的时候检查一下使用class时  是否用了new

```javascript

	function _classCallCheck(instance, Constructor) { if (!(instance instanceof Constructor)) { throw new TypeError("Cannot call a class as a function"); } }

	var KReport = function () {
	    function KReport() {
	        _classCallCheck(this, KReport);
	    }

```

只有使用new的时,this才会变为构造函数的实例

当然也有措施使使用了new和没使用new变得一样

```javascript
function User(name) {
  var self = this instanceof User
           ? this
           : Object_create(User.prototype)
  self.name = name
  return self
}

//兼容不支持Create的浏览器
if(typeof Object.create === 'undefined') {
  Object.create = function(prototype) {
    function C() {}
    C.prototype = prototype
    return new C()
  }
}


```

# 第五章 数组和字典

## 第44条 使用null原型防止原型污染


```javascript
function C(){}
C.prototype = null

var o = new C()
Object.getPrototypeOf(o) === null //false
Object.getPrototypeOf(o) === Object.prototype //true

```

以上这样还是已object.prototype为原型

ES5提供了一个真正的null原型

```javascript

var x = Object.create(null)
Object.getPrototypeOf(x) === null //true
Object.getPrototypeOf(x) === Object.prototype //false
```

# 第六章 类和API设计设计

## 第60条: 支持方法链

通过在无状态的方法中返回新对象来支持方法链

通过在有状态的方法中返回this来支持方法链



# 第七章 并发

## 第68条 使用promise模式清洁异步逻辑






















































