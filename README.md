## javascript


### 目录

* [作用域](#作用域)
* [执行上下文](#执行上下文)
* [闭包](#闭包)

### 作用域

作用域分 全局作用域和局部作用域

	1. 全局作用域 在浏览器上 window 在node的上global 在这个里面定义的变量 有全局的作用域
	2. 局部作用域 在函数里面定义的变量有着局部作用域
	3. 局部作用域里面可以获取到全局作用域的变量，全局的作用域则不能访问局部的变量

```javascript

var a=100;
var b=50;
function add(){
	var c=100;
	//在add函数局部变量里面就可以看到全局的a b 
	console.log(a+b);	
}
add();
//在全局里面就看不到局部的变量c 
console.log(c)


```

提示：

		150
		Uncaught ReferenceError: c is not defined
    	at index.html:12

执行add 可以得到函数外面的A和B的值但是在外面得不到C的值


### 执行上下文
与this有关，☞当前运行的环境，在严格模式下有区别，但是传统的分为四种情况

* **在对象中调用**

```javascript
var cat={
	words:'喵喵喵!!',
	speak:function (){
		console.log('这个动物会发出'+this.words+'的声音');
		console.log(this);	
		console.log(this === cat);	
	}
}
cat.speak();
```
在对象里面 this 指向对象本身

* **在函数中调用**

```javascript
function cat(msg){
	this.words=msg;
	console.log(this.words);
	console.log(this);//window
}
cat('喵喵喵!!!');
```
在函数中执行 指向全局变量
* **在构造函数中调用**
```javascript
function foo(words){
	this.words=words;
	this.speak=function(){
		console.log(this.words);
	}
}
var foo_obj=new foo('xiaoma');
foo_obj.speak();
```

在构造函数中指向构造出来的函数

* **call和apply调用**

```javascript
var cat={
	words:'喵喵喵!!',
	speak:function (say){
		console.log('这个动物会发出'+say+'的声音');
		console.log(this);	
		console.log(this === cat);	
	}
}
var dog ={
	words:'wang'
}

cat.speak.call(dog,'Speak')
```
这里的this被绑在了dog上


### 闭包

闭包，我认为就是一种打破全局变量和局部变量的一种方法











