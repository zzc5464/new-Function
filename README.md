# new-Function  深入了解所有函数实例的鼻祖Function
## argument(超级重点)
> 基本上是所有公司的必考面试题
- 它代表对象的实例

        function checkVarCount(a, b) {
            if (checkVarCount.length == arguments.length) {
                alert("形参和实参个数一样");
            }else{
                alert("形参和实参的个数不一样");
    
            }
        }
1. 形参就是函数写好,先填上去的参数,如a,b
2. 实参就是用户传进去的实际参数
3. arguments只有在代码运行的时候才起作用
4. arguments是一个数组，保存函数的参数 -- 准确的说是伪数组
## call
### 供爷法则

      //对象1
     var myclass={
         getAllStudentsNumbers:function(){
             return 130}
     };
      //对象2
      var student={
         getDetail:function(){
             return {name:'莉莉',aihao:'唱歌跳舞'}
         }
     };
      //借用 -- 供爷法则
    console.log(myclass.getAllStudentsNumbers.call(student))
    
    
> 为什么叫供爷法则呢?

> 因为借给你的那个债主写在前面
- 如果你需要传参数

      console.log(myclass.getAllStudentsNumbers.call(student,10,200))

- 另外一种方法

      function add(a, b) {
        alert(a + b);
      }

      function sub(a, b) {
        alert(a - b);
      }
      add.call(sub, 3, 1);
> call也能使用在函数上,因为函数也是一种对象                                                         
### call的使用方法
- 用于改变this

      var value="全局变量";
      /*函数中默认this指向window*/
      function Fun1(){
        console.log(this.value);//当前this=window
      }
> 所有的全局变量都指向window对象

    Fun1.call(window);
> 指向的仍然是window,因为fun1自己指向的就是window
    
        function Fun1(){
          console.log(this.value);
        }
        Fun1.call(document.getElementById('myBtn'));
        //改变this指向自身,这回指向了myBtn.会得到此btn的值
### call的作用
1. 借用另外一个对象的方法,而不用拷贝.
2. 将一个伪数组变成一个真数组
> 伪数组:就是一个带有length属性的json对象,它的key都是1,2,3,...开头的
3. 它和真数组的区别:
> 都是模拟集合

> 伪数组每次都要自己去计算length的个数,自己去拼装对象

> 真数组有自己的方法:push pop join....

> document.getElementsByTagName("div")和argument这些都是一个伪数组

    var divs = document.getElementsByTagName("div")
    console.log(divs.length)
    /*说明他不是一个数组，无法访问里面的方法*/
    divs.pop().style.background='green'//会报错
> 使用如下方法,可以转换成真数组

    /*Array.prototype.slice.call(arguments)能将具有length属性的对象转成数组*/
    /*这是一种固定用法*/
> jQuery和其他很多框架就是通过伪数组实现的
#### 实际用法例子

        function add(){
          var sum=0;
       /* arguments.push(10)  //报错*/
          var arr = Array.prototype.slice.call(arguments)
          arr.push(10)  //报错
          for(var i=0;i<arr.length;i++){
              sum+=arr[i]
          }
          return sum;
        }

    var sum = add(1,2,3,4,5)

    console.log(sum)
#### 你也可以自定义一个伪数组
    var fackArray1 = {0:'first',1:'second',length:2};
    Array.prototype.slice.call(fackArray1);//  ["first", "second"]

    var fackArray2 = {length:2};
    Array.prototype.slice.call(fackArray2);//  [undefined, undefined]
    
## apply
- apply和call的功能,一毛一样.
- 仅一点,传参不一样
> call是平铺传参的

    //借用 -- 供爷法则
    console.log(myclass.getAllStudentsNumbers.call(student,10,200))
> apply则是把所有参数放到数组里面

    //借用 -- 供爷法则
    console.log(myclass.getAllStudentsNumbers.apply(student,argument))
    //相当于
    console.log(myclass.getAllStudentsNumbers.apply(student,[10,200]))
    
### 作用
> 很多时候在框架中需要用到一些算法,有关于数组的

