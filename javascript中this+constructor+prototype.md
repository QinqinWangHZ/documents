####javascript中this，constructor,prototype
this

---

	this代表当前对象，如果在全局作用范围内使用this，则指代当前对象windows；如果在函数中使用this，则this指代什么是根据运行时此函数在什么对象上被调用(this是在运行时决定的，而不是函数定义时)。注：可使用apply和call两个全局方法来改变函数中this的具体指向。
	//定义一个全局函数
	function foo() {
		console.log(this.fruit);
	}
	//定义一个全局变量
	var fruit = "apple";
	//自定义一个对象
	var pack = {
		fruit:"orange";
	};
	//此时函数foo中this指向window对象
	//这种调用方式和window.foo();完全等价
	foo(); //"apple"
	//等价于window.foo();
	foo.apply(window); //"apple"
	//此时foo中的this ＝＝＝ pack
	foo.apply(pack); //"orange"
	
prototype

---

	prototype本质上还是一个JavaScript对象,并且每个函数都有一个默认的prototype属性，如果这个函数被用在创建自定义对象的场景中，我们称这个函数为构造函数。在函数中添加一个prototype属性，这个属性用来保存一些供所有"同类"实例对象共享使用的属性和方法。
	//构造函数
	function Person (name) {
		this.name = name;
	}
	//定义Person的原型，原型中的属性可以被自定义对象引用
	Person.prototype = {
		getName: function () {
			return this.name;
		}
	}
	var value = new Person ("haorooms");
	console.log(value.getName()); //"haorooms"
	
	JavaScript所有的固有数据类型都具有只读的prototype属性 （这是可以理解的：因为如果修改了这些类型的prototype属性，则哪些预定义的方法就消失了）， 但是我们可以向其中添加自己的扩展方法。
	//向JavaScript固有类型Array扩展的一个获取最小值的方法
	Array.prototype.max = function () {
		var max = this[0];
		for (var i = 1; i < this.length; i ++) {
			if(this[i] > max) {
				max = this[i];
			}
		}
		return max;
		
	};	
	//在任意的Array的实例上调用max方法
	console.log([1,30,40,12].max()); //40
	
	注意：这里有一个陷阱，向Array的原型中添加扩展方法后，当使用for-in循环数组时，这个扩展方法也会被循环出来。 下面的代码说明这一点（假设已经向Array的原型中扩展了max方法）：
	 var arr = [1, 56, 34, 12];
	 var total = 0;
	 for (var i in arr) {
	 	total += parseInt(arr[i],10);
	 }
	 console.log(total); //NaN
	 
	 解决办法很简单：
	 var arr = [1, 56, 34, 12];
	 var total = 0;
	 for (var i in arr) {
	 	if(arr.hasOwnProperty(i)) {
	 		total += parseInt(arr[i],10);
	 	}
	 }
	 console.log(total); //103
	 
constructor

---
	
	constructor属性返回对创建此对象的数组函数的引用
	constructor始终指向创建当前对象的构造函数.在JavaScript中，每个具有原型的对象都会自动获得constructor属性。除了arguments、Enumerator、Error、Global、Math、RegExp、Regular Expression等一些特殊对象之外，其他所有的JavaScript内置对象都具备constructor属性。例如：Array、Boolean、Date、Function、Number、Object、String等
	//等价于var foo = new Array(1,56,34,12);
	var arr = [1, 56, 34, 12];
	console.log(arr.constructor === Array); //true
	
	//等价于var foo = new Function ();
	var Foo = function () {};
	console.log(Foo.constructor === Function); //true
	
	//由构造函数实例化一个obj对象
	var obj = new Foo();
	console.log(obj.constructor === Foo); //true
	
	//将上面代码合并
	console.log(obj.constructor.constructor === Function); //true
	
	------
	当constructor遇到prototype时，这个prototype的constructor默认指向这个函数。
	function Person (name) {
		this.name = name;
	};
	Person.prototype.getName = function () {
		return this.name;
	};
	var p = new Person("haorooms");
	console.log(p.constructor === Person); //true
	console.log(Person.prototype.constructor === Person); //true
	//将上两行代码合并
	console.log(p.constructor.prototype.constructor === Person); //true
	
	**当我们重新定义函数的prototype时（注意和上例区别，这里不是修改而是覆盖）
	function Person (name) {
		this.name = name;
	};
	Person.prototype = {
		getName: function () {
			return this.name;
		}
	};
	var p = new Person("haorooms");
	console.log(p.constructor === Person); //false
	console.log(Person.prototype.constructor === Person);//false
	console.log(p.constructor.prototype.constructor === Person);//false
	原来因为覆盖Person.prototype时，等价于如下操作：
	Person.prototype = new Object({
		getName: function () {
			return this.name;
		}
	});
	而constructor始终指向创建自身的构造函数，所以此时Person.prototype.constructor ===Object,即是：
	function Person (name) {
		this.name = name;
	};
	Person.prototype = {
		getName: function () {
			return this.name;
		}
	};
	var p = new Person("haorooms");
	console.log(p.constructor === Object); //true
	console.log(Person.prototype.constructor === Object);//true
	console.log(p.constructor.prototype.constructor === Object); //true
	
	**修正该问题，需重新覆盖Person.prototype.constructor
	function Person (name) {
		this.name = name;
	};
	Person.prototype = {
		getName: function () {
			return this.name;
		}
	};
	Person.prototype.constructor = Person;
	var p = new Person("haorooms");
	console.log(p.constructor === Person); //true
	console.log(Person.prototype.constructor === Person);//true
	console.log(p.constructor.prototype.constructor === Person); //true
	**也可以这么写：
	function Person (name) {
		this.name = name;
	};
	Person/prototype = {
		constructor:Person, //指定constructor
		getName: function () {
			return this.name;
		}
	}
	
