## javascript


### 目录

* [作用域](#作用域)
* [执行上下文](#执行上下文)
* [闭包](#闭包)
* [活动对象](#活动对象)
* [原型机制](#原型机制)
* [作用域和作用域链](#作用域和作用域链)


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

闭包，我认为就是一种打破全局变量和局部变量的一种方法。一个作用域并没有在他相应的词法作用域的地方被执行，就是闭包

```javascript
//全局变量
var n = 10;
function foo(){
	console.log(n);
}
foo() //10  局部变量可以查看全露变量

//局部变量
function foo(){
	var n = 10;
}
foo() 
console.log(n);//n is not defined

//注意  函数里面定义n 要用var  如果不用var 其实定义的是全局变量所以能查到n
function foo(){
	n = 10;
}
foo() 
console.log(n);//n is not defined

//我们如何能够在全局变量里面访问到局部的变量呢
function foo(){
	var n=100;
	function  bar(){
		console.log(n);
	}
	return bar;
}
var resule=foo();
resule();//100 

```
在 最后一个例子中，我们在 函数foo（局部变量） 中定义了一个函bar（又一个嵌套的局部变量），这个bar是相对于foo的局部变量，bar可以看到foo的所有变量，然后在foo函数中直接把bar这个函数返回出去，那么调用这个返回的函数，就可以在全局里面得到foo函数的变量

这里需要一个知识点 `词法作用域`

	什么是词法作用域，就是函数不管在哪里执行，他都拥有在代码编写时，词法所规定的作用域。就是你在中国出生，不管你在哪里生活，你都是中国人。
	就如同闭包里面的 bar 他出生时的作用域是 foo 的子局部作用域。所以他可以看到foo里面的变量，那么把它return到全局里面的时候，他也是可以看到foo的变量。

**闭包的作用**

* 第一个就是上面所述，读取函数内部的变量
* 第二个就是可以让这些变量始终保存在内存中 ，如下：

```javascript
function foo(){
	var n=999;
	add=function(){
		n=n+1;
	}
	function bar(){
		console.log(n);
	}
	return  bar
}
var result=foo();
result();//999
add();
result();//1000
add();
result();//1001
```
这里，我们add函数前面没有var，所以在第一次调用foo的时候，就会把add这个函数注册到全局里面，再次调用就可以把n+1；这时候n并没有从999开始而是，沿着上次加的数开始加的，这时候N这个数就常驻在内存里了。

	所以这里要注意，不要滥用闭包，会造成内存泄漏，网页性能变差。

**闭包的经典例子**

```javascript
for(var i=1;i<=5;i++){
	setTimeout(
		function timer(){
			console.log(i);
		}, i*1000);
}
//每秒输出一个6，输出五秒
```
	
***思考：为什么这里不是每秒输出一个，然后12345 输出呢？确实5个6***

```javascript
for(var i=1;i<=5;i++){
	console.log(i);//这里输出的是12345，但是先输出了 才会输出下面函数里面的
	setTimeout(
		function timer(){
			console.log(i);
		}, i*1000);
}

```

延迟函数的回调函数都会在循环结束时才开始执行。

 我们想象着   执行函数会一直自动捕获相应的i  但是作用域的工作原理，i 在全局的作用域里面，其实就是一个i  就是6 然后 timer去拿了五次

那么怎么解决这个问题呢？

给settimeout函数自己的作用域吗

```javascript
for(var i=1;i<=5;i++){
	(function(){
		setTimeout(
		function timer(){
			console.log(i);
		}, i*1000);
	})(i);
}
```
这么做，结果还是没有改变为什么呢？

虽然有了自己的作用域，但是在调用阶段，函数在自己的作用域里面寻找i 发现没有找到，这时候去全局里面找，这时候全局里面也已经是6了 所以还是5个6

怎么来拿到想要的12345呢？

答案就是在i还是12345的时候，把值付给函数作用域里面的一个变量，比如j
```javascript
for(var i=1;i<=5;i++){
	(function(){
		var j=i;
		setTimeout(
		function timer(){
			console.log(j);
		}, j*1000);
	})();
}
//输出12345
```

这里为什么不是输出55555呢？  因为这里每次把I付给了J。但是每个j都是不一样的。都有自己的作用域。并不是一个j

**例子**

```javascript
//demo1
function coolModule(){
		var someThing="cool";
		var another=[1,2,3];
		function doSomeThing(){
			console.log(someThing);
		}
		function doAnother(){
			console.log(another.join('!'));
		}
		return {
			doSomeThing:doSomeThing,
			doAnother:doAnother
		}
	}
 var foo=	coolModule();
 foo.doSomeThing();//cool
 foo.doAnother();//1!2!3

//demo2
var foo=(function coolModule(id){
		function change(){
			publicAPI.identify=identify2;
		}
		function identify1(){
			console.log(id);
		}

		function identify2(){
			console.log(id.toUpperCase());
		}

		return publicAPI={
			change:change,
			identify:identify1
		}
})('marain');
foo.identify();//marain
foo.change();
foo.identify();//MARAIN

//demo3  模块机制

var  MyModules = (function (){
	var modules ={};
	function define(name,deps,impl){
		for (var i = 0; i <deps.length; i++) {
			deps[i]=modules[deps[i]];
		};
		modules[name]=impl.apply(impl,deps);
	}
	function get(name){
		return modules[name];
	}
	return {
		define:define,
		get:get
	}
})();

MyModules.define("bar",[],function(){
	function hello(who){

		return "let me introduce :"+who;
	}
	return {
		hello:hello,
	}
});

MyModules.define("foo",["bar"],function(bar){
	var hungry="hippo";

	function awesome(){
		console.log(bar.hello(hungry).toUpperCase());
	}

	return{
		awesome:awesome
	}
});

var bar=MyModules.get("bar");
var foo=MyModules.get("foo");

console.log(
	bar.hello("hippo")
);
foo.awesome();
//let me introduce :hippo
//LET ME INTRODUCE :HIPPO
```
