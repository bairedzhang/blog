###1.$()()()()/返回括号个数

   
	var $ = (function(){
		var counter = 0;
		var fn = function(){
			counter++;
			return arguments.callee;
		}
		fn.toString = function(){
			var tmp = counter;
			counter = 0;
			return tmp;
		}
		return fn;		
	})();
	//===以下方法有待改进
	var t = 0;
	var g = function(){
		arguments.callee.called = true;
		t++;
		var args = arguments.callee;
		arguments.callee.timer = setTimetout(function(){
			var nt = t;
			if(t!=0) console.log(nt);
			t =0;
		},100);
		
		return function(){
			if(arguments.callee.called = true){
				clearTimeout(arguments.callee.timer);
			}
			return args;
		}
	}

###2.设置ALERT为打印ALERT(即alert('ALERT'))

	window.__defineGetter__('ALERT',function(){return alert('ALERT')})
	or
	Object.defineProperty(window,'ALERT',{
		get:function(){
			return alert('ALERT');
		}
	})
###3.对象深拷贝

