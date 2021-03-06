##RegExp
		var expression = /pattern/flags;
		var expression = new RegExp("pattern",flags);
其中的模式（pattern）部分可以是任何简单或复杂的正则表达式，可以包含字符类、限定符、分组、向前查找以及反向引用。
** 由于RegExp构造函数的模式参数是字符串，所以在某些情况下要对字符进行双重转义。所有元字符都必须双重转义，那些转义过的字符也是如此。**

- g:全局模式，应用于所有字符串，而非在发现第一个匹配项时立即停止
- i:不区分大小写
- m:多行模式

所有元字符需要转义。包括：
		 
		( [ { \ ^ $ | ) ? * + . ]}

直接量字符:
		
		\o 		NUL字符
		\t		制表符
		\n		换行符
		\v		垂直制表符
		\f		换页符
		\r		回车符
		\uxxxx	16进制数 xxxx制定的unicode

字符类:
		
		.		除换行符和其它Unicode行终止符之外的任意字符
		\w		任何ASCII字符组成的单词，等价于[a-zA-Z0-9]
		\W		任何不是ASCII字符组成的单词，等价于[^a-zA-Z0-9]
		\s		任何Unicode空白符
		\S		任何非Unicode空白符的字符，注意\w和\S不 
		\d		任何ASCII数字，等价于[0-9]
		\D		[^0-9]
		[\b]	退格直接量(特例)
					
重复字符语法：
		
		？		{0,1}
		+		{1,}
		*		{0,}
		
**使用正则表达式字面量和使用RegExp够做函数创建的正则表达式不一样。前者始终会共享一个RegExp实例，后者每次都是创建一个新实例。**

###实例属性
RegExp每个实例都有下列属性：

- global:boolean
- ignoreCase:boolean
- lastIndex:int,表示开始搜索下一个匹配项的字符位置，从0算起
- multiline:boolean
- source:正则表达式的字符串表示，按照字面量形式而非传入构造函数中的字符串模式返回

###实例方法
>主要方法是`exec()`，该方法专门为捕获设计的。返回包含第一个匹配项信息的数组，没有返回null。数组包含两个额外的属性：`index`和`input`。index表示匹配项在字符串中的位置，input表示应用正则表达式的字符串。数组第一项是与整个模式匹配的字符串，其他项是与模式中的捕获组匹配的字符串（没有捕获组则只包含第一项）。**对于exec而言，即使在模式中设置了全局标志（g），它每次也只会返回一个匹配项。在不设置全局标志的情况下，在同一个字符串上多次调用exec()方法将始终返回第一个匹配项的信息。在设置全局标志的情况下，每次调用将会字符串中继续查找新匹配项**

>第二个方法是`test()`

	input      		$_ 		最近一次要匹配的字符串
	lastMatch  		$&		最近一次的匹配项
	lastParen		$+		最近一次匹配的捕获组
	leftContext    	$`		input字符串中lastMatch之前的文本
	multiline		$*		布尔值，表示是否所有表达式都使用多行模式。
	rightContext	$`		input字符串中lastMatch之后的文本
	
##语法
![Alt text](pic/reg1.png)
![Alt text](pic/reg2.png)
![Alt text](pic/reg3.png)
![Alt text](pic/reg4.png)
	