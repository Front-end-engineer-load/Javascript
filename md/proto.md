
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
