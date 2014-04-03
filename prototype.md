#Prototype

##创建对象
###工厂模式

     function createPerson(name,age,job){
     	var o = new Object();
     	o.name = name;
     	o.age = age;
     	o.job = job;
     	o.sayName = function(){
     		alert(this.name);
     	};
     	return o;
     }
     
###构造函数模式

		function Person (name,age,job){
			this.name = name;
			this.age = age;
			this.job = job;
			this.sayName = function(){
				alert(this.name);
			}
		}
		
###原型模式

		function Person (name,age,job){
			this.name = name;
			this.age = age;
			this.job = job;
		}
		Person.prototype={
			sayName:function(){
				alert(this.name);
			}
		}
		
####理解原型
>无论什么时候，只要创建了一个新函数，就会根据一组特定的规则为该函数创建一个 prototype属性，这个属性指向函数的原型对象。默认所有原型都会获得一个constructor属性，指向prototype所在函数的指针。

		isPrototypeOf(),getPrototypeOf(),hasOwnProperty();
		
		var Person = function (){};
		var person = new Person();
		等价于：
			var person = {};
			person.__proto__ = Person.prototype;
			Person.call(person);