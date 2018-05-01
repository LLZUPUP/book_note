# js 函数学习笔记
### 函数对象
```
函数就是对象，对象是“名／值”对的集合并拥有一个连到原型对象的隐藏连接。
函数对象在创建时会有一个prototype原型属性。
```
### 函数字面量
```
函数对象可以通过函数字面量来创建：
函数字面量包括4个部分，
1: 保留字function
2: 函数名（可以省略）即匿名函数
3: 括号内的参数，多个参数用逗号隔开
4: 花括号内的执行代码块
```

### 函数调用
```
1. 调用一个函数会暂停当前函数的执行，传递控制权和参数给调用的新函数。
2. 除了函数声明时的形参，还有2个附加的参数：this 和 arguments。 this非常重要，它的值取决于调用的模式。
3. js中有4种调用模式： 方法调用，函数调用，构造器调用和apply调用。
```
- 方法调用模式
  ```
    当一个函数被保存为对象的一个属性时，就可以通过这个对象的方法来调用此函数。
    方法可以使用this来访问自己所属的对象，所以函数（方法）可以从对象中取值或对此对象的值进行修改。
  ```
- 函数调用模式
    ```
    1. 当一个函数并非是对象的一个属性的时候，那么它就是被当作一个函数来调用的。
    2. 用此方法调用函数的话，this会被绑定到全局对象，那么当内部函数被调用时，this应该仍然绑定到外部函数的this变量上，因此应该在外部函数上定义一个that变量，并给它赋值为this。
    3. 那么内部的that变量就是外部的this，就可以访问到this。
    ```
    ```
    <!-- 给myObject 增加一个double方法 -->
    var sum = add(3,4); //sum的值为7.
    myObject.double = function() {
        var that = this;    //解决方法
        var helper = function(){
            that.value = add(that.value,that.value);
        }
        helper();   //函数调用模式
    };
    myObject.double();  //方法调用模式  
    ```

- 构造器调用模式
    ```
    如果在一个函数前面带上一个 new 来调用，那么背地里将会创建一个连接到该函数的prototype 成员的新对象，同时this 会被绑定到那个新对象上。
    //创建一个名为Quo的构造器函数。它构造一个带有status属性的对象。
     var Quo = function(string) {
         this.status = string;
     };

     //给Quo的所有实例提供一个名为get_status的公共方法。
     Quo.prototype.get_status = function() {
         return this.status;
     };

     //构造一个Quo 实例。
     var myQuo = new Quo("confused");

     myQuo.get_status();        //"confused"
    ```

- Apply调用模式
    ```
    apply方法让我们构建一个参数数组传递给调用函数。
    apply方法接收2个函数，第一个是要绑定给this的值，第2个就是一个参数数组。

    //构造一个包含两个数字的数组，并将它们相加。
    
    var array = [3,4];
    var sum = add.apply(null,array);

    //构建一个包含status成员的对象。
    var statusObject = {
        status: 'A-OK'
    };

    //statusObject 并没有继承自Quo.prototype,但我们可以在statusObject 上调用get_status方法，尽管statusObject 并没有一个名为get_status的方法。

    var status = Quo.prototype.get_status.apply(statusObject);
    //status 值为'A-OK'。
    ```

### 参数
```
Arguments参数
当函数被调用时，会得到一个免费配送的参数，那就是arguments数组。
函数可以通过arguments数组来访问所有此函数被调用时所被传入的实际参数列表，包括那些没有被分配给函数声明时定义的形式参数的多余参数
也就是说，可能可以编写一个无须传入行参的函数，只要传入实参就可以实行
```
```
var sum = function() {
    var sum = 0;
    for(var i= 0;i<arguments.length;i++){
        sum += arguments[i];
    }
    return sum;
};
console.log(sum(4,8,9,12,234,456,68));  //791

arguments 并不是一个真正的数组，它只是一个“类似数组”的对象，它只有一个length的属性，并没有任何数组的方法。
```

### 异常
```
异常是干扰程序的正常流程的不寻常的事故。当发生这样的事故时，应该抛出一个异常：

var add = function(a,b) {
    if(typeof a !== 'number' || typeof b !== 'number') {
        throw {
            name: 'TypeError',
            message: 'add needs numbers'
        };
    }
    return a + b;
}

var try_it = function() {
    try {
        add("seven");
    }catch(e) {
        document.writeIn(e.name + ':' + e.message);
    }
}
try_it();

如果在try 代码块内抛出了一个异常，控制权就会跳转到它的catch 从句。
```

### 递归
```
var factorial = function(a,b) {
    b = b || 1;
    if(a < 2) {
        return b;
    }
    return factorial(a - 1,b * a);
};
console.log(factorial(4));  //24

递归函数就是会直接或间接地调用自身的一种函数。
```
### 作用域
```
作用域的好处是内部函数可以访问定义它们的外部函数的参数和变量（除了this和arguments）。
```
### 闭包
```
var add_the_handlers = function(nodes) {
    var i;
    var helper = function(i) {
        return function(e) {
            alert(i);
        };
    };
    for(i=0;i<nodes.length;i++) {
        nodes[i].onclick = helper(i);    
    }
};
```
### 回调
```
request = prepare_the_request();
send_request_asynchronously(request,function(response) {
    display(response);
});
传递一个函数作为第一个参数给send_request_asynchronously函数，一旦接收到响应，它就会被调用。
```

### 模块
```
模块是一个提供接口却隐藏状态与实现的函数或对象，它利用了函数作用域和闭包来创建被绑定对象与私有成员的关联。
模块模式的一般形式是： 一个定义了私有变量和函数的函数；利用闭包创建可以访问私有变量和函数的特权函数；最后返回这个特权函数，或者把他们保存到一个可访问到的地方。
```


