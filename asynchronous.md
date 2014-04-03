#Asynchronous
##1.public/subscribe
>node自身提供的events模块是发布/订阅模式的一个简单实现，node中部分模块都继承自它，这个模块比前端中大量DOM事件简单，不存在事件冒泡，也不存在preventDeafault、stopPropagation(),stopImmediatePropagation()等控制事件传递的方法。它具有addListener/on(),once(),removeListener,removeAllListeners()和emit()等基本的事件监听模式的方法实现。
			
	examples:
		emitter.on('event1',function(message){
			console.log(message);
		});
		emitter.emit('event1','i am message');
		
##2.promise/defferer
>Deffered 就是Promise的超集，它比promise多一项关键特性：可以直接触发。纯promise只允许添加多个调用，而且必须由其他什么东西来触发这些调用。
  使用resolve方法或者reject均可触发Deferred对象。
  		
  		var promptDeferred = new $.Defrred();
  		promptDefferred
  		.always(function(){
  			conosole.log('A choice was made:');
  		}).done(function(){
  			conosole.log('Starting game...');
  		}).fail(function(){
  			conosole.log('No game today...');
  		});
  		
  	var promptPromise = promptDefrred.promise();//纯promise对象,无resolve/reject
  	var promise1 = promptDefrred.promise();
  	var promise2 = promptDefrred.promise();
  	console.log(promise1 ===promise2)//true,多个promise对象和promptDeferred分享同样的stat(pending,resolved,rejected);
  	console.log(promise1 === promise1.promise());//true 对promise对象再调用promise方法，只不过产生一个指向相同对象的引用
  	
>使用promise方法的唯一理由就是“封装”。
###2.1jquery API中的promise对象

		Animation
	var $flash = $('.flash');
	var showPromise = $flash.show().promise();
	
	var slideUpDeferred = new $.Deferred();
	$('.menu').slideUp(slideUpDeferred.resolve);
	var slideUpPromise = slideUpDeferred.promise();	
	以下3种等效：
	$(onReady);
	$(document).ready(onReady);
	$.ready.promise().done(onReady);
	
####2.1.1向回调函数传据数

		var slashdotter = {
			comment:function(editor){
				conosole.log('Obviously',editor,'is the best text editor.');
			}
		};
		var grammarDeferred = new $.Deferred();
		grammarDefferred.done(function(verb,object){
			this[verb](object);
		});
		grammarDefferred.resolve.call(slashdotter,'comment','Emacs');
		//grammarDefferred.resolveWith(slashdotter,[comment,'Emacs']);
		//grammarDefferred.resolve.apply(slashdotter,['comment','Emacs']);
	》Obviously Emacs is the best text editor.
####2.1.2进度通知

		var nanowrimoing = $.Deferred();
		var wordGoal = 5000;
		nanowrimoing.progress(function(wordCount){
			var percentComplete = Math.floor(wordCount/wordGoal*100);
			$('#indicator').text(percentComplete +'% complete');
		});
		nanowrimoing.done(function(){
			$('#indicator').text('Good job!');
		});
		$('#document').on('keypress',function(){
			var wordCount = $(this).val().split(/\s/).length;
			if(wordCount>=wordCoal){							nanowrimoing.resolve();
			}
			nanowrimoing.notify(wordCount);
		});
####2.1.3Promise对象合并
>Promise如此强的的一个主要原因是，它允许我们把任务当成布尔量来处理。

	when:
	var gameReadying = $.when(tutorialPromise,gameLoadedPromise);
	gameReadying.done(startGame);
		
	var serverData = {};
	var getting1 = $.get('/1').done(function(result){serverData['1'] = result;});
	var getting1 = $.get('/2').done(function(result){serverData['2'] = result;});
	$.when(getting1,getting2).done(function(){
		//获得的信息现在都已位于serverData...
	});
>$.when 及其他能取用Promise对象的jQuery方法均支持传入非Promise对象作为参数。
而这些非Promise参数会被当成因相应参数位置已赋值而执行的Promise对象来处理。例如
		
		$.when('foo');//相当于匿名自执行promise对象并传递数据’foo‘
会生成一个因赋值'foo'而立即执行的Promise对象;
		
		var promise = $.Deferred().resolve('manchu'); 
		$.when('foo', promise).done(function(){
    		console.log(arguments);//['foo','manchu'];
		});
		
####2.1.4管道连接未来(pipe)
	var getPromise =$.get('/query');
	getPromise.done(function(){
		var postPromise = $.post('/query')		
	})
	getPromsie完成之前无法对postPromise绑定回调，因为posePromise还不存在
	
	var getPromise = $.get('query');
	var postPromise = getPromise.pipe(function(){
		return $.post('query');
	})
	postPromise.done(function(){});