####其他
apply&&call

	apply方法：
	语法：apply([thisObj[,argArray]])
	定义：应用某一对象的一个方法，用另一个对象替换当前对象
	说明：如果argArray不是一个有效数组或者不是arguments对象，那么将导致一个TypeError。如果没提供argArray和thisObj任何一个参数，那么Global对象将被用作thisObj，并且无法被传递任何参数
	
	call方法：
	语法：call([thisObj[,arg1[,arg2[,[,argN]]]]])
	定义：调用一个对象的一个方法，以另一个对象替换当前对象
	说明：call方法可以用来代替另一个对象调用一个方法。call方法可将一个函数的对象上下文从初始的上下文改变为由thisObj指定的新对象。
	如果没有提供thisObj参数，那么Global对象被用作thisObj.
	
	function Animal () {
		this.name = "Animal";
		this.showName = function () {
			alert(this.name);
		}
	}
	function Cat() {
		this.name = "Cat";
	}
	var animal = new Animal();
	var cat = new Cat();
	
	//通过call或者apply方法，将原本属于Animal对象的showName方法交给对象cat来使用了。
	//输出结果为“Cat”
	animal.showName.call(cat,",");
	//animal.showName.apply(cat,[]);
	
	call的意思是animal的方法放在cat上执行，原来cat是没有showName() 方法，现在是把animal 的showName()方法放到 cat上来执行，所以this.name 应该是 Cat
	
parseInt()
	
	语法：parseInt(string, radix)
	
	参数		描述
	string	必需。要被解析的字符串。
	radix	可选。表示要解析的数字的基数。该值介于 2 ~ 36 之间。
	
	parseInt() 函数可解析一个字符串，并返回一个整数。
	当参数 radix 的值为 0，或没有设置该参数时，parseInt() 会根据 string 来判断数字的基数。
	当忽略参数 radix , JavaScript 默认数字的基数如下:
	如果 string 以 "0x" 开头，parseInt() 会把 string 的其余部分解析为十六进制的整数。
	如果 string 以 0 开头，那么 ECMAScript v3 允许 parseInt() 的一个实现把其后的字符解析为八进制或十六进制的数字。
	如果 string 以 1 ~ 9 的数字开头，parseInt() 将把它解析为十进制的整数。
	
	**提示和注释
	注意： 只有字符串中的第一个数字会被返回。
	注意： 开头和结尾的空格是允许的。
	注意：如果字符串的第一个字符不能被转换为数字，那么 parseFloat() 会返回 NaN。
	注意：在字符串以"0"为开始时旧的浏览器默认使用八进制基数。ECMAScript 5，默认的是十进制的基数。
	
hasOwnProperty()
	
	hasOwnProperty()函数用于指示一个对象自身(不包括原型链)是否具有指定名称的属性。如果有，返回true，否则返回false。
	该方法属于Object对象，由于所有的对象都"继承"了Object的对象实例，因此几乎所有的实例对象都可以使用该方法。
	此方法不会检查对象的原型链中是否存在该属性，该属性只有是对象本身的一个成员才会返回true。
	function Site(){
    	this.name = "CodePlayer";
    	this.url = "http://www.365mini.com/";

    	this.sayHello = function(){
        	document.writeln("欢迎来到" + this.name);
    	};
	}

	var obj = {
    	engine: "PHP",
    	sayHi: function(){
        	document.writeln("欢迎访问" + this.url);
    	}
	};
	// 使用对象obj覆盖Site本身的prototype属性
	Site.prototype = obj;
	var s =  new Site();
	console.log( s.hasOwnProperty("name") ); // true
	console.log( s.hasOwnProperty("sayHello") ); // true
	
	// 以下属性继承自原型链，因此为false
	console.log( s.hasOwnProperty("engine") ); // false
	console.log( s.hasOwnProperty("sayHi") ); // false
	console.log( s.hasOwnProperty("toString") ); // false

	// 想要查看对象(包括原型链)是否具备指定的属性，可以使用in操作符
	console.log( "engine" in s ); // true
	console.log( "sayHi" in s ); // true
	console.log( "toString" in s ); // true