> 常用写法:

    /*传统方式写法*/
    function getMax(arr){
        var arrLen=arr.length;
        for(var i=0,ret=arr[0];i<arrLen;i++){
            ret=Math.max(ret,arr[i]);
    }
    return ret;
    }
    console.log(getMax([1,2,3,4,5,6,7]))

> 这么写太麻烦,可以运用apply的巧妙特性来简化.

    function getMax2(arr){
        return Math.max.apply(null,arr);
       /* return Math.max.call(null,1,2,3,4,5);*/
    }
    console.log(getMax2([1,2,3,4,5,6,7]));
> 有非常多的运用常见要用到apply,划重点~

> apply经常用来处理数组的计算问题
## caller
> 返回函数调用者

> caller的应用场景 主要用于察看函数本身被哪个函数调用

        function fn() {
        //        判断某函数是否被调用
                if (fn.caller) {
                        alert(fn.caller.toString());
                } else {
                        alert("函数直接执行");
                }
        }
        function AAAA() {
                fn();
        }
        AAAA();
## callee
- 返回正被执行的 Function 对象，也就是所指定的 Function 对象的正文.
- callee是arguments 的一个属性成员，它表示对函数对象本身的引用
- arguments.callee.length可以获取实参参数
> 可以用来判断实参和形参是否一致

> 较常用于递归
- 什么是递归:一个方法，自己调用自己，用上一次调用得出的结果作为这次的参数
        
        //传统的递归
        var fn=function(n){
                if(n>0) return n+fn(n-1);
                return 0;
        }
        alert('采用传统方式'+fn(10))
        
        
        
        //callee的递归方式
        var fn=(function(n){
        if(n>0) return n+arguments.callee(n-1);
        return 0;
        })(10);
        alert('采用callee方式:'+fn)
### 传统方式的缺点:
1. 破坏了,零重复法则,当一旦函数名称更改,需要更改多处
2. fn是一个全局变量,fn内部一般使用局部bianliang,而这里是一个全局变量,这是一个潜在的全局变量污染
- 递归的好处:这样就让代码更加简练。又防止了全局变量的污染.

## constructor

    function Student(name) {
        this.name = name;
    }
    var zhangsan = new Student('张三');
    if (zhangsan.constructor == Student)
    document.write("zhangsan是根据构造函数<strong>Student</strong>创造（实例化）出来的"+"<br />");
> 就是用来判断实例的构造函数是谁的属性.

## prototype
> 当定义好一个函数的时候,会自动生成一个prototype属性

    var product = function(){}

    /*自动有一个 prototype属性 它是一个对象--- 原型对象*/

    product.prototype.buy=function(){}

    product.prototype={}
## object
- object的__proto__指向Function对象的原型
   

        //Object对象是Function对象的一个实例
        console.log(Object.__proto__ === Function.prototype)
        /*Function.prototype对象的原型的__proto__属性指向Object.prototype*/
        console.log(Function.prototype.__proto__ === Object.prototype)
        /*object的原型的__proto__属性为null*/
        console.log(Object.prototype.__proto__ )
- 总结:一个自定义对象可以访问到Function对象的所有属性和方法，也可以访问到Object对象的所有属性和方法
## 内置对象的实例指向
> 全都指向Function.prototype

    console.log(Number.__proto__ === Function.prototype)  // true
    console.log(Boolean.__proto__ === Function.prototype) // true
    console.log(String.__proto__ === Function.prototype)  // true
    console.log(Object.__proto__ === Function.prototype)  // true
    console.log(Function.__proto__ === Function.prototype) // true
    console.log(Array.__proto__ === Function.prototype)   // true
    console.log(RegExp.__proto__ === Function.prototype)  // true
    console.log(Error.__proto__ === Function.prototype)   // true
    console.log(Date.__proto__ === Function.prototype)    // true
    var obj = new Object()
    /*object的实例指向Object的原型对象*/
    console.log(obj.__proto__ === Object.prototype)
## 自定义对象的原型链
    
    function Product(){}
    Product.prototype={

    }
    var iphone = new Product()
    console.log(Product.prototype.__proto__)
- 总结:一个自定义对象可以访问到Function对象的所有属性和方法，也可以访问到Object对象的所有属性和方法

## object.prototype是一切链式循环的终点    老大

## function.prototype可以理解为老二