##3Async.js
####3.1.1异步工作流次序问题
>假想先按字母顺序读取recipes(菜谱)目录中的所有文件，接着把读取出的这些内容连接成一个字符串并显示出来，使用同步方法很容易做到这点。

	var fs = require('fs');
	process.chdir('recipes');//改变工作目录
	var concatenation = '';
	//=======origin
	fs.readdirSync('.')
	  .filter(function(filename){
	  //跳过不是文件的目录
	  return fs.statSync(filename).isFile();
	}).forEach(function(){
		concatenation+=fs.readFileSync(filename,'utf8');
	});
    console.log(concatenation);
    
    //=======answer1
    fs.readdir('.',function(err,filenames){
		if(err) throw err;
		function readFileAt(i){
			var filename = filenames[i];
			fs.stat(filename,function(err,stats){
				if(err) throw err;
				if(!stats.isFile()) return readFileAt(i+1);
				fs.readFile(filename,'utf8',function(err,text){
					if(err) throw err;
					concatenation+=text;
					if(i+1 ===filenames.length){
						return console.log(concatenation);
					}
					readFileAt(i+1);
				})
			})
		}
		readFileAt(0);
	});
	
	//=======answer2
	var async = require('async');
	var fs = require('fs');
	process.chdir('recipes');

	var concatenation = '';

	var dirContents = fs.readdirSync('.');

	async.filter(dirContents,isFilename,function(filenames){
		async.forEachSeries(filenames,readAndConcat,onComplete);
	});
	function isFilename(filename,callback){
		fs.stat(filename,function(err,stats){
			if(err) throw err;
			callback(stats.isFile());
		});
	}
	function readAndContancat(filename,callback){
		fs.readFile(filename,'utf8',function(err,fileContents){
			if(err) return callback(err);
			concatenation += fileContents;
			callback();
		})
	}
	function onComplete(err){
		if(err) throw err;
		consolelog(concatenation);
	}
	
>Async.js提供了以下方法：
	
* reject/rejectSeries，与filter刚好相反
* map/mapSeries,1:1变换
* reduce/reduceRight,值的逐步变换；
* detect/detectSeries,找到筛选器匹配的值 
* sortBy,产生一个副本；
* some,测试是否至少有一个值符合给定标准
* every,测试是否所有值均符合标准
####3.1.2Async.js的错误处理技术
简单来说，Async.js遵守Node的约定。这意味着所有的I/O回调都形如*callback(er,results...)*，唯一的里外是结果为*布尔型*的回调。布尔型回调的写法就是callback(result),所以上一代码示例中的isFilename迭代器需要亲自处理错误。对于非布尔型的所有Async.js迭代器，传递非null/undefined的值作为迭代器回调的首参数将会立即因该错误值而调用完工回调(onComplete)，这正是readAndConcat不用throw也能工作的原因。
###3.2Asyncjs的任务组织技术
####3.2.1异步函数序列的运行
		funcs[0](function(){
			funcs[1](function(){
				funcs[2](onComplete);
			});
		});
*async.series*和*async.waterfall*均接受一组函数作为参数并顺序运行它们，二者给任何表中的函数均传递一个node风格的回调，其差别是，前者给各个任务的只有回调，而后者还会提供任务列表中前一任务的结果。

		var async = require('async');
		var start = new Date;
		async.series([
			function(callback){setTimeout(callback,100);},
			function(callback){setTimeout(callback,300);},
			function(callback){setTimeout(callback,200);}
			],function(err,results){
			concole.log('Conpleted in' +(new Date- start)+'ms');//600
		});

####3.2.2异步函数的并行运行
Async.js提供了series的并行版，即parallel，也接受一组形为function(callback){..}的函数作为参数，但会再加上一个（可选的）在触发最末回调后运行的完工时间处理器。

		var async = require('async');
		var start = new Date;
		async.parallel([
			function(callback){setTimeout(callback,100);},
			function(callback){setTimeout(callback,300);},
			function(callback){setTimeout(callback,200);}
			],function(err,results){
			concole.log('Conpleted in' +(new Date- start)+'ms');//300
		});
Async.js按照*任务列表的次序*向完工事件处理器传递结果，*而不是按照生成这些结果的次序*。这样，我们既拥有了并行机的性能优势，又没有失去结果的可预知性。
###3.3异步工作流的动态排队技术
series 和parallel的局限性：

* 任务列表是静态，一旦调用，不能再增减任务。
* 任务处于黑箱，无法查看进度，除非内部自行派发更新信息。
* 只有两个选择，要么完全没有并发性，要么不受限制的并发性。对文件I/O任务是个大问题，如果要操作上千个文件，当然不想因按顺序操作而效率低下，但如果试着并行执行所有操作，又很可能会激怒操作系统。

Async.js提供了一种可以解决上述问题的全能方法：queue。
####3.3.1深入理解队列




		
	

  	
  	
  		

