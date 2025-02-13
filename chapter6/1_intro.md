# 函数式编程

函数式编程通过数学函数求值来处理程序计算，它具有代码简洁易读，更接近于自然语言，开发效率高等优点。

####函数式特点

他有如下特点：

1. 函数为一等公民
2. 声明式
3. 值不可变
4. 引用透明

**函数为一等公民**

一等公民（first class）简单的说就是可以当做值来使用的，比如作为变量的值，作为函数的返回值。而函数作为一等公民就是说函数也可以作为参数进行传递，或者作为另一个函数的返回值。因为函数是一段代码，所以函数作为一等公民就意味着代码段及代码段内包含的变量也是可以传递的。

```haskell
--Haskell 函数作为参数传递给另一个函数
map (+3) [1,2,3]
```

如上`map`是一个函数，`(+3)`也是一个函数（这个函数传递了变量3作为加法的右操作数），`[1,2,3]`是另一个参数，这里把函数`(+3)`当做参数传递给了另一个函数`map`。

**声明式**

函数式编程是一种声明式编程范式，声明式通过表达式计算、函数声明来代替命令式编程中的逻辑控制。

```haskell
--Haskell 函数定义

isZero:: Int->String
isZero 0 = "yes"
isZero _ = "no"

isZero 3    --no
```
如上声明了一个函数isZero，其中Int->String表示接受一个整数类型，返回一个字符串类型。然后声明了`isZero 0 = "yes"`表示当参数为0时返回yes。最后`isZero _ = "no"`表示除此之外任意参数都返回no。

isZero会按照声明的顺序对输入参数进行判断，先判断输入参数是否为0，如果为0则返回yes，否则执行下一个模式，一般最后一个模式都用`_`匹配任意值，相当于if else中的else。

对比声明式编程看下命令式如何实现isZero。

```java
//Java 命令式编程
public static String isZero(int num){
  if(num == 0)  return "yes";
  else          return "no";
}

System.out.print(isZero(0));    //yes
```

**值不可变**

1. 函数式编程是没有副作用的；
2. 函数式要求值是不可修改的，状态也是不可改变的；
3. 对输入参数进行函数运算不允许修改源数据，相应的会生成一个新的拷贝作为运算结果。
4. 函数运算只依赖于输入参数，不会改变函数外部数据的值或状态。
5. 面向对象语言通过封装解决状态改变的问题，而函数式语言通过函数变换避免状态改变。

**引用透明**

引用透明是指函数的运算不会受到外部变量的影响，其计算结果只依赖于输入参数，输入参数相同则返回结果相同。

引用透明的特性使函数式语言使其具有如下优势：

1. 如果当前函数表达式返回值不再需要，可以直接删除，不会对函数表达式外数据产生影响。
2. 多次调用一个纯函数输入相同参数，返回结果相同，且不存在副作用。
3. 替换两个纯函数表达式的执行顺序不会影响执行结果，因此适合做并行开发。
4. 无副作用的表达式运算允许编译器自由结合表达式的运算结果，可以将多个函数组合使用。

####对比命令式编程、面向对象编程

1. 命令式编程是按照程序是一系列改变状态的命令来建模的一种编程风格，函数式编程则将程序描述为表达式和变换，以数学方程的形式建立模型。
2. 命令式编程一次循环完成多个任务，更注重性能，而函数式一个任务可能需要多次循环，更注重语义。
3. 面向对象是对数据进行封装，而函数式则对行为进行封装。
4. 面向对象编程中通过封装不确定因素使代码更容易被人理解，而函数式编程通过尽量减少不确定因素来使代码容易被人理解。
5. 面向对象提倡对具体的问题建立专门的数据结构及操作，而函数式则提倡使用几种基本的数据结构（数组、列表）及针对这几种结构高度优化的操作来完成程序任务。
6. 函数式编程将程序描述为表达式和变换，像数学方程一样建立模型。更注重高层的抽象，而非底层的细节，开发者需要用高阶函数调整底层运转。
7. 函数式编程能在更细小的层面上重用代码。

###完美数的例子：

上面像说明书一样列举了函数式的特点，并对比了与命令式的区别。但这些终究是教科书一样的文案，没有实际的例子是难以认清函数式编程的真面目的，下面我们来通过完美数例子来对比命令式编程与函数式编程。

>完美数是指除了自身以外的所有他的正约数之和等于其本身的数字，比如6(1+2+3=6,其中6的非自身正约数有1，2，3)。

在说明编码之前，先说明下如何判断一个数是否为完美数。

1. 取1到这个数之间的所有数。
2. 把这些数中能整除它的数筛选出来。
3. 对筛选出来的数字求和。
4. 判断是否与这个数自身相等。

这里我们用JavaScript实现完美数例子：

```javascript
//JavaScript 命令式实现完美数

(function(){
  var factors = []
  ,   number
  ,   sum = 0
  ,   result;

  number = 496;

  getFactors();  
  aliquoSum();
  result = isPerfect();

  if(result)   console.log(number + " is perfcet");
  else         console.log(number + " is not perfect");
  
  //取得所有正公约数
  function getFactors(){
    for(i = 1;  i < number; i = i+1){
       if(isFactor(i))  factors.push(i); 
    }
  }
  
  //判断是否为公约数
  function isFactor(pontential){
    return number % pontential === 0;
  }
  
  //对所有正公约数求和
  function aliquoSum(){
    for(i = 0; i < factors.length; i = i+1){
      sum = sum + factors[i];
    }
  }

  //判断是否为完美数
  function isPerfect(){
    return sum === number;
  }

})();    
//496 is perfect
```

可以看到我们先用getFactors方法取得1到当前数之间所有数字，然后通过isFactor方法找出可以整除的数记录到factor数组。最后把数组中所有元素相加，判断是否与这个数字相等，如果相等则说明这个数字是完美数。

好，接下来我们看看函数式是如何实现的（这里引用了JavaScript的函数式库underscore.js）。

```javascript
//JavaScript 函数式编程实现完美数

var _ = require("underscore")
,   number = 496;

if(
  _.isEqual(number,(
    _.reduce(
      _.filter(
        _.range(1, number)
        , function(n){ return number%n == 0 })
      , function(p,n){ return p+n }
      , 0)
    )
  )
){  
  console.log(number + " is perfect");    
  //496 is perfect
}
```
我们从最里层的调用开始看\_\.range(1, number)会帮我们生成一个从1到number的数组，然后我们把这个数组作为参数应用到filter函数，这个函数会把表达式`number%n == 0`为真的数字返回，也就是能整除的数字。然后我们把这些过滤后得到的数字传递给reduce函数，并通过匿名函数`function(p,n){ return p+n }`求和，最后将求和得到的结果传入isEqual函数，判断是否与number相等。

这里可以看出函数式编程通常将数据作为输入，通过一个接一个的函数进行映射、过滤、折叠处理，最终返回出一个计算结果。

对比命令式编程，函数式代码更加简洁清晰，容易阅读。

针对上述代码，我们还可以通过链式调用进行优化，使代码更加简洁清晰。

```javascript
//JavaScript 通过链式调用使函数式代码更加语义化

var number = 496
,   isPerfect = _.chain(_.range(1, 300))
    .filter(function(n){ return 300%n == 0 })
    .reduce(function(p,n){ return p+n }, 0)
    .value();
if(isPerfect)  console.log(number, "is perfect");
//496 is perfect
```
我们通过\_\.chain函数可以将数组传入管道，然后就可以通过链式调用对数据进行处理，最后调用value函数计算最终结果。