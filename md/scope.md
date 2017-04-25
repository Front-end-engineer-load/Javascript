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
