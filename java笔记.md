# JAVA基础笔记

## 封装

### static

静态成员:
1. 类对象共享
2. 类加载时产生，销毁时释放

静态方法:
1. 不可在内调用当前类中的非静态方法，不能使用当前类中的非静态属性
2. 只能通过对象实例化后调用非静态方法

### 代码块

静态代码块,最先执行,无论多少次实例化都只会执行一次
1. 不可在内调用当前类中的非静态方法，不能使用当前类中的非静态属性
2. 只能通过对象实例化后调用

构造代码块，在静态代码块之后执行，每次实例化后都会执行

```java
{
	System.out.println("构造代码块");
}

static {
	System.out.println("静态代码块");
}
```

### this关键字用法

1. 不能使用静态类和方法
2. 使用`this`构造方法时，不能在`this`关键字前使用任何语句

## 继承

### 构造方法和super

继承关系中，父子类构造方法的访问特点：

1. 子类构造方法当中有一个默认隐含的“super()”调用，所以一定是先调用的父类构造，后执行的子类构造。
2. 子类构造可以通过super关键字来调用父类重载构造。
3. super的父类构造调用，必须是子类构造方法的第一个语句。不能一个子类构造调用多次super构造。
总结：
子类必须调用父类构造方法，不写则赠送super()；写了则用写的指定的super调用，super只能有一个，还必须是第一个。

super可以在子类中调用父类中方法重写前的方法或是参数
super关键字的用法有三种：
  1. 在子类的成员方法中，访问父类的成员变量。
  2. 在子类的成员方法中，访问父类的成员方法。
  3. 在子类的构造方法中，访问父类的构造方法。
**注：**
1. 父类的构造方法不可被继承，不可被重写
2. 子类默认调用父类的无参构造方法，可通过super()访问父类的其他方法
3. 使用super()调用父类的构造方法时，必须在有效代码的第一行
4. this和super都不可以在静态方法中调用
5. this和super调用无参构造方法时只能使用一条语句

### final

`final class`：该类无子类
`final` 方法：该方法无法被子类重写，但是可以被子类正常继承使用

`final` 方法内局部变量

`final` 类成员属性：赋值过程：1.定义直接初始化 2.构造方法 3.构造代码块
`final` 引用类型变量：初始化后不可在指向另一个对象，但对象内容可变
`final static`：全局不容许修订内容
注：final不可修饰构造方法

### 访问成员变量
在父子类的继承关系当中，如果成员变量重名，则创建子类对象时，访问有两种方式：

1. 直接通过子类对象访问成员变量：等号左边是谁，就优先用谁，没有则向上找。
2. 间接通过成员方法访问成员变量：
   局部变量：         直接写成员变量名
   本类的成员变量：    this.成员变量名
   父类的成员变量：    super.成员变量名

### 方法重载与方法重写

方法重载（Overload）：方法的名称一样，参数列表【不一样】。
1. 在同一个类中
2. 方法名相同，参数列表不同（顺序、个数、类型）
3. 返回值，访问修饰符任意
4. 与方法的参数名无关

方法重写（Override）：方法的名称一样，参数列表【也一样】。覆盖、覆写。
1. 有继承关系的子类
2. 方法名相同，参数列表相同（顺序、个数、类型）方法返回值相同
3. 与方法参数名无关
4. 重写后的访问修饰符只能比父类的权限大

方法覆盖重写的注意事项：

1. 必须保证父子类之间方法的名称相同，参数列表也相同。
   @Override：写在方法前面，用来检测是不是有效的正确覆盖重写。
2. 子类方法的返回值必须【小于等于】父类方法的返回值范围。返回值不能使用父类返回值的父类
3. 子类方法的权限必须【大于等于】父类方法的权限修饰符。

方法的覆盖重写特点：创建的是子类对象，则优先用子类方法。

在父子类的继承关系当中，创建子类对象，访问成员方法的规则：
    创建的对象是谁，就优先用谁，如果没有则向上找。

注意事项：
无论是成员方法还是成员变量，如果没有都是向上找父类，绝对不会向下找子类的。



### 权限修饰符

* public：任何地方均可访问
* protected：只能在同包中，跨包子类中访问
*    默认：只能在同包中使用
* private：这能在当前类中访问

## 多态

## 抽象类

* 抽象方法：就是加上abstract关键字，然后去掉大括号，直接分号结束。
* 抽象类：抽象方法所在的类，必须是抽象类才行。在class之前写上`abstract`即可。

如何使用抽象类和抽象方法：
1. 不能直接创建`new`抽象类对象。
2. 必须用一个子类来继承抽象父类。
3. 子类必须覆盖重写抽象父类当中所有的抽象方法。 
   覆盖重写（实现）：子类去掉抽象方法的`abstract`关键字，然后补上方法体大括号。
4. 创建子类对象进行使用。

## 异常
`java.lang.Throwable`类是Java、语言中所有错误或异常的超类

`Exception`：编译期异常，进行编译(写代码)java程序出现的问题
`RuntimeException`：运行期异常，java程序运行过程中出现的问题
异常就相当于程序得了一个小毛病，把异常处理了，程序就可以正常工作

`Error`：错误
  错误就相当于程序得了一个无法治愈的毛病，必须修改源代码，程序才能运行

`throw`关键字
作用：
  可以使用`throw`在指定的方法中抛出指定的异常
使用格式：
  `throw new xxxException("异常抛出的原因")`;

注意事项：
1. `throw`关键字必须写在方法的内部
2. `throw`关键字后`new`的对象必须是`Exception`或`Exception`子类对象
3. `throw`关键字抛出指定异常对象，必须处理异常对象
  * `throw`关键字后创建的是`RuntimeException`或者是`RuntimeException`的子类对象，可以不处理，默认交给JVM处理（打印异常，中断程序）
  * `throw`关键字后创建的是编译异常（写代码的时候报错），就必须处理这个异常，要吗throws，要吗try...catch

```java
public static int getElement(int[] arr, int index) {
    /*
    可以对传递过来的参数数组，进行合法性校验
    如果数组arr的值是null
    那么我们抛出空指针异常，告知方法的调用者“传递的数组值为null”
     */
    if(arr == null){
        throw new NullPointerException("传递的数组值为空");
    }
    /*
    可以对传递过来的参数index，进行合法性校验
    如果index的范围不在数组的索引范围内
    那么我们抛出数组索引越界异常，告知方法的调用者“传递索引超出了数组的使用范围”
     */
    if (index<0||index>arr.length-1){
        throw new IndexOutOfBoundsException("传递索引超出了数组的使用范围");
    }
    int ele = arr[index];
    return ele;
}
```

**如果是`编译器异常`必须在方法名的地方使用`throws`，如果是`运行期异常`则不一定用`throws`关键字**

### throws 

`throws`关键字：异常处理的第一种方式，交给别人处理
作用：
  当方法内部抛出异常对象时，那么我们就必须处理这个异常
  可以使用`throws`关键字处理异常对象，会吧异常对象声明抛出给方法的调用者处理（自己不处理，给别人处理），最终交给jvm处理-->中断处理

使用格式：在方法声明时使用
```java
修饰符 返回值类型 方法名(参数列表) throws aaaException, bbbException {
  throw new aaaException("产生原因");
  throw new bbbException("产生原因");
  ……
}
```

**注意:**
  1. `throws`关键字必须写在方法声明处
  2. `throws`关键字后边声明的异常必须是`Exception`或者是`Exception`的子类
  3. 方法内部出现多个异常对象，那么`throws`后边必须也声明多个异常
     如果抛出多个异常对象有子父类关系，那么直接声明父类异常即可
  4. 调用了一个声明抛出的异常，我们必须处理声明的异常
     要吗继续使用`throws`,交给方法的调用者处理，最终交给jvm
     要吗`try……catch`自己处理异常

```java
/*
    定义一个方法，对传递的文件路径进行合法性判断
    如果路径不是"c:\\a.txt"，那么抛出文件找不到异常对象，告知方法调用者
    注意：
        FileNotFoundException是编译异常，抛出就处理
        可以使用throws继续声明FileNotFoundException这个异常
 */

public static void readFile(String fileName) throws IOException {
    if (!fileName.equals("c:\\\\a.txt")) {
        throw new FileNotFoundException("传递的文件路径不是c:\\\\a.txt");
    }
    /*
    如果传递的路径不是.txt结尾
    那么我们就抛出IO异常对象
     */
    if (!fileName.endsWith(".txt")) {
        throw new IOException("文件后缀名不对");
    }
}
```


### try...catch

异常处理的第二种方式，自己处理异常

格式：
```java
try {
	可能产生异常的代码
}catch(定义一个异常的变量，怎么处理异常对象){
	异常的处理逻辑，异常之后，怎么处理异常对象
	一般会把异常的信息记录到一个日志中
}
……
catch(异常类名 变量名){

}
```

**注意：**
1. `try`中可能会抛出多个异常对象，那么就可以使用多个`catch`来处理这些异常对象
2. 如果`try`中产生了异常那么就会执行`catch`中异常处理逻辑，执行完毕后，继续执行`try……catch`之后的代码,如果`try`中没有产生异常，那么就不会执行`catch`中异常的处理逻辑，执行完`try`中的代码后，继续执行`try……catch`之后的代码

**一个try多个catch注意事项：**
catch里边定义的异常变量，如果有子父类关系，那么子类的异常变量必须写在上边，否则就会报错


```java
public static void main(String[] args) {
    try {
        readFile("c:\\\\a.tx");
    } catch (IOException e) {
        System.out.println("catch--传递的文件后缀不是.txt");

        /*
            Throwable类中，定义了三个处理异常的方法
            String getMessage() 返回此throwable的简短描述。
            String toString() 返回此可抛出的详细消息字符串。
            void printStackTrace() JVM打印异常对象默认使用该方法，打印信息最全面
        */

        System.out.println(e.getMessage());//文件后缀名不对
        //重写了toString
        System.out.println(e.toString());//java.io.IOException: 文件后缀名不对
        e.printStackTrace();//一般报错形式
    }
    System.out.println("后续代码");
}
public static void readFile(String fileName) throws IOException {

    /*
    如果传递的路径不是.txt结尾
    那么我们就抛出IO异常对象
     */
    if (!fileName.endsWith(".txt")) {
        throw new IOException("文件后缀名不对");
    }
}
```

### finally
格式：
```java
try {
	可能产生异常的代码
}catch(定义一个异常的变量，怎么处理异常对象){
	异常的处理逻辑，异常之后，怎么处理异常对象
	一般会把异常的信息记录到一个日志中
}
……
catch(异常类名 变量名){

}
finally{
	无论是否出现异常都会执行
}
```
**注意：**
  1. `finally`不能单独使用，必须和`try`一起使用
  2. `finally`一般用于资源释放（资源回收），无论是否出现异常，最后都要资源释放（IO）
  3. 避免`finally`中写`return`；

```java
public static void main(String[] args) {
    try {
        //会产生异常的代码
        readFile("c:\\\\a.txt");
    } catch (IOException e) {
        //异常的处理逻辑
        e.printStackTrace();
    } finally {
        //是否出现异常都会执行
        System.out.println("资源释放");
    }
}

public static void readFile(String fileName) throws IOException {

    /*
    如果传递的路径不是.txt结尾
    那么我们就抛出IO异常对象
     */
    if (!fileName.endsWith(".txt")) {
        throw new IOException("文件后缀名不对");
    }
}

```

### 子父类异常

子父类的异常
  1. 如果父类抛出了多个异常，子类重写父类方法时，抛出和父类相同的异常或者是父类异常的子类或者不抛出异常
  2. 父类方法没有抛出异常，子类重写父类该方法时也不可抛出异常。此时子类产生该异常，只能捕获处理，不能声明抛出
**注意：**

    父类异常什么样，子类异常就什么样

```java
public class Fu {
    public void show01() throws NullPointerException, ClassCastException {
    }

    public void show02() throws IndexOutOfBoundsException {
    }

    public void show03() throws IndexOutOfBoundsException {
    }

    public void show04() {
    }
}

class zi extends Fu {
    //子类重写父类方法时，抛出和父类相同的异常
    public void show01() throws NullPointerException, ClassCastException {
    }

    //子类重写父类方法时，抛出父类异常的子类
    public void show02() throws ArrayIndexOutOfBoundsException {
    }

    //子类重写父类方法时，不抛出异常
    public void show03() {
    }

    //父类方法没有抛出异常，子类重写父类该方法时也不可抛出异常
    //public void show04() throws Exception {}
    public void show04(){
        //throw new Exception("编译期异常");
        try {
            throw new Exception("编译期异常");
        }catch (Exception e){
            System.out.println(e);
        }
    }
}
```

### 自定义异常类

java提供的异常类，不够我们使用，需要自己定义一些异常类

格式：
```java
    public class XXXException extends Exception/RuntimeException
        添加一个空参构造方法
        添加一个带异常信息构造方法
```
**注意：**
  1. 自定义异常类一般都是以Exception结尾，说明是一个异常类
  2. 自定义异常类，必须继承Exception或者RuntimeException
     继承Exception：自定义的异常类是编译期异常，如果方法内部抛出了编译期异常，就必须处理这个异常，要吗throws，要吗try……catch
     继承RuntimeException：自定义的异常类是运行期异常，无需处理，交给虚拟机处理(中断处理)

```java
public class RegisterException extends Exception {
    //添加一个空参构造方法
    public RegisterException() {
    }

    //添加一个带异常信息构造方法
    public RegisterException(String message) {
        super(message);
    }
}
```

## API

### ArrayList

`ArrayList`长度可以发生变化
对于`ArrayList`有一个`<E>`代表泛型

泛型：装在集合中的所有元素都为同一类型

**注：**

1. 泛型只能为引用类型，不能为基本类型
2. 对于`ArrayList`，直接打印得到的不是地址值，而是内容如果为空，则打印的是[]
3. 对于于`ArrayList`集合，add添加动作一定成功，返回值可用可不用，对于其它集合，add方法不一定成功

### Arrays

```java
public static String toString(数组);将参数变为字符串（按照默认格式[元素1，元素2,……]）
public static void sort(数组);//按照默认升序对元素进行排序
```

**注：**
1. 如果是数字，sort自动按升序排列
2. 如果是字符串，sort默认按字母升序
3. 如果是自定义的类，那么这个自定义的类需要有Comparable或Comparator接口的支持

### Calendar

`java.util.Calender`类：日历类
`Calendar`类是一个抽象类，里面提供了许多操作日历字段的方法(YEAR、MONTH、DAY_MONTH、HOUR)
`Calendar`类无法直接创建对象使用，里面有一个静态方法`getInstance()`，该方法返回了`Calendar`类的子类对象
`static Calender getInstance()`使用默认时区和语言环境获得一个日历

`Calendar`类常用成员方法：
`public int get(int field)`：返回给定日历字段的值。
`public void set(int field, int value)`：将给定的日历字段设置为给定值。
`public abstract void add(int field, int amount)`：根据日历的规则，为给定的日历字段添加或减去指定的时间量。
`public Date getTime()`：返回一个表示此Calendar时间值（从历元到现在的毫秒偏移量）的Date对象。
成员方法参数：
`int field` ：日历类的字段，可以使用Calendar类的静态成员变量获取
`public static final int YEAR = 1;`	年
`public static final int MONTH = 2;`	月
`public static final int DATE = 5;`	月中的某一天
`public static final int DAY_OF_MONTH = 5;`月中的某一天
`public static final int HOUR = 10;` 		时
`public static final int MINUTE = 12;` 	分
`public static final int SECOND = 13;`	秒

```java
/*
    public Date getTime()：返回一个表示此Calendar时间值（从历元到现在的毫秒偏移量）的Date对象。
    吧日历对象转化为日期对象
     */
    private static void demo04() {
        Calendar c = Calendar.getInstance();

        Date time = c.getTime();
        System.out.println(time);
    }

    /*
    public abstract void add(int field, int amount)：根据日历的规则，为给定的日历字段添加或减去指定的时间量。
    把指定字段增加/减少指定的值
    参数：
        int field：传递指定的日历字段(YEAR,MONTH...)
        int amount ：增加/减少指定的值
                     正数：增加
                     负数：减少
     */
    private static void demo03() {
        //使用getInstance方法获取Calendar对象
        Calendar c = Calendar.getInstance();

        //增加两年
        c.add(Calendar.YEAR, 2);
        //月减少3
        c.add(Calendar.MONTH, -2);
        int year = c.get(Calendar.YEAR);
        System.out.println(year);

        int month = c.get(Calendar.MONTH);//西方的月0-11，东方1-12
        System.out.println(month);

        int day = c.get(Calendar.DAY_OF_MONTH);
        int date = c.get(Calendar.DATE);//和DAY_OF_MONTH一样
        System.out.println(day);
    }

	/*
    public void set(int field, int value)：将给定的日历字段设置为给定值。
    参数:
        int field :传递指定的字段(YEAR,MONTH...)
        int value :给指定字段设置的值
     */
    private static void demo02() {
        //使用getInstance方法获取Calendar对象
        Calendar c = Calendar.getInstance();

        //设置年为9999
        c.set(Calendar.YEAR, 9999);
        //设置月为9月,我们的10月
        c.set(Calendar.MONTH, 9);
        //设置日为9日
        c.set(Calendar.DAY_OF_MONTH, 9);

        //同时设置年月日，使用set的重载方法
        c.set(8888, 8, 8);

        int year = c.get(Calendar.YEAR);
        System.out.println(year);

        int month = c.get(Calendar.MONTH);//西方的月0-11，东方1-12
        System.out.println(month);

        int day = c.get(Calendar.DAY_OF_MONTH);
        int date = c.get(Calendar.DATE);//和DAY_OF_MONTH一样
        System.out.println(day);
    }

    /*
    public int get(int field)：返回给定日历字段的值。
    参数:传递指定的字段(YEAR,MONTH...)
    返回值：日历字段返回具体的值
     */
    private static void demo01() {
        //使用getInstance方法获取Calendar对象
        Calendar c = Calendar.getInstance();
        int year = c.get(Calendar.YEAR);
        System.out.println(year);

        int month = c.get(Calendar.MONTH);//西方的月0-11，东方1-12
        System.out.println(month);

        int day = c.get(Calendar.DAY_OF_MONTH);
        int date = c.get(Calendar.DATE);//和DAY_OF_MONTH一样
        System.out.println(day);
    }
```

### Collection

`java.util.Collection`
所有单列集合最顶层的接口，里边定义了所有单列集合共性的方法
任意的单列机和都可以使用`Collection`接口中的方法


共性的方法：
`public boolean add(E e)`：  把给定的对象添加到当前集合中 。
`public void clear()` :清空集合中所有的元素。
`public boolean remove(E e)`: 把给定的对象在当前集合中删除。
`public boolean contains(E e)`: 判断当前集合中是否包含给定的对象。
`public boolean isEmpty()`: 判断当前集合是否为空。
`public int size()`: 返回集合中元素的个数。
`public Object[] toArray()`: 把集合中的元素，存储到数组中。

### Collections

`java.utils.Collections`是集合工具类，用来对集合进行操作。部分方法如下：

`public static <T> boolean addAll(Collection<T> c, T... elements)  `:往集合中添加一些元素。
`public static void shuffle(List<?> list)` 打乱顺序:打乱集合顺序。

`public static <T> void sort(List<T> list)`:将集合中元素按照默认规则排序

**注意事项：**
`sort(List<T> list)`适用前提
被排序的集合里边储存的元素，必须实现`Comparable`，重写接口里的方法`comparableTo`定义排序规则

`Comparable`接口的排序规则：
自己(this)-参数：升序
参数-自己(this)：降序

`public static <T> void sort(List<T> list，Comparator<? super T> )`:将集合中元素按照指定规则排序。

Comparator和Comparable的区别
    Comparable：自己(this)和别人(参数)比较，自己需要实现Comparable接口，重写比较的规则`compareTo`方法
    Comparator：相当于找一个第三方裁判，比较两个元素

Comparator比较规则
    o1-o2 升序
    o2-o1 降序

### Iterator
`java.util.Iterator`接口：迭代器(对集合进行遍历)
有两个常用方法：
`boolean hasNext()`如果迭代具有更多元素，则返回 true 。判断集合还有没有下一个元素，如果有返回true，如果没有返回false
`E next()`返回迭代中的下一个元素。取出集合中的下一个元素

`Iterator`迭代器是一个接口，无法直接使用，需要实现类对象，获取实现类方法比较特殊
`Collection`接口中有一个方法`iterator()`，返回的就是`Iterator`实现类对象
`Iterator<E> iterator()` 返回此集合中的元素的迭代器。

迭代器的使用步骤（重点）：
1. 先使用集合中的方法`iterator()`获取迭代器实现类对象，用`Iterator`接口接受（多态）
2. 用`Iterator`接口中的方法`hasNext()`方法判断还有没有下一个元素
3. 用`Iterato`r接口中的方法`next()`方法切除集合中的下一个元素

```java
public static void main(String[] args) {
    //创建对象
    Collection<String> coll = new ArrayList<>();
    //添加元素
    coll.add("姚明");
    coll.add("科比");
    coll.add("麦迪");
    coll.add("詹姆斯");
    coll.add("艾弗森");
    /*
    1.先使用集合中的方法iterator()获取迭代器实现类对象，用Iterator接口接受（多态）
    注意事项：
       Iterator<E>接口也是有泛型的，迭代器泛型跟着集合走，集合是什么泛型，迭代器就是什么泛型
     */
    Iterator<String> it = coll.iterator();
    //2.用Iterator接口中的方法hasNext()方法判断还有没有下一个元素
    boolean b = it.hasNext();
    System.out.println(b);
    //3.用Iterator接口中的方法next()方法切除集合中的下一个元素
    String s = it.next();
    System.out.println(s);

    //优化
    while(it.hasNext()){
        String s1 = it.next();
        System.out.println(s1);
    }
}
```

### Date

`java.util.Date`:表示日期和时间的类
类`Date`表示特定的瞬间，精确到毫秒
毫秒：千分之一秒，1000毫秒=1秒

毫秒值的作用：可以对时间和日期进行计算
将日期转换为毫秒进行计算，计算完毕，再把毫秒转换为日期

把日期转换为毫秒：
当前的日期：2088 - 01 - 01
时间原点(0毫秒)：1970 年 1 月 1 日 00:00:00（英国格林威治）
就是计算当前日期到时间原点一共经历了多少毫秒

**注意：**
中国是东八区，会把时间增加八个小时
1970 年 1 月 1 日 08:00:00

把毫秒换算为日期：1 天 = 24 * 60 * 60 = 86400 秒 = 86400000 毫秒

`System.currentTimeMillis()`获取当前时间
空参数构造方法`Date()` 获取当前系统的日期和时间
`Date（long date）`：传递毫秒值，把毫秒值转换为date日期
`long getTime()` 把日期转换为毫秒值

```java
/*
Date类的空参数构造方法
Date() 获取当前系统的日期和时间
*/
private static void demo01(){
    Date date = new Date();
    System.out.println(date);
}

/*
Date类的带参数构造方法
Date（long date）：传递毫秒值，把毫秒值转换为date日期
 */
private static void demo02(){
    Date date = new Date(0L);
    System.out.println(date);

    date = new Date(15362838263876L);
    System.out.println(date);
}

/*
long getTime() 把日期转换为毫秒值
 */
private static void demo03(){
    Date date = new Date();
    long time = date.getTime();
    System.out.println(time);
}
```

### DateFormat

`java.text.DateFormat`:是日期/时间格式化子类的抽象类
作用:
格式化（也就是日期 -> 文本）、解析（文本-> 日期）

成员方法:
`String format(Date date)`  按照指定的模式,把Date日期,格式化为符合模式的字符串
`Date parse(String source)`  把符合模式的字符串,解析为Date日期

`DateFormat`类是一个抽象类,无法直接创建对象使用,可以使用`DateFormat`类的子类

`java.text.SimpleDateFormat extends DateFormat`

构造方法:
`SimpleDateFormat(String pattern)`
用给定的模式和默认语言环境的日期格式符号构造` SimpleDateFormat`。
参数:
`String pattern`:传递指定的模式
模式:区分大小写的
y   年
M   月
d   日
H   时
m   分
s   秒
写对应的模式,会把模式替换为对应的日期和时间`"yyyy-MM-dd HH:mm:ss"`

**注意:**
模式中的字母不能更改,连接模式的符号可以改变
`"yyyy年MM月dd日 HH时mm分ss秒"`

```java
/*
使用DateFormat类中的方法format，把日期格式化为文本
使用步骤：
    1.创建SimpleDateFormat对象，构造方法中传递指定的模式
    2.调用SimpleDateFormat对象中的方法format，按照构造方法中指定的模式，吧Date日期格式化为符合模式的字符串(文本)
    */
private static void demo01(){

    SimpleDateFormat sdf = new SimpleDateFormat("yyyy年MM月dd日 HH时mm分ss秒");
    Date date = new Date();
    String format = sdf.format(date);
    System.out.println(date);//Mon Jan 20 19:42:56 CST 2020
    System.out.println(format);//2020年01月20日 19时42分56秒
}

/*
使用DateFormat类中的方法parse,把文本解析为日期
     使用步骤:
        1.创建SimpleDateFormat对象,构造方法中传递指定的模式
        2.调用SimpleDateFormat对象中的方法parse,把符合构造方法中模式的字符串,解析为Date日期
        注意:
            public Date parse(String source) throws ParseException
            parse方法声明了一个异常叫ParseException
            如果字符串和构造方法的模式不一样,那么程序就会抛出此异常
            调用一个抛出了异常的方法,就必须的处理这个异常,要么throws继续抛出这个异常,要么try catch自己处理
 */
private  static void demo02() throws ParseException {
    SimpleDateFormat sdf = new SimpleDateFormat("yyyy年MM月dd日 HH时mm分ss秒");
    //2.调用SimpleDateFormat对象中的方法parse,把符合构造方法中模式的字符串,解析为Date日期
    //Date parse(String source)  把符合模式的字符串,解析为Date日期
    Date date = sdf.parse("2088年08月08日 15时51分54秒");
    System.out.println(date);
}
```

### Object
`equals`：
1. 继承Object中的equals方法时，比较的是两个引用是否转向一同一个对象
2. 子类可以通过重写equals方法的形式，改变比较的内容

`toString`:
1. 输出对象名时，默认会直接调用类中的toString
2. 承Object中的toString方法时，输出对象的字符串表示形式：类型信息+@+地址信息

`getClass`:返回对象的哈希代码值

`hashCode`：获取当前类对象所属的类信息，返回Class对象

# FileAndIO

## File

文件和目录路径名的抽象表示。

java把电脑中的文件和文件夹（目录）封装为一个File类，可以使用File类可以对文件和文件夹进行操作

我们可以使用File类的方法
  创建一个文件/文件夹
  删除文件/文件夹
  获取文件/文件夹
  判断文件/文件夹是否存在
  遍历文件/文件夹
  获取文件大小

File类与系统无关，任何操作系统都可以使用这个类

* file：文件
* directory：文件夹/目录
* path：路径

### 符号

`File`静态成员变量
1. `static String pathSeparator`  与系统相关的路径分隔符字符，为方便起见，表示为字符串。
2. `static char pathSeparatorChar` 与系统相关的路径分隔符。
3. `static String separator` 与系统相关的默认名称 - 分隔符字符，以方便的方式表示为字符串。
4. `static char separatorChar` 与系统相关的默认名称分隔符。

操作路径：路径不能写死了
C:\.... windows
C:/.... linux

"C:"+File.separator+"...."

### 路径

* 绝对路径：是一个完整的路径
  - 以盘符开始的路径

* 相对路径：是一个简化的路径
  - 相对指的是相对于当前项目的根目录
  - 如果使用当前项目的根目录，可以简化书写

* 注意：
  1. 路径不区分大小写
  2. 路径中的文件名称分隔符使用转义字符：//

### 构造方法

* `File(String pathname)` 通过将给定的路径名字符串转换为抽象路径名来创建新的 File实例。

  参数：
  `String pathname`：字符串的路径名称
  1. 路径可以以文件结尾，也可以用文件夹结尾
  2. 路径可以是想相对路径，也可以是绝对路径
  3. 路径可以存在，也可以不存在
  4. 创建File对象，只是把字符串路径封装为File对象，不考虑路径真假情况


* `File(String parent, String child)` 从父路径名字符串和子路径名字符串创建新的 File实例。

  参数：吧路径分为了两部分
  `String parent`：父路径
  `String child`：子路径

  好处：父路径和子路径可以单独书写，使用起来更灵活，父路径，子路径都可以变化


* `File(File parent, String child)` 从父抽象路径名和子路径名字符串创建新的 File实例。

  参数：吧路径分为了两部分
  `String parent`：父路径
  `String child`：子路径

  好处：
  1. 父路径和子路径可以单独书写，使用起来更灵活，父路径，子路径都可以变化
  2. 父路径是File类型，可以使用file的方法对路径进行操作，在使用路径创建对象

### 方法

* `public String getAbsolutePath()` ：返回此File的绝对路径名字符串。

  获取构造方法中传递的路径
  无论是相对还是绝对，getAbsolutePath返回的都是绝对路径

* `public String getPath()` ：将此File转换为路径名字符串。

  获取构造方法中传递的路径
  toString调用getPath

* `public String getName()` ：返回由此File表示的文件或目录的名称。

  获取构造方法的结尾部分(文件/文件夹)

* `public long length()` ：返回由此File表示的文件的长度。

  获取的是构造方法的文件的大小，以字节为单位
  
  注意：
  1. 文件夹没有大小概念，不能获取文件夹大小
  2. 如果构造方法中给出的路径不存在，那么length返回0

* `boolean exists()` 测试此抽象路径名表示的文件或目录是否存在。

  用于判断构造方法中的路径是否存在
  存在：true
  
* `boolean isDirectory()` 测试此抽象路径名表示的文件是否为目录。

  用于判断构造方法中给定的路径是否以文件夹结尾
  是：true

* `boolean isFile()` 测试此抽象路径名表示的文件是否为普通文件。

  用于判断构造方法中给定的路径是否以文件结尾
  是：true

  注意：
  1. 硬盘只有文件和文件夹，两个方法互斥
  2. 方法使用前提，路径必须存在，否则都返回false

* `boolean createNewFile()` 当且仅当具有该名称的文件尚不存在时，原子地创建一个由该抽象路径名命名的新的空文件。

  创建文件的路径和名称在构造方法中给出（构造方法的参数）
  
  返回值：
  true：文件不存在，创建文件，返回true
  false：文件存在，不创建文件，返回false

  注意：
  1. 只创建文件，不创建文件夹
  2. 创建文件的路径必须存在，否则抛出异常
  3. 调用方法必须处理异常

* `boolean mkdir()` :创建单级文件夹
* `boolean mkdirs()` :既可以创建单级文件夹，又可以创建多级文件夹

  创建文件夹的路径和名称在构造方法中给出（构造方法的参数）
  
  返回值：
  true：文件夹不存在，创建文件夹，返回true
  false：文件存在夹，不创建文件夹，返回false路径不存在也返回false

  注意：
  1. 只创建文件夹，不创建文件
  2. 创建文件的路径必须存在，否则抛出异常

* `boolean delete()` 删除由此抽象路径名表示的文件或目录。

  可以删除构造方法的文件或文件夹
  
  返回值：
  true：文件\文件夹删除成功
  false：文件夹中有内容，不会删除返回false；构造方法中路径不存在返回false

  注意：
  delete是直接在硬盘中删除文件/文件夹，不走回收站

* `String[] list()`返回一个字符串数组，命名由此抽象路径名表示的目录中的文件和目录。

  遍历构造方法中给出的目录，会获取目录中所有文件/文件夹的名称，把获取到的多个名称储存到一个String类型的数组中
  
* `File[] listFiles()`返回一个抽象路径名数组，表示由该抽象路径名表示的目录中的文件。

  遍历构造方法中给出的目录，会获取目录中所有文件/文件夹，把获取到的文件/文件夹封装为file对象
  
  注意：
  1. list方法和listFile方法遍历的是构造方法中给出的目录
  2. 如果构造方法中给出的目录的路径不存在，会抛出空指针异常
  3. 如果构造方法中给出的路径不是一个目录，也会抛出空指针异常

### 递归打印

```java
/*
定义一个方法。参数传递File类型的目录
 */
public static void getAllFile(File dir){
    System.out.println(dir);
    File[] files = dir.listFiles();
    for (File f : files) {
        if (f.isDirectory())
            getAllFile(f);
        else System.out.println(f);
    }
}
```

### 寻找指定文件后缀

```java
public class Demo08 {
    static String END_NAME;

    public static void main(String[] args) {
        System.out.println("请输入后缀名：");
        END_NAME = "." + new Scanner(System.in).next().toLowerCase();
        getAllFile(new File("C:\\Users\\Administration\\Desktop\\程序"));
    }

    public static void getAllFile(File dir) {
        File[] files = dir.listFiles();
        for (File f : files) {
            if (f.isDirectory())
                getAllFile(f);
            else if (f.getName().toLowerCase().endsWith(END_NAME))
                System.out.println(f);
        }
    }
}
```

## 过滤器

* `File[] listFiles(FileFilter filter)` 返回一个抽象路径名数组，表示由此抽象路径名表示的满足指定过滤器的目录中的文件和目录。

  - `FileFilter`接口：用于抽象路径名（File）的过滤器

    抽象方法：`boolean accept(File pathname)` 测试指定的抽象路径名是否应包含在路径名列表中。

    参数：`File pathname`：使用listFiles方法遍历目录，得到的每一个文件对象
    
```java
/*
创建FileFilter的实现类，重写过滤方法accept，定义过滤规则
 */
public class FileFilterImpl implements FileFilter {
    @Override
    public boolean accept(File pathname) {
        //过滤规则
        //如果是文件夹，返回true，结尾正确返回true
        if (pathname.isDirectory()) return true;
        return pathname.getName().toLowerCase().endsWith(".java");
    }
}
```

* `File[] listFiles(FilenameFilter filter)` 返回一个抽象路径名数组，表示由此抽象路径名表示的满足指定过滤器的目录中的文件和目录。

  - `FilenameFilter`接口：实现此接口的类的实例用于过滤文件名。

    抽象方法：`boolean accept(File dir, String name)` 测试指定文件是否应包含在文件列表中。
  
    参数：
    `File dir`：构造方法中传递的目录
    `String name`：listFiles遍历目录获取的没有个文件或文件夹的名称
  
* 注意事项：两个过滤器接口都没有实现类，需要自己写实现类，重写过滤的方法，在方法中自己定义过滤规则

```java
public static void main(String[] args) {
    getAllFile(new File("src"));
}

/*
    定义一个方法。参数传递File类型的目录
 */

public static void getAllFile(File dir) {
    System.out.println(dir);
    File[] files = dir.listFiles(new FilenameFilter() {
        @Override
        public boolean accept(File dir, String name) {
            return new File(dir, name).isDirectory() || name.toLowerCase().endsWith(".java");
        }
    });
    for (File f : files) {
        if (f.isDirectory())
            getAllFile(f);
        else System.out.println(f);
    }
}
```

## IO字节流

### OutputStream

`java.io.OutputStream`:此抽象类是表示输出字节流的所有类的超类

定义了一些共享的成员方法：

  * `void close()` 关闭此输出流并释放与此流相关联的任何系统资源。
  * `void flush()` 刷新此输出流并强制任何缓冲的输出字节被写出。
  * `void write(byte[] b)` 将 b.length字节从指定的字节数组写入此输出流。
  * `void write(byte[] b, int off, int len) `从指定的字节数组写入 len个字节，从偏移 off开始输出到此输出流。
  * `abstract void write(int b)` 将指定的字节写入此输出流。

### FileOutputStream

`java.io.FileOutputStream extends OutputStream`
`FileOutputStream`:文件字节输出流

作用：把内存中的数据写入到文件中

构造方法：

  * `FileOutputStream(File file)` 创建文件输出流以写入由指定的 File对象表示的文件。
  * `FileOutputStream(String name)` 创建文件输出流以指定的名称写入文件。

参数：写入数据的目的地

构造方法作用：

  1. 创建一个FileOutputStream对象
  2. 会根据构造方法中传递的文件/文件路径，创建一个新的文件
  3. 会吧FileOutputStream对象指向创建好的文件

写入内存的原理（内存->硬盘）
  java程序-->jvm-->os(操作系统)-->os调用写数据的方法-->把数据写入到文件中

字节输出流的使用步骤：

  1. 创建FileOutputStream对象，构造方法中传递目的地
  2. 调用FileOutputStream中的方法write，吧数据写入到文件中
  3. 释放资源(流使用会占用一定的内存，使用完毕后要把内存清空，提高程序效率)


一次写多个字节的方法：

  *  `void write(byte[] b)` 将 b.length字节从指定的字节数组写入此输出流。
  *  `void write(byte[] b, int off, int len)` 从指定的字节数组写入 len个字节，从偏移 off开始输出到此输出流。

```java
public static void main(String[] args) throws IOException {
    FileOutputStream fos = new FileOutputStream(new File("io流文件测试\\b.txt"));
    /*
    void write(byte[] b) 将 b.length字节从指定的字节数组写入此输出流。
    一次写多个字符：
        如果写的是整数(0~127)，显示时查ASCII表
        如果写的第一个字节是负数，那么第一个字节会和第二个字节，两个字节组成一个中文显示，查询系统默认的码表(GBK)
     */
    byte[] bytes = {65, 66, 67, 68, 69};//ABCDE
    //byte[] bytes = {-65, -66, -67, 68, 69};//烤紻E
    fos.write(bytes);

    /*
    void write(byte[] b, int off, int len)
        从指定字节数组写入len字节，从偏移 off开始输出到此输出流。
     */
    fos.write(bytes,1,2);//BC

    /*
    写入字符的方法：可以使用String类中的方法，吧字符串转换为字节数组
     */
    fos.write("写入字符串".getBytes());
    fos.close();
}
```

追加写/续写：

  * `FileOutputStream(File file, boolean append)` 创建文件输出流以写入由指定的 File对象表示的文件。
  * `FileOutputStream(String name, boolean append)` 创建文件输出流以指定的名称写入文件。

参数：

  1. 路径
  2. 追加写开关，

    true: 创建对象不会覆盖源文件，继续在文件末尾写
    false：创建新文件覆盖源文件

写换行：写换行字母

```java
public static void main(String[] args) throws IOException {
    FileOutputStream fos = new FileOutputStream("io流文件测试\\b.txt",true);
    for (int i = 0; i < 10; i++) {
        fos.write("你好\n".getBytes());
    }
    fos.close();
} 
```

### InputStream

`java.io.InputStream`:字节输入流，次抽象类是表示字节输入流所有类的超类

定义了所有子类共性的方法

  * `int read(byte[] b)` 从输入流读取一些字节数，并将它们存储到缓冲区 b 。
  * `int read(byte[] b, int off, int len)` 从输入流读取最多 len字节的数据到一个字节数组。
  * `void close()` 关闭此输入流并释放与流相关联的任何系统资源。

### FileInputStream

`java.io.FileInputStream extends InputStream`
`FileInputStream`：文件字节输入流

作用：把硬盘文件的数据读取到内存中

构造方法：

  * `FileInputStream(File file)`
  * `FileInputStream(String name)`
    参数：读取文件的数据源

构造方法作用：

  1. 创建对应
  2. 将对象指向要读取的对象

读取数据的原理：
  java程序-->jvm-->os-->os读取数据的方法-->读取文件
    
字节输入流的使用步骤：

  1. 创建对象，构造方法中绑定读取的数据源
  2. 使用对象的方法read，读取文件
  3. 释放资源

```java
public static void main(String[] args) throws IOException {
    FileInputStream fis = new FileInputStream("io流文件测试\\a.txt");
    //int read()：读取文件中的一个字节并返回，读取到文件的末尾返回-1
    int len = 0;//记录已读取的字节
    while ((len = fis.read()) != -1)
        System.out.println((char)len);
    fis.close();
}
```

* `int read(byte[] b)` 从输入流读取最多 len字节的数据到一个字节数组。

参数：

  * `b`：起缓冲作用，储存读取的结果

  * `返回值`：读取有效的字节个数

```java
public static void main(String[] args) throws IOException {
    FileInputStream fis = new FileInputStream("io流文件测试\\b.txt");
    byte[] bytes = new byte[1024];
    int len;
    while ((len = fis.read(bytes))!=-1){
        System.out.println(new String(bytes,0,len));
    }
}
```

### 文件复制

```java
public static void main(String[] args) throws IOException {
        FileInputStream fis = new FileInputStream("io流文件测试\\b.txt");

        FileOutputStream fos = new FileOutputStream("io流文件测试\\copyA.txt");;

        //读取多个字节，加快读写效率
        int len;
        byte[] bytes = new byte[1024];
        while ((len = fis.read(bytes))!=-1){
            fos.write(bytes,0,len);
        }
        fis.close();
        fos.close();
}
```

## IO字符流

### Reader

`java.io.Reader`:字符输入流，是字符输入流的最顶层父类，定义了一些共性的成员方法，是一个抽象类

共性的成员方法:

  * `int read()` 读一个字符并返回
  * `int read(char[] cbuf)` 一次读取多个数组将字符读入数组。
  * `abstract void close()` 关闭流并释放与之相关联的任何系统资源。

### FileReader

`java.io.FileReader extends InputStreamReader extends Reader`
`FileReader`：文件字符输入流

作用：把硬盘文件弘德属于以字符的方式读取到内存中

构造方法：

  * `FileReader(File file)` 创建一个新的 FileReader ，给出 File读取。
  * `FileReader(String fileName)` 创建一个新的 FileReader ，给定要读取的文件的名称。

参数：读取文件的数据源

FileReader构造方法的作用：

  1. 创建一个FileReader对象
  2. 对象指向要读取的文件

字符输入流的使用步骤：

  1. 创建对象，绑定要读取的数据源
  2. 使用FileReader对象中的方法read读取文件
  3. 释放资源

```java
public static void main(String[] args) throws IOException {
    FileReader fr = new FileReader("io流文件测试\\b.txt");

    /*
    int len;
    while ((len = fr.read()) != -1) {
        System.out.print((char) len);
    }
    */

    //数组读取
    int len;
    char[] chars = new char[1024];
    while ((len = fr.read(chars)) != -1) {
        System.out.println(new String(chars,0,len));
    }
    fr.close();
}
```

### Writer

`java.io.Writer` ：字符输出流，是所有字符输出流的最顶层父类，是一个抽象类

共性的成员方法：

  * `abstract void close()` 关闭流，先刷新。
  * `abstract void flush()` 刷新流。
  * `void write(char[] cbuf)` 写入一个字符数组。
  * `abstract void write(char[] cbuf, int off, int len)` 写入字符数组的一部分。
  * `void write(int c)` 写一个字符
  * `void write(String str)` 写一个字符串
  * `void write(String str, int off, int len)` 写一个字符串的一部分。

### FileWriter

`java.io.FileWriter extends OutputStreamWriter extends Writer`
`FileWriter`：文件字符输出流

作用：把内存中的字符数据写入到文件中

构造方法：

  * `FileWriter(File file)` 给一个File对象构造一个FileWriter对象。 。
  * `FileWriter(String fileName)` 构造一个给定文件名的FileWriter对象。

参数：写入数据的目的地

构造方法的作用：

  1. 创建对象
  2. 根据构造方法传递文件\文件夹的路径，创建文件
  3. 对象指向文件

使用步骤：

  1. 创建对象，构造方法中绑定目的地
  2. 使用write，吧数据写入到内存缓冲区中（字符转换为字节）；
  3. 使用flush，把内存缓冲区的数据，刷新到文件中
  4. 释放资源（先把内存把内存缓冲区的数据，刷新到文件中，然后释放）

```java
public static void main(String[] args) throws IOException {
    FileWriter fw = new FileWriter("io流文件测试\\d.txt");

    fw.write(97);

    fw.flush();//可以不写

    fw.close();
}
```

`flush`和`close`的方法

* `flush`：刷新缓冲区，流对象可以继续使用
* `close`：先刷新缓冲区，然后通知系统释放资源，流对象不可使用

字符输入流写数据的其他方法

  * `void write(char[] cbuf)` 写入一个字符数组。
  * `abstract void write(char[] cbuf, int off, int len)` 写入字符数组的一部分。
  * `void write(String str)` 写一个字符串
  * `void write(String str, int off, int len) `写一个字符串的一部分。

```java
public static void main(String[] args) throws IOException {
    FileWriter fw = new FileWriter("io流文件测试\\g.txt");
    char[] cs = {'a', 'b', 'c', 'd', 'e'};
    //void write(char[] cbuf) 写入一个字符数组。
    fw.write(cs);
    //abstract void write(char[] cbuf, int off, int len) 写入字符数组的一部分。
    fw.write(cs, 1, 3);
    //void write(String str) 写一个字符串
    fw.write("都需要");
    //void write(String str, int off, int len) 写一个字符串的一部分。
    fw.write("都需要", 1, 2);
    fw.close();
}
```

* 续写和换行

* 续写，追加写

  - FileWriter(File file, boolean append)
  - FileWriter(String fileName, boolean append)

参数：

  * `String fileName/File file`：写入数据目的地
  * `boolean append`：续写开关

换行：

  * windows：`\r\n`
  * linux：`/n`
  * mac：`/r`

```java
public static void main(String[] args) throws IOException {
    FileWriter fw = new FileWriter("io流文件测试\\f.txt", true);
    for (int i = 0; i < 20; i++) {
        fw.write("HelloWorld" + i + "\r\n");
    }
    fw.close();
}
```

## 流的异常处理

* `try catch finally`

```java
public static void main(String[] args) {
    FileWriter fw = null;
    try {
        fw = new FileWriter("io流文件测试\\f.txt", true);
        for (int i = 0; i < 20; i++) {
            fw.write("HelloWorld" + i + "\r\n");
        }
        fw.close();
    } catch (IOException e) {
        System.out.println(e);
    } finally {
        if (fw != null) {
            try {
                fw.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}
```

* JDK7

JDK7新特性
在try后边可以加一个括号，括号里可以创建流对象
流的作用域为try
try中代码执行完毕，自动释放流对象，不用finally

```java
public static void main(String[] args) {
    try (FileInputStream fis = new FileInputStream("io流文件测试\\b.txt");
         FileOutputStream fos = new FileOutputStream("io流文件测试\\copyA.txt")) {
        int len = 0;
        byte[] bytes = new byte[1024];
        while ((len = fis.read(bytes)) != -1) {
            fos.write(bytes, 0, len);
        }
    } catch (IOException e) {
        System.out.println(e);
    }
}
```

* JDK9

JDK9新特性
try前边定义流对象
在try后边括号中直接引入流对象的名称
在try代码块执行完毕，流对象也可以释放
但是流的声明还是需要throws

```java
public static void main(String[] args) throws FileNotFoundException {
    FileInputStream fis = new FileInputStream("io流文件测试\\b.txt");
    FileOutputStream fos = new FileOutputStream("io流文件测试\\copyA.txt");
    try (fis; fos) {
        int len = 0;
        byte[] bytes = new byte[1024];
        while ((len = fis.read(bytes)) != -1) {
            fos.write(bytes, 0, len);
        }
    } catch (IOException e) {
        System.out.println(e);
    }
}
```

## Properties

`java.util.Properties 集合 extend HashTable<k,v> implements Map<k,v>`

`Properties`类表示一组持久的属性。` Properties`可以保存到流中或从流中加载。

Properties集合是一个唯一和IO流相结合的集合

  * 可以使用properties集合中的方法store，把集合中的临时数据，持久化写入到硬盘中存储
  * 可以使用properties集合中的方法load，吧硬盘中的文件（键值对），读取到集合中使用

属性列表中的每个键及其对应的值都是一个字符串。
properties集合是一个双列集合，key和value默认都是字符串

  * `Object setProperty(String key, String value)` 调用 Hashtable方法 put 。
  * `String getProperty(String key)` 通过key找value，此方法相当于Map中的get方法
  * `Set<String> stringPropertyNames()` 返回此属性列表中的一组键，其中键及其对应的值为字符串，此方法相当于Map中的keySet方法

```java
private static void show01() {
    //创建对象
    Properties prop = new Properties();
    //使用setProperty往集合中天添加元素
    prop.setProperty("小明", "168");
    prop.setProperty("小刚", "165");
    prop.setProperty("小吕", "178");

    //使用stringPropertyNames把properties集合中的key取出，储存到一个set集合
    Set<String> set = prop.stringPropertyNames();

    //遍历,取出每一个key
    for (String key : set) {
        //使用getProperty方法使用key获取value
        String value = prop.getProperty(key);
        System.out.println(key + "=" + value);
    }
}
```

可以使用properties集合中的方法store，把集合中的临时数据，持久化写入到硬盘中存储

  * `void store(OutputStream out, String comments)`
  * `void store(Writer writer, String comments)`
参数：

  * `OutputStream out`：字节输出流，不能写中文
  * `Writer writer`：字符输出流，可以写中文
  * `String comments`：注释，用来解释说明保存文件是做什么用的,不能使用中文，会产生乱码，默认使用Unicode编码,一般用""

使用步骤：

  1. 创建对象，添加数据
  2. 创建字节输出流/字符输出流对象，构造方法中绑定要输出的目的地
  3. 使用properties集合中的方法store，把把集合中的临时数据，持久化写入到硬盘中存储
  4. 释放资源

```java
private static void show02() throws IOException {
    //1.创建对象，添加数据
    Properties prop = new Properties();
    prop.setProperty("小明", "168");
    prop.setProperty("小刚", "165");
    prop.setProperty("小吕", "178");

    //2.创建字节输出流/字符输出流对象，构造方法中绑定要输出的目的地
    //FileWriter fw = new FileWriter("io流文件测试\\prop.txt");

    //3.使用properties集合中的方法store，把把集合中的临时数据，持久化写入到硬盘中存储
    //prop.store(fw,"save data");

    //4.释放资源
    //fw.close();

    prop.store(new FileOutputStream("io流文件测试\\prop.txt"), "");
}
```

可以使用properties集合中的方法load，把硬盘中的文件（键值对），读取到集合中使用

  * `void load(InputStream inStream)`
  * `void load(Reader reader)`

参数：

  * InputStream inStream：字节输入流，不能读取含有中文的键值对
  * Reader reader：字符输入流，可以读取含有中文的键值对

使用步骤：

  1. 创建对象
  2. load方法读取保存键值对的文件
  3. 遍历集合

注意：

  1. 储存键值对的文件中，key与默认值的连接符号可以使用=，空格（其他符号）
  2. 储存键值对的文件中，可以使用#注释，被注释的不会读取
  3. 储存键值对的文件中，key与默认值的都是字符串，不用加引号

```java
private static void show03() throws IOException {
    //1.创建对象
    Properties prop = new Properties();

    //2.load方法读取保存键值对的文件
    prop.load(new FileReader("io流文件测试\\prop.txt"));

    //3.遍历集合
    Set<String> set = prop.stringPropertyNames();
    for (String key : set) {
        String value = prop.getProperty(key);
        System.out.println(key + "=" + value);
    }
}
```

## 缓冲流

### BufferedOutputStream

`java.io.BufferedOutputStream extend OutputStream`
`BufferedOutputStream`:字节缓冲输出流

继承父类的共性成员方法

  * `void close()` 关闭此输出流并释放与此流相关联的任何系统资源。
  * `void flush()` 刷新此输出流并强制任何缓冲的输出字节被写出。
  * `void write(byte[] b)` 将 b.length字节从指定的字节数组写入此输出流。
  * `void write(byte[] b, int off, int len)` 从指定的字节数组写入 len个字节，从偏移 off开始输出到此输出流。
  * `abstract void write(int b)` 将指定的字节写入此输出流。

构造方法：

  * `BufferedOutputStream(OutputStream out)` 创建一个新的缓冲输出流，以将数据写入指定的底层输出流。
  * `BufferedOutputStream(OutputStream out, int size)` 创建一个新的缓冲输出流，以便以指定的缓冲区大小将数据写入指定的底层输出流。

参数：

  * OutputStream out：字节输出流
    我们可以传递FileOutputStream，缓冲流会给FileOutputStream增加一个缓冲区，提高FileOutputStream的写入效率
  * int size ：指定内部缓冲区的大小，不指定默认

使用步骤
  1. 创建一个FileOutputStream对象，构造方法中传递要输出的目的地
  2. 创建BufferedOutputStream对象，构造方法中传递FileOutputStream对象，提高FileOutputStream的使用效率
  3. 使用BufferedOutputStream对象的write方法，把数据写到内部的缓冲区中
  4. 使用BufferedOutputStream对象的flush方法，把内部缓冲区的数据刷新到文件中
  5. 释放资源（会先调用flush方法刷新数据）

```java
public class Demo01 {
    public static void main(String[] args) throws IOException {
        //1. 创建一个FileOutputStream对象，构造方法中传递要输出的目的地
        FileOutputStream fos = new FileOutputStream("io流文件测试\\a1.txt");
        //2. 创建BufferedOutputStream对象，构造方法中传递FileOutputStream对象，提高FileOutputStream的使用效率
        BufferedOutputStream bos = new BufferedOutputStream(fos);
        //3. 使用BufferedOutputStream对象的write方法，把数据写到内部的缓冲区中
        bos.write("写数据".getBytes());
        //4. 使用BufferedOutputStream对象的flush方法，把内部缓冲区的数据刷新到文件中
        bos.flush();
        //5. 释放资源（会先调用flush方法刷新数据）
        bos.close();
    }
}
```

### BufferedInputStream

`java.io.BufferedInputStream extend InputStream`
`BufferedInputStream`字节缓冲输入流

继承父类的成员方法

  * `int read(byte[] b)` 从输入流读取一些字节数，并将它们存储到缓冲区 b 。
  * `int read(byte[] b, int off, int len)`从输入流读取最多 len字节的数据到一个字节数组。
  * `void close()` 关闭此输入流并释放与流相关联的任何系统资源。

构造方法

  * `BufferedInputStream(InputStream in)` 创建一个 BufferedInputStream并保存其参数，输入流 in ，供以后使用。
  * `BufferedInputStream(InputStream in, int size)` 创建 BufferedInputStream具有指定缓冲区大小，并保存其参数，输入流 in ，供以后使用。

参数：

  * `InputStream in`：字节输入流
    我们可以传递FileInputStream，缓冲流会给FileInputStream增加一个缓冲区，提高FileInputStream的读取效率
  * `int size` ：指定内部缓冲区的大小，不指定默认

使用步骤

  * 1. 创建FileInputStream，构造方法中绑定要读取的数据源
  * 2. 创建BufferedInputStream对象，构造方法中传递FileInputStream对象，提高FileInputStream的使用效率
  * 3. 使用BufferedInputStream对象的read方法，读取文件
  * 4. 释放资源

```java
public class Demo02BufferedInputStream {
    public static void main(String[] args) throws IOException {
        //1. 创建FileInputStream，构造方法中绑定要读取的数据源
        FileInputStream fis = new FileInputStream("io流文件测试\\a1.txt");
        //2. 创建BufferedInputStream对象，构造方法中传递FileInputStream对象，提高FileInputStream的使用效率
        BufferedInputStream bis = new BufferedInputStream(fis);
        //3. 使用BufferedInputStream对象的read方法，读取文件
        int len = 0;//记录每次读取到的有效字节个数
        byte[] bytes = new byte[1024];//存取每次读取的数据
        while ((len = bis.read(bytes)) != -1){
            System.out.println(new String(bytes,0,len));
        }
        //4. 释放资源
        bis.close();
    }
}
```

### BufferedWriter
`java.io.BufferedWriter extends Writer`
`BufferedWriter`:字符缓冲输入流

继承自父类的共性成员方法：

  * `abstract void close()` 关闭流，先刷新。
  * `abstract void flush()` 刷新流。
  * `void write(char[] cbuf)` 写入一个字符数组。
  * `abstract void write(char[] cbuf, int off, int len)` 写入字符数组的一部分。
  * `void write(int c)` 写一个字符
  * `void write(String str)` 写一个字符串
  * `void write(String str, int off, int len)` 写一个字符串的一部分。

构造方法：

  * `BufferedWriter(Writer out)` 创建使用默认大小的输出缓冲区的缓冲字符输出流。
  * `BufferedWriter(Writer out, int sz)` 创建一个新的缓冲字符输出流，使用给定大小的输出缓冲区。

参数：

  * `Writer out`:字符输出流
    我们可以传递FileWriter,缓冲流会给FileWriter增加一个缓冲区,提高FileWriter的写入效率
    
  * `int sz`:指定缓冲区的大小,不写默认大小

特有的成员方法:

  * `void newLine()` 写入一个行分隔符。会根据不同的操作系统,获取不同的行分隔符
    换行:换行符号
    windows:\r\n
    linux:/n
    mac:/r

使用步骤:

  1. 创建字符缓冲输出流对象,构造方法中传递字符输出流
  2. 调用字符缓冲输出流中的方法write,把数据写入到内存缓冲区中
  3. 调用字符缓冲输出流中的方法flush,把内存缓冲区中的数据,刷新到文件中
  4. 释放资源

```java
public class Demo03BufferedWriter {
    public static void main(String[] args) throws IOException {
        //System.out.println();
        //1.创建字符缓冲输出流对象,构造方法中传递字符输出流
        BufferedWriter bw = new BufferedWriter(new FileWriter("io流文件测试\\b1.txt"));
        //2.调用字符缓冲输出流中的方法write,把数据写入到内存缓冲区中
        for (int i = 0; i <10 ; i++) {
            bw.write("啦啦啦");
            //bw.write("\r\n");
            bw.newLine();
        }
        //3.调用字符缓冲输出流中的方法flush,把内存缓冲区中的数据,刷新到文件中
        bw.flush();
        //4.释放资源
        bw.close();
    }
}
```

### BufferedReader

`java.io.BufferedReader extends Reader`
`BufferedReader`:字符缓冲输入流

继承自父类的共性成员方法:

  * `int read()` 读取单个字符并返回。
  * `int read(char[] cbuf)`一次读取多个字符,将字符读入数组。
  * `void close()` 关闭该流并释放与之关联的所有资源。

构造方法:

  * `BufferedReader(Reader in)`  创建一个使用默认大小输入缓冲区的缓冲字符输入流。
  * `BufferedReader(Reader in, int sz)`    创建一个使用指定大小输入缓冲区的缓冲字符输入流。

参数:

  * `Reader in`:字符输入流
    我们可以传递FileReader,缓冲流会给FileReader增加一个缓冲区,提高FileReader的读取效率
            

特有的成员方法:

  * `String readLine()` 读取一个文本行。读取一行数据
    行的终止符号:通过下列字符之一即可认为某行已终止：换行 ('\n')、回车 ('\r') 或回车后直接跟着换行(\r\n)。
    返回值:包含该行内容的字符串，不包含任何行终止符，如果已到达流末尾，则返回 null

使用步骤:

  1. 创建字符缓冲输入流对象,构造方法中传递字符输入流
  2. 使用字符缓冲输入流对象中的方法read/readLine读取文本
  3. 释放资源

```java
public class Demo04BufferedReader {
    public static void main(String[] args) throws IOException {
        //1.创建字符缓冲输入流对象,构造方法中传递字符输入流
        BufferedReader br = new BufferedReader(new FileReader("io流文件测试\\b1.txt"));

        //2.使用字符缓冲输入流对象中的方法read/readLine读取文本
        /*String line = br.readLine();
        System.out.println(line);

        line = br.readLine();
        System.out.println(line);

        line = br.readLine();
        System.out.println(line);

        line = br.readLine();
        System.out.println(line);*/

        /*
            发下以上读取是一个重复的过程,所以可以使用循环优化
            不知道文件中有多少行数据,所以使用while循环
            while的结束条件,读取到null结束
         */
        String line;
        while ((line = br.readLine()) != null) {
            System.out.println(line);
        }

        //3.释放资源
        br.close();
    }
}
```

### 文本排序

练习:

  对文本的内容进行排序
  按照(1,2,3....)顺序排序

分析:

  1. 创建一个HashMap集合对象,可以:存储每行文本的序号(1,2,3,..);value:存储每行的文本
  2. 创建字符缓冲输入流对象,构造方法中绑定字符输入流
  3. 创建字符缓冲输出流对象,构造方法中绑定字符输出流
  4. 使用字符缓冲输入流中的方法readline,逐行读取文本
  5. 对读取到的文本进行切割,获取行中的序号和文本内容
  6. 把切割好的序号和文本的内容存储到HashMap集合中(key序号是有序的,会自动排序1,2,3,4..)
  7. 遍历HashMap集合,获取每一个键值对
  8. 把每一个键值对,拼接为一个文本行
  9. 把拼接好的文本,使用字符缓冲输出流中的方法write,写入到文件中
  10. 释放资源

```java
public class Demo05txtSort {
    public static void main(String[] args) throws IOException {
        //1.创建一个HashMap集合对象,可以:存储每行文本的序号(1,2,3,..);value:存储每行的文本
        HashMap<String,String> map = new HashMap<>();
        //2.创建字符缓冲输入流对象,构造方法中绑定字符输入流
        BufferedReader br = new BufferedReader(new FileReader("io流文件测试\\in.txt"));
        //3.创建字符缓冲输出流对象,构造方法中绑定字符输出流
        BufferedWriter bw = new BufferedWriter(new FileWriter("io流文件测试\\out.txt"));
        //4.使用字符缓冲输入流中的方法readline,逐行读取文本
        String line;
        while((line = br.readLine())!=null){
            //5.对读取到的文本进行切割,获取行中的序号和文本内容
            String[] arr = line.split("\\.");
            //6.把切割好的序号和文本的内容存储到HashMap集合中(key序号是有序的,会自动排序1,2,3,4..)
            map.put(arr[0],arr[1]);
        }

        //7.遍历HashMap集合,获取每一个键值对
        for(String key : map.keySet()){
            String value = map.get(key);
            //8.把每一个键值对,拼接为一个文本行
            line = key + "." + value;
            //9.把拼接好的文本,使用字符缓冲输出流中的方法write,写入到文件中
            bw.write(line);
            bw.newLine();//写换行
        }
        //10.释放资源
        bw.close();
        br.close();
    }
}
```

## 转换流

### OutputStreamWriter

`java.io.OutputStreamWriter extends Writer`
`OutputStreamWriter`: 是字符流通向字节流的桥梁：可使用指定的 charset 将要写入流中的字符编码成字节。(编码:把能看懂的变成看不懂)

继续自父类的共性成员方法:

  * `void write(int c)` 写入单个字符。
  * `void write(char[] cbuf)`写入字符数组。
  * `abstract  void write(char[] cbuf, int off, int len)`写入字符数组的某一部分,off数组的开始索引,len写的字符个数。
  * `void write(String str)`写入字符串。
  * `void write(String str, int off, int len)` 写入字符串的某一部分,off字符串的开始索引,len写的字符个数。
  * `void flush()`刷新该流的缓冲。
  * `void close()` 关闭此流，但要先刷新它。

构造方法:

  * `OutputStreamWriter(OutputStream out)`创建使用默认字符编码的 OutputStreamWriter。
  * `OutputStreamWriter(OutputStream out, String charsetName) `创建使用指定字符集的 OutputStreamWriter。

参数:

  * `OutputStream out`:字节输出流,可以用来写转换之后的字节到文件中
  * `String charsetName`:指定的编码表名称,不区分大小写,可以是utf-8/UTF-8,gbk/GBK,...不指定默认使用UTF-8

使用步骤:

  1. 创建OutputStreamWriter对象,构造方法中传递字节输出流和指定的编码表名称
  2. 使用OutputStreamWriter对象中的方法write,把字符转换为字节存储缓冲区中(编码)
  3. 使用OutputStreamWriter对象中的方法flush,把内存缓冲区中的字节刷新到文件中(使用字节流写字节的过程)
  4. 释放资源

```java
public class Demo01OutputStreamWriter {
    public static void main(String[] args) throws IOException {
        //write_utf_8();
        write_gbk();
    }

    /*
       使用转换流OutputStreamWriter写GBK格式的文件
    */
    private static void write_gbk() throws IOException {
        //1.创建OutputStreamWriter对象,构造方法中传递字节输出流和指定的编码表名称
        OutputStreamWriter osw = new OutputStreamWriter(new FileOutputStream("io流文件测试\\gbk.txt"),"GBK");
        //2.使用OutputStreamWriter对象中的方法write,把字符转换为字节存储缓冲区中(编码)
        osw.write("你好");
        //3.使用OutputStreamWriter对象中的方法flush,把内存缓冲区中的字节刷新到文件中(使用字节流写字节的过程)
        osw.flush();
        //4.释放资源
        osw.close();
    }

    /*
        使用转换流OutputStreamWriter写UTF-8格式的文件
     */
    private static void write_utf_8() throws IOException {
        //1.创建OutputStreamWriter对象,构造方法中传递字节输出流和指定的编码表名称
        //OutputStreamWriter osw = new OutputStreamWriter(new FileOutputStream("10_IO\\utf_8.txt"),"utf-8");
        OutputStreamWriter osw = new OutputStreamWriter(new FileOutputStream("io流文件测试\\utf_8.txt"));//不指定默认使用UTF-8
        //2.使用OutputStreamWriter对象中的方法write,把字符转换为字节存储缓冲区中(编码)
        osw.write("你好");
        //3.使用OutputStreamWriter对象中的方法flush,把内存缓冲区中的字节刷新到文件中(使用字节流写字节的过程)
        osw.flush();
        //4.释放资源
        osw.close();
    }
}
```

### InputStreamReader

`java.io.InputStreamReader extends Reader`
`InputStreamReader`:是字节流通向字符流的桥梁：它使用指定的 charset 读取字节并将其解码为字符。(解码:把看不懂的变成能看懂的)

继承自父类的共性成员方法:

  * `int read() `读取单个字符并返回。
  * `int read(char[] cbuf)`一次读取多个字符,将字符读入数组。
  * `void close() `关闭该流并释放与之关联的所有资源。

构造方法:

  * `InputStreamReader(InputStream in)` 创建一个使用默认字符集的 InputStreamReader。
  * `InputStreamReader(InputStream in, String charsetName)` 创建使用指定字符集的 InputStreamReader。

参数:

  * `InputStream in`:字节输入流,用来读取文件中保存的字节
  * `String charsetName`:指定的编码表名称,不区分大小写,可以是utf-8/UTF-8,gbk/GBK,...不指定默认使用UTF-8

使用步骤:

  1. 创建InputStreamReader对象,构造方法中传递字节输入流和指定的编码表名称
  2. 使用InputStreamReader对象中的方法read读取文件
  3. 释放资源

注意事项:

  构造方法中指定的编码表名称要和文件的编码相同,否则会发生乱码

```java
public class Demo02InputStreamReader {
    public static void main(String[] args) throws IOException {
        //read_utf_8();
        read_gbk();
    }


    /*
        使用InputStreamReader读取GBK格式的文件
     */
    private static void read_gbk() throws IOException {
        //1.创建InputStreamReader对象,构造方法中传递字节输入流和指定的编码表名称
        //InputStreamReader isr = new InputStreamReader(new FileInputStream("10_IO\\gbk.txt"),"UTF-8");//???
        InputStreamReader isr = new InputStreamReader(new FileInputStream("io流文件测试\\gbk.txt"),"GBK");//你好

        //2.使用InputStreamReader对象中的方法read读取文件
        int len = 0;
        while((len = isr.read())!=-1){
            System.out.println((char)len);
        }
        //3.释放资源
        isr.close();
    }

    /*
        使用InputStreamReader读取UTF-8格式的文件
     */
    private static void read_utf_8() throws IOException {
        //1.创建InputStreamReader对象,构造方法中传递字节输入流和指定的编码表名称
        //InputStreamReader isr = new InputStreamReader(new FileInputStream("10_IO\\utf_8.txt"),"UTF-8");
        InputStreamReader isr = new InputStreamReader(new FileInputStream("io流文件测试\\utf_8.txt"));//不指定默认使用UTF-8
        //2.使用InputStreamReader对象中的方法read读取文件
        int len = 0;
        while((len = isr.read())!=-1){
            System.out.println((char)len);
        }
        //3.释放资源
        isr.close();
    }
}
```

### 转换文件编码

练习：转换文件编码
  将GBK编码的文本文件，转换为UTF-8编码的文本文件。

分析:

  1. 创建InputStreamReader对象,构造方法中传递字节输入流和指定的编码表名称GBK
  2. 创建OutputStreamWriter对象,构造方法中传递字节输出流和指定的编码表名称UTF-8
  3. 使用InputStreamReader对象中的方法read读取文件
  4. 使用OutputStreamWriter对象中的方法write,把读取的数据写入到文件中
  5. 释放资源

```java
public class Demo03Test {
    public static void main(String[] args) throws IOException {
        //1.创建InputStreamReader对象,构造方法中传递字节输入流和指定的编码表名称GBK
        InputStreamReader isr = new InputStreamReader(new FileInputStream("10_IO\\我是GBK格式的文本.txt"),"GBK");
        //2.创建OutputStreamWriter对象,构造方法中传递字节输出流和指定的编码表名称UTF-8
        OutputStreamWriter osw = new OutputStreamWriter(new FileOutputStream("10_IO\\我是utf_8格式的文件.txt"),"UTF-8");
        //3.使用InputStreamReader对象中的方法read读取文件
        int len = 0;
        while((len = isr.read())!=-1){
            //4.使用OutputStreamWriter对象中的方法write,把读取的数据写入到文件中
            osw.write(len);
        }
        //5.释放资源
        osw.close();
        isr.close();
    }
}
```

## 序列化流

### 序列化`Serializable `

序列化和反序列化的时候,会抛出`NotSerializableException`没有序列化异常
类通过实现` java.io.Serializable` 接口以启用其序列化功能。未实现此接口的类将无法使其任何状态序列化或反序列化。

`Serializable`接口也叫标记型接口

  要进行序列化和反序列化的类必须实现Serializable接口,就会给类添加一个标记

  当我们进行序列化和反序列化的时候,就会检测类上是否有这个标记
  有:就可以序列化和反序列化
  没有:就会抛出 NotSerializableException异常

* `static`关键字:静态关键字

    静态优先于非静态加载到内存中(静态优先于对象进入到内存中)
    被static修饰的成员变量不能被序列化的,序列化的都是对象
    private static int age;
    oos.writeObject(new Person("小美女",18));
    Object o = ois.readObject();
    Person{name='小美女', age=0}

* `transient`关键字:瞬态关键字

    被transient修饰成员变量,不能被序列化
    private transient int age;
    oos.writeObject(new Person("小美女",18));
    Object o = ois.readObject();
    Person{name='小美女', age=0}

```java
public class Person implements Serializable{
    private static final long serialVersionUID = 1L;
    private String name;
    //private static int age;
    //private transient int age;
    public int age;

    public Person() {
    }

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    @Override
    public String toString() {
        return "Person{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }
}
```

### ObjectOutputStream

`java.io.ObjectOutputStream extends OutputStream`
`ObjectOutputStream`:对象的序列化流

作用:把对象以流的方式写入到文件中保存

构造方法:

  * `ObjectOutputStream(OutputStream out)` 创建写入指定 OutputStream 的 ObjectOutputStream。

参数:

  * `OutputStream out`:字节输出流

特有的成员方法:

  * `void writeObject(Object obj)` 将指定的对象写入 ObjectOutputStream。

使用步骤:

  1. 创建ObjectOutputStream对象,构造方法中传递字节输出流
  2. 使用ObjectOutputStream对象中的方法writeObject,把对象写入到文件中
  3. 释放资源

```java
public class Demo01ObjectOutputStream {
    public static void main(String[] args) throws IOException {
        //1.创建ObjectOutputStream对象,构造方法中传递字节输出流
        ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream("10_IO\\person.txt"));
        //2.使用ObjectOutputStream对象中的方法writeObject,把对象写入到文件中
        oos.writeObject(new Person("小美女",18));
        //3.释放资源
        oos.close();
    }
}
```

### ObjectInputStream

`java.io.ObjectInputStream extends InputStream`
`ObjectInputStream`:对象的反序列化流

作用:把文件中保存的对象,以流的方式读取出来使用

构造方法:

  * `ObjectInputStream(InputStream in)`创建从指定 InputStream 读取的 ObjectInputStream。

参数:

  * `InputStream in`:字节输入流

特有的成员方法:

  * `Object readObject() 从 ObjectInputStream` 读取对象。

使用步骤:
  1. 创建ObjectInputStream对象,构造方法中传递字节输入流
  2. 使用ObjectInputStream对象中的方法readObject读取保存对象的文件
  3. 释放资源
  4. 使用读取出来的对象(打印)

`readObject`方法声明抛出了`ClassNotFoundException`(class文件找不到异常)
当不存在对象的class文件时抛出此异常

反序列化的前提:

  1. 类必须实现Serializable
  2. 必须存在类对应的class文件

```java
public class Demo02ObjectInputStream {
    public static void main(String[] args) throws IOException, ClassNotFoundException {
        //1.创建ObjectInputStream对象,构造方法中传递字节输入流
        ObjectInputStream ois = new ObjectInputStream(new FileInputStream("io流文件测试\\person.txt"));
        //2.使用ObjectInputStream对象中的方法readObject读取保存对象的文件
        Object o = ois.readObject();
        //3.释放资源
        ois.close();
        //4.使用读取出来的对象(打印)
        System.out.println(o);
        Person p = (Person)o;
        System.out.println(p.getName()+p.getAge());
    }
}
```

### 序列化集合

练习：序列化集合

  当我们想在文件中保存多个对象的时候
  可以把多个对象存储到一个集合中
  对集合进序列化和反序列化

分析:

  1. 定义一个存储Person对象的ArrayList集合
  2. 往ArrayList集合中存储Person对象
  3. 创建一个序列化流ObjectOutputStream对象
  4. 使用ObjectOutputStream对象中的方法writeObject,对集合进行序列化
  5. 创建一个反序列化ObjectInputStream对象
  6.  使用ObjectInputStream对象中的方法readObject读取文件中保存的集合
  7. 把Object类型的集合转换为ArrayList类型
  8. 遍历ArrayList集合
  9. 释放资源

```java
public class Demo03Test {
    public static void main(String[] args) throws IOException, ClassNotFoundException {
        //1.定义一个存储Person对象的ArrayList集合
        ArrayList<Person> list = new ArrayList<>();
        //2.往ArrayList集合中存储Person对象
        list.add(new Person("张三",18));
        list.add(new Person("李四",19));
        list.add(new Person("王五",20));
        //3.创建一个序列化流ObjectOutputStream对象
        ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream("io流文件测试\\list.txt"));
        //4.使用ObjectOutputStream对象中的方法writeObject,对集合进行序列化
        oos.writeObject(list);
        //5.创建一个反序列化ObjectInputStream对象
        ObjectInputStream ois = new ObjectInputStream(new FileInputStream("io流文件测试\\list.txt"));
        //6.使用ObjectInputStream对象中的方法readObject读取文件中保存的集合
        Object o = ois.readObject();
        //7.把Object类型的集合转换为ArrayList类型
        ArrayList<Person> list2 = (ArrayList<Person>)o;
        //8.遍历ArrayList集合
        for (Person p : list2) {
            System.out.println(p);
        }
        //9.释放资源
        ois.close();
        oos.close();
    }
}
```

## 打印流

java.io.PrintStream:打印流
PrintStream extends OutputStream
PrintStream 为其他输出流添加了功能，使它们能够方便地打印各种数据值表示形式。

PrintStream特点:

  1. 只负责数据的输出,不负责数据的读取
  2. 与其他输出流不同，PrintStream 永远不会抛出 IOException
  3. 有特有的方法,print,println
     * void print(任意类型的值)
     * void println(任意类型的值并换行)

构造方法:

  * `PrintStream(File file)`:输出的目的地是一个文件
  * `PrintStream(OutputStream out)`:输出的目的地是一个字节输出流
  * `PrintStream(String fileName)` :输出的目的地是一个文件路径


继承自父类的成员方法:

  * `public void close() `：关闭此输出流并释放与此流相关联的任何系统资源。
  * `public void flush() `：刷新此输出流并强制任何缓冲的输出字节被写出。
  * `public void write(byte[] b)`：将 b.length字节从指定的字节数组写入此输出流。
  * `public void write(byte[] b, int off, int len) `：从指定的字节数组写入 len字节，从偏移量 off开始输出到此输出流。
  * `public abstract void write(int b) `：将指定的字节输出流。

注意:

  * 如果使用继承自父类的write方法写数据,那么查看数据的时候会查询编码表 97->a
  * 如果使用自己特有的方法print/println方法写数据,写的数据原样输出 97->97

```java
public class Demo01PrintStream {
    public static void main(String[] args) throws FileNotFoundException {
        //System.out.println("HelloWorld");

        //创建打印流PrintStream对象,构造方法中绑定要输出的目的地
        PrintStream ps = new PrintStream("io流文件测试\\print.txt");
        //如果使用继承自父类的write方法写数据,那么查看数据的时候会查询编码表 97->a
        ps.write(97);
        //如果使用自己特有的方法print/println方法写数据,写的数据原样输出 97->97
        ps.println(97);
        ps.println(8.8);
        ps.println('a');
        ps.println("HelloWorld");
        ps.println(true);

        //释放资源
        ps.close();
    }
}
```

可以改变输出语句的目的地(打印流的流向)
输出语句,默认在控制台输出
使用`System.setOut`方法改变输出语句的目的地改为参数中传递的打印流的目的地

* static void setOut(PrintStream out)
  重新分配“标准”输出流。

```java
public class Demo02PrintStream {
    public static void main(String[] args) throws FileNotFoundException {
        System.out.println("我是在控制台输出");

        PrintStream ps = new PrintStream("io流文件测试\\目的地是打印流.txt");
        System.setOut(ps);//把输出语句的目的地改变为打印流的目的地
        System.out.println("我在打印流的目的地中输出");

        ps.close();
    }
}
```

# JDBC

* 概念：`Java DataBase Connectivity` Java数据库连接
* JDBC本质：其实是官方（sun）定义的一套操作所有关系型数据库规则，即接口。各个数据库厂商去实现这套接口，提供数据库驱动jar包。我们可以使用这套接口（JDBC）编程，真正执行的代码是驱动jar包中的实现类
* 快速入门

  1. 导入驱动jar包
     1. 复制mysql-connector-java-5.1.37-bin.jar到项目的libs文件夹下
     2. 右键-->Add as Library
  2. 注册驱动
  3. 获取数据库连接对象 Connection
  4. 定义sql
  5. 获取执行sql语句的对象 Statement
  6. 执行sql，接受返回结果
  7. 处理结果
  8. 释放资源

```java
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.Statement;

/*
jdbc快速入门
 */
public class JDBCDemo01 {
    public static void main(String[] args) throws Exception {
        //1.导入驱动
        //2.注册驱动
        Class.forName("com.mysql.jdbc.Driver");
        //3.获取数据库连接对象
        Connection conn = DriverManager.getConnection("jdbc:mysql://localhost:3306/db1", "root", "root");
        //4.定义一个sql语句
        String sql = "update account set balance = 500 where id = 1;";
        //5.获取执行sql的对象 Statement
        Statement stmt = conn.createStatement();
        //6.执行sql
        int count = stmt.executeUpdate(sql);
        //7.处理结果
        System.out.println(count);
        //8.释放资源
        stmt.close();
        conn.close();
    }
}
```

## JDBC各个类详解

### 1. DriverManager：驱动管理对象
   * 功能
   1. 注册驱动:告诉程序该使用哪一个数据库驱动jar
      `static void registerDriver(Driver driver)` 注册与给定的驱动程序 DriverManager 。 
      写代码使用：`Class.forName("com.mysql.jdbc.Driver");`
      `com.mysql.jdbc.Driver`类原码中有静态代码块
      注意：mysql5之后的驱动jar包可以省略驱动注册
```java
static {
	try {
		java.sql.DriverManager.registerDriver(new Driver());
	} catch (SQLException E) {
		throw new RuntimeException("Can't register driver!");
	}
}
```
   2. 获取数据库连接
   * 方法：`static Connection getConnection(String url, String user, String password)` 尝试建立与给定数据库URL的连接。 
   * 参数
     * url:指定连接的路径
       * 语法：`jdbc:mysql://ip地址(域名):端口号/数据库名称`
       * 如果连接的是本机的服务器，并且mysql服务默认端口是3306，则url简写为`jdbc:mysql:///数据库名称`
     * user:用户名
     * password：密码
### 2. Connection：数据库连接对象
   1. 功能：
      1. 获取执行的sql对象
         * `Statement createStatement() `创建一个 Statement对象，用于将SQL语句发送到数据库。  
         * `PreparedStatement prepareStatement(String sql)` 创建一个 PreparedStatement对象，用于将参数化的SQL语句发送到数据库。  
      2. 管理事物
         * 开启事务：`void setAutoCommit(boolean autoCommit) ` 设置参数为false，即开启事务
         * 提交事务：`void commit()`
         * 回滚事务：`void rollback()`
### 3. Statement：执行sql对象
   1. 执行sql
      1. `boolean execute(String sql) `：可以执行任意的sql语句
      2. `int executeUpdate(String sql)`：执行DML（insert，update，delect）语句，DDL（create，alter，drop）语句
         * 返回值：影响的行数，通过影响的行数，判断DML语句是否执行成功，返回值>0成功，反之失败
      3. `ResultSet executeQuery(String sql)`：执行DQL（select）语句
```java
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;
import java.sql.Statement;

/*
account 表添加一条记录 insert
 */
public class JDBCDemo02 {
    public static void main(String[] args) {
        Statement stmt = null;
        Connection conn = null;
        try {
            //1.注册驱动
            Class.forName("com.mysql.jdbc.Driver");
            //2.定义sql
            String sql = "insert into account() values(null,'王五',3000)";
            //3.获取Connection对象
            conn = DriverManager.getConnection("jdbc:mysql:///db1", "root", "root");
            //4.获取执行的sql对象 Statement
            stmt = conn.createStatement();
            //5.执行sql
            int count = stmt.executeUpdate(sql);//影响的行数
            //6.处理结果
            System.out.println(count);
            if (count > 0) {
                System.out.println("添加成功");
            } else {
                System.out.println("添加失败");
            }
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        } catch (SQLException e) {
            e.printStackTrace();
        } finally {
            //7.释放资源
            //避免空指针异常
            if (stmt != null) {
                try {
                    stmt.close();
                } catch (SQLException e) {
                    e.printStackTrace();
                }
            }
            if (conn != null) {
                try {
                    conn.close();
                } catch (SQLException e) {
                    e.printStackTrace();
                }
            }
        }
    }
}
```
### 4. ResultSet：结果集对象，封装查询结果
   * `boolean next()`：游标线下移动一行，判断当前行是否是最后一行末尾，如果是返回false
   * `getxxx(参数)`：获取数据
     * xxx：代表数据类型  如：`int getInt()`;
     * 参数：
       1. int：代表列的编号，从1开始
       2. String：代表列名称
   * 注意
     * 使用步骤：
       1. 游标向下移动一行
       2. 判断是否有数据
       3. 获取数据

```java
//1.注册驱动
Class.forName("com.mysql.jdbc.Driver");
//2.获取Connection对象
conn = DriverManager.getConnection("jdbc:mysql:///db1", "root", "root");
//3.定义sql
String sql = "select * from account";
//4.获取执行sql对象
stmt = conn.createStatement();
//5. 执行sql
rs = stmt.executeQuery(sql);
//6.处理结果
while (rs.next()){
//循环判断是否有数据
int id = rs.getInt(1);
String name = rs.getString("name");
double balance = rs.getDouble(3);
System.out.println(id + "---" + name + "---" + balance);
```

### 5. PreparedStatement：执行sql的对象

1. sql注入问题：在拼接sql时有一些sql的特殊关键字参与字符串拼接，会造成安全性问题
2. 解决sql注入问题：使用`PreparedStatement`对象来解决
3. 预编译sql：参数使用？作为占位符
4. 步骤
  1. 导入驱动jar包
  2. 注册驱动
  3. 获取数据库连接对象` Connection`
  4. 定义sql
     * 注意：sql参数使用？作为占位符  `select * from user where username = ? and password = ?`
  5. 获取执行sql语句的对象` PreparedStatement`  ，使用`Connection`接口的`PreparedStatement prepareStatement(String sql)`
  6. 给问号赋值
     * 方法：`setXxx(参数1，参数2)` 
     * 参数1：问号的位置，从1开始
     * 参数2：问号的值
  7. 执行sql，接受返回结果，不需要传递sql语句
  8. 处理结果
  9. 释放资源
5. 注意，后期都会使用`PreparedStatement`来完成增删改查的操作
   1. 防止sql注入问题
   2. 效率更高

## JDBC控制事务
1. 事务：一个包含多个步骤的业务操作。如果这个业务操作被事务管理，则这多个步骤要吗同时成功，要吗同时失败
2. 操作
   1. 开启事务
   2. 提交事务
   3. 回滚事务
3. 使用Connection对象来管理事务
   * 开启事务：`void setAutoCommit(boolean autoCommit) ` 设置参数为false，即开启事务
     * 在执行sql之前开启事务
   * 提交事务：`void commit()`
     * 在所有sql执行完提交事务
   * 回滚事务：`void rollback()`
     * 在catch回滚事务

## 抽取jdbc工具类：JDBCUtils

* 目的：简化书写
* 分析
  1. 注册驱动抽取
  2. 抽取方法获取连接对象
     * 不想传递参数，还得保证工具类的通用性
     * 解决：配置文件
     
  3. 抽取方法释放释放

```java
import java.io.FileReader;
import java.io.IOException;
import java.net.URL;
import java.sql.*;
import java.util.Properties;

/**
 * JDBC工具类
 */
public class JDBCUtils {
    private static String url;
    private static String user;
    private static String password;
    private static String driver;

    /**
     * 文件的读取只需要一次就可以。使用静态代码块
     */
    static {
        //读取资源文件，获取值
        try {
            //1.创建properties集合类
            Properties pro = new Properties();

            //获取src路径下的文件方式-->ClassLoad 类加载器
            ClassLoader classLoader = JDBCUtils.class.getClassLoader();
            URL res = classLoader.getResource("jdbc.properties");
            String path = res.getPath();

            //2.加载文件
            pro.load(new FileReader(path));

            //3.获取属性赋值
            url = pro.getProperty("url");
            user = pro.getProperty("user");
            password = pro.getProperty("password");
            driver = pro.getProperty("driver");
            //4.注册驱动
            Class.forName(driver);
        } catch (IOException e) {
            e.printStackTrace();
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        }
    }

    /**
     * 获取连接
     *
     * @return 连接对象
     */
    public static Connection getConnection() throws SQLException {
        return DriverManager.getConnection(url, user, password);
    }

    /**
     * 释放资源
     *
     * @param stmt
     * @param conn
     */
    public static void close(Statement stmt, Connection conn) {
        if (stmt != null) {
            try {
                stmt.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
        if (conn != null) {
            try {
                conn.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
    }

    public static void close(ResultSet rs, Statement stmt, Connection conn) {
        if (rs != null) {
            try {
                rs.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
        if (stmt != null) {
            try {
                stmt.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
        if (conn != null) {
            try {
                conn.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
    }
}
```

## 数据库连接池

1. 概念 ：其实就是一个容器（集合）存放数据库连接的容器

当系统初始化好后，容器被创建，容器中会申请一些连接对象，当用户来访问数据库时，从容器中获取连接对象，用户访问完之后，会将连接对象归还给容器

2. 好处
   1. 节约资源
   2. 用户访问高效

3. 实现：
   1. 标准接口：`DataSource` javax.sql包下的
      1. 方法：
         * 获取连接：`getConnection()`
         * 归还连接：如果Connection对象是从连接池中获取的，那么调用Connection.close()方法，则不会关闭连接，而是归还连接
   2. 一般我们不去实现，由数据库厂商来实现
      1. C3P0：数据库连接池技术
      2. Druid：数据库连接处技术，阿里巴巴实现

4. C3P0
   * 步骤：
     1. 导入jar包 `c3p0-0.9.5.2.jar`和`mchange-commons-java-0.2.12.jar`
     2. 定义配置文件：
        * 名称：`c3p0.properties` 或者 `c3p0-config.xml`
        * 路径：直接将文件放在src目录下
     3. 创建核心对象 数据库连接池对象 `ComboPooledDataSourse`
     4. 获取连接：`getConnection()`

```java
public static void main(String[] args) throws SQLException {
    //1.创建数据库连接池对象,什么都不传使用默认配置
    //DataSource ds = new ComboPooledDataSource();
    //使用指定名称的配置
    DataSource ds = new ComboPooledDataSource("otherc3p0");
    //2.获取连接对象
    Connection conn = ds.getConnection();

    //3.打印
    System.out.println(conn);
}
```
```xml
<c3p0-config>
  <!-- 使用默认的配置读取连接池对象 -->
  <default-config>
  	<!--  连接参数 -->
    <property name="driverClass">com.mysql.jdbc.Driver</property>
    <property name="jdbcUrl">jdbc:mysql://localhost:3306/db2</property>
    <property name="user">root</property>
    <property name="password">root</property>
    
    <!-- 连接池参数 -->

    <!-- 初始化申请的连接数量 -->
    <property name="initialPoolSize">5</property>
    <!-- 最大的的连接数量 -->
    <property name="maxPoolSize">10</property>
    <!-- 超时时间 -->
    <property name="checkoutTimeout">3000</property>
  </default-config>

  <named-config name="otherc3p0"> 
    <!--  连接参数 -->
    <property name="driverClass">com.mysql.jdbc.Driver</property>
    <property name="jdbcUrl">jdbc:mysql://localhost:3306/db1</property>
    <property name="user">root</property>
    <property name="password">root</property>
    
    <!-- 连接池参数 -->
    <property name="initialPoolSize">5</property>
    <property name="maxPoolSize">8</property>
    <property name="checkoutTimeout">1000</property>
  </named-config>
</c3p0-config>
```

## Druid：数据库连接处技术，阿里巴巴实现
* 步骤：
  1. 导入jar包 `druid-1.0.9.jar`
  2. 定义配置文件：
     * 是Properties形式
     * 可以叫任意名称，可以放在任意目录
  3. 加载配置文件Properties
  4. 获取数据库连接池对象：通过工厂来过去 DruidDataSouceFactory
  5. 获取连接

* 定义工具类

  1. 定义一个类 JDBCUtils
  2. 提供方法
     1. 获取连接方法：通过数据库连接池获取连接

## Spring JDBC

* Spring框架对jdbc的简单封装。提供了了JDBCTempate对象简化jdbc开发

* 步骤：
  1. 导入jar包
  2. 创建JdbcTemplate对象，依赖于数据源DataSource
     * `JdbcTempalte tempate = new JdbcTemplate(ds);`

  3. 调用JdbcTemplate的方法完成crud的操作
     * `update()`:执行dml语句，增删改
     * `queryForMap()`:查询结果将结果封装为map
       * 注意：这个方法查询的结果集长度只能是1
     * `queryForList()`:查询结果将结果封装为List
       * 注意：将每一条记录封装为一个map集合，再将集合封装为list集合
     * `query()`:查询结果，将结果封装为JavaBean对象
       * 参数：RowMapper
         * 一般使用`BeanPropertyRowMapper`实现类，完成数据到`JavaBean`的自动封装
         * `new BeanPropertyRowMapper<类型>(类型.class)`
     * `queryForObject()`：查询结果，将结果封装为对象
       * 一般用于聚合函数的查询

```java
public class Demo02 {
    //Junit单元测试，可以让方法独立执行
    //1.获取JDBCTemplate
    private JdbcTemplate template = new JdbcTemplate(JDBCUtils.getDataSource());

    /**
     * 1. 修改1号数据的 salary 为 10000
     */
    @Test
    public void text1() {
        //2.定义sql
        String sql = "update emp set salary = 10000 where id = 1001";
        //3.执行sql
        int count = template.update(sql);
        System.out.println(count);
    }

    /**
     * 2. 添加一条记录
     */
    @Test
    public void text2() {
        //2.定义sql
        String sql = "insert into emp (id,ename,dept_id) value(?,?,?)";
        //3.执行sql
        int count = template.update(sql, 1015, "郭靖", 10);
        System.out.println(count);
    }

    /**
     * 3. 删除刚才添加记录
     */
    @Test
    public void text3() {
        //2.定义sql
        String sql = "delete from emp where id = ?";
        //3.执行sql
        int count = template.update(sql, 1015);
        System.out.println(count);
    }


    /**
     * 4. 查询id为1001的记录，将其封装为Map集合
     * 注意：这个方法查询的结果集长度只能是1
     */
    @Test
    public void text4() {
        //2.定义sql
        String sql = "select * from emp where id = ?";
        //3.执行sql
        Map<String, Object> map = template.queryForMap(sql, 1001);
        System.out.println(map);
    }

    /**
     * 5. 查询所有的记录，将其封装为List集合4
     * 注意：将每一条记录封装为一个map集合，再将集合封装为list集合
     */
    @Test
    public void text5() {
        //2.定义sql
        String sql = "select * from emp";
        //3.执行sql
        List<Map<String, Object>> list = template.queryForList(sql);
        for (Map<String, Object> map : list) {
            System.out.println(map);
        }
    }

    /**
     * 6. 查询所有的记录，将其封装为Emp对象的List集合
     */
    @Test
    public void text6() {
        //2.定义sql
        String sql = "select * from emp";
        //3.执行sql

        List<Emp> list = template.query(sql, new RowMapper<Emp>() {
            @Override
            public Emp mapRow(ResultSet rs, int i) throws SQLException {
                Emp emp = new Emp();
                int id = rs.getInt("id");
                String ename = rs.getString("ename");
                int job_id = rs.getInt("job_id");
                int mgr = rs.getInt("mgr");
                Date joindate = rs.getDate("joindate");
                double salary = rs.getDouble("salary");
                double bonus = rs.getDouble("bonus");
                int dept_id = rs.getInt("dept_id");
                emp.setId(id);
                emp.setEname(ename);
                emp.setJob_id(job_id);
                emp.setMgr(mgr);
                emp.setJoindate(joindate);
                emp.setSalary(salary);
                emp.setBonus(bonus);
                emp.setDept_id(dept_id);

                return emp;
            }
        });
        for (Emp emp : list) {
            System.out.println(emp);
        }
    }

    /**
     * 6. 查询所有的记录，将其封装为Emp对象的List集合
     */
    @Test
    public void text6_2() {
        //2.定义sql
        String sql = "select * from emp";
        //3.执行sql
        List<Emp> list = template.query(sql, new BeanPropertyRowMapper<Emp>(Emp.class));
        for (Emp emp : list) {
            System.out.println(emp);
        }
    }

    /**
     *     7. 查询记录数
     */
    @Test
    public void text7() {
        //2.定义sql
        String sql = "select count(id) from emp";
        //3.执行sql
        Long count = template.queryForObject(sql, Long.class);
        System.out.println(count);
    }
}
```

# 网络编程

## 三要素

1. 协议

* TCP：速度慢，安全，三次握手
* UDP：速度快，可能丢失数据

2. IP地址

* IPv4：是一个32位的二进制数，通常被分为4个字节，表示成`a.b.c.d` 的形式，例如`192.168.65.100` 。其中a、b、c、d都是0~255之间的十进制整数，那么最多可以表示42亿个。

* IPv6：由于互联网的蓬勃发展，IP地址的需求量愈来愈大，但是网络地址资源有限，使得IP的分配越发紧张。

  为了扩大地址空间，拟通过IPv6重新定义地址空间，采用128位地址长度，每16个字节一组，分成8组十六进制数，表示成`ABCD:EF01:2345:6789:ABCD:EF01:2345:6789`，号称可以为全世界的每一粒沙子编上一个网址，这样就解决了网络地址资源数量不够的问题。

常用命令

* 查看本机IP地址，在控制台输入：

```shell
ipconfig
```

* 检查网络是否连通，在控制台输入：

```shell
ping 空格 IP地址
ping 220.181.57.216
```

特殊的IP地址

* 本机IP地址：`127.0.0.1`、`localhost` 。

3. 端口号：

用两个字节表示的整数，它的取值范围是0~65535**。其中，0~1023之间的端口号用于一些知名的网络服务和应用，普通的应用程序需要使用1024以上的端口号。如果端口号被另外一个服务或应用所占用，会导致当前程序启动失败。

利用`协议`+`IP地址`+`端口号` 三元组合，就可以标识网络中的进程了，那么进程间的通信就可以利用这个标识与其它进程进行交互。

## TCP服务器端

* TCP通信的服务器端：接受客户端的请求，给客户端回写数据
* 表示服务器的类:java.net.ServerSocket 这个类实现了服务器套接字。 服务器套接字等待通过网络进入的请求。 它根据该请求执行一些操作，然后可能将结果返回给请求者。

* 构造方法:`ServerSocket(int port)` 创建绑定到指定端口的服务器套接字。

服务器必须明确一件事情，必须知道那个客户端请求的服务器
所以可以使用accept方法获取到请求的客户端对象Socket

* 成员方法:`Socket accept()` 侦听要连接到此套接字并接受它。

* 服务器实现步骤:
  1. 创建服务器ServerSocket对象和系统指定好的端口号
  2. 使用ServerSocket对象的方法accept()，获取到请求的客户端对象Socket
  3. 使用Socket对象中的getInputStream()获取网络字节输入流InputStream对象
  4. 使用网络字节输入流InputStream对象中的方法read,读取客户端发送的数据
  5. 使用Socket对象中的getOutputStream()获取网络字节输出流OutputStream对象
  6. 使用网络字节输出流OutputStream对象中的方法write,给客户端回写数据
  7. 释放(Socket，ServerSocket)
```java
public static void main(String[] args) throws IOException {
    //1. 创建服务器ServerSocket对象和系统指定好的端口号
    ServerSocket server = new ServerSocket(8888);
    //2. 使用ServerSocket对象的方法accept()，获取到请求的客户端对象Socket
    Socket socket = server.accept();
    //3. 使用Socket对象中的getInputStream()获取网络字节输入流InputStream对象
    InputStream is = socket.getInputStream();
    //4. 使用网络字节输入流InputStream对象中的方法read,读取客户端发送的数据
    byte[] bytes = new byte[1024];
    int len = is.read(bytes);
    System.out.println(new String(bytes, 0, len));
    //5. 使用Socket对象中的getOutputStream()获取网络字节输出流OutputStream对象
    OutputStream os = socket.getOutputStream();
    //6. 使用网络字节输出流OutputStream对象中的方法write,给客户端回写数据
    os.write("收到谢谢".getBytes());
    //7. 释放(Socket，ServerSocket)
    socket.close();
    server.close();
}
```

## TCP客户端

TCP通信的客户端:向服务器发送链接请求，给服务器发送数据，读取服务器回写的数据

* 表示客户端的类：java.net.Socket：该类实现客户端套接字（也称为“套接字”）。 套接字是两台机器之间通讯的端点。
* 套接字：包含了ip地址和端口号的网络单位

* 构造方法：Socket(String host, int port) 创建流套接字并将其连接到指定主机上的指定端口号。
参数：
`String host`：服务器主机的名称/服务器ip地址
`int port`：服务器端口号

* 成员方法
`OutputStream getOutputStream()` 返回此套接字的输出流。
`InputStream getInputStream()` 返回此套接字的输入流。
`void close() 关闭此套接字。`

* 实现步骤：
  1. 创建一个客户端对象Socket，构造方法绑定服务器ip地址和端口号
  2. 使用Socket对象中的getOutputStream()获取网络字节输出流OutputStream对象
  3. 使用网络字节输出流OutputStream对象中的方法write,给服务器发送数据
  4. 使用Socket对象中的getInputStream()获取网络字节输入流InputStream对象
  5. 使用网络字节输入流InputStream对象中的方法read,读取服务器回写的数据
  6. 释放资源（Socket）

* 注意
  1. 客户端和服务器端进行交互，必须使用Socket中提供的网络流，不能使用自己创建的流对象
  2. 当我们创建客户端对象Socket的时候，就会去请求服务器经过3次我收连接通路
     如果服务器没有启用，那么就会抛出异常
     如果服务器已经启动，那么就可以交互了

```java
public static void main(String[] args) throws IOException {
    //1. 创建一个客户端对象Socket，构造方法绑定服务器ip地址和端口号
    Socket socket = new Socket("127.0.0.1", 8888);
    //2. 使用Socket对象中的getOutputStream()获取网络字节输出流OutputStream对象
    OutputStream os = socket.getOutputStream();
    //3. 使用网络字节输出流OutputStream对象中的方法write,给服务器发送数据
    os.write("你好服务器".getBytes());
    //4. 使用Socket对象中的getInputStream()获取网络字节输入流InputStream对象
    InputStream is = socket.getInputStream();
    //5. 使用网络字节输入流InputStream对象中的方法read,读取服务器回写的数据
    byte[] bytes = new byte[1024];
    int len = is.read(bytes);
    System.out.println(new String(bytes, 0, len));
    //6. 释放资源（Socket）
    socket.close();
}
```

## 案例:文件上传

* 客户端

```java
public class TCPClient {
    public static void main(String[] args) throws IOException {
        //1. 创建一个本地自己输入流FileInputStream对象，构造方法中绑定要读取的数据源
        FileInputStream fis = new FileInputStream("C:\\1.jpg");
        //2. 创建一个Socket对象，构造方法中绑定服务器的ip地址和端口号
        Socket socket = new Socket("127.0.0.1", 8888);
        //3. 使用Socket中的方法getOutputStream获取网络字节输出流OutputStream对象
        OutputStream os = socket.getOutputStream();
        //4. 使用本地的字节输入流FileInputStream对象的read方法，读取本地文件
        int len = 0;
        byte[] bytes = new byte[1024];
        while ((len = fis.read(bytes)) != -1) {
            //5. 使用网络字节输出流OutputStream对象的方法write，把读取到的文件上传到服务器
            os.write(bytes, 0, len);
        }

        /*
        上传时未读取到文件的的结束
        解决：给服务器写一个结束标记
        void shutdownOutput() 禁用此套接字的输出流。
        禁用此套接字的输出流。 对于TCP套接字，任何先前写入的数据将被发送，随后是TCP的正常连接终止序列。
         */
        socket.shutdownOutput();
        
        //6. 使用Socket中的方法getInputStream获取网络字节输入流InputStream对象
        InputStream is = socket.getInputStream();
        //7. 使用网络字节输入流InputStream对象的方法read读取服务器回写的数据
        while ((len = is.read(bytes)) != -1) {
            System.out.println(new String(bytes, 0, len));
        }
        //8. 释放资源(FileInputStream，Socket)
        fis.close();
        socket.close();
    }
}
```

* 服务器端

```java
public class TCPService {
    public static void main(String[] args) throws IOException {
        //1. 创建一个服务器ServerSocket对象，和系统指定的端口号
        ServerSocket ss = new ServerSocket(8888);
        //2. 使用ServerSocket对象中的方法accept(),获取到请求的客户端对象

        /*
        让服务器一直处于监听状态(死循环accept)
        有一个客户端上传文件，就保存一个文件
         */
        while (true) {
            Socket socket = ss.accept();
            /*
            使用多线程提高程序效率
            有一个客户端上传文件我就开启线程，完成文件上传
             */

            new Thread(new Runnable() {
                @Override
                public void run() {
                    //父类方法没有声明异常，子类也不能声明，只能用try/catch
                    try {
                        //3. 使用Socket对象中的getInputStream()获取网络字节输入流InputStream对象
                        InputStream is = socket.getInputStream();
                        //4. 判断文件夹是否存在，若不存在则创建
                        File f = new File("D:\\upload");
                        if (!f.exists()) {
                            f.mkdirs();
                        }
                        /*
                        自定义文件命名规则：防止同名文件被覆盖
                        规则：域名+毫秒值+随机数
                         */
                        String filename = "dxy" + System.currentTimeMillis() + new Random().nextInt(999999) + ".jpg";

                        //5. 创建一个本地的字节输出流FileOutputStream对象,构造方法中绑定要输出的目的地
                        //FileOutputStream fos = new FileOutputStream(f + "\\1.jpg");
                        FileOutputStream fos = new FileOutputStream(f + "\\" + filename);
                        //6. 使用网络字节输入流InputStream对象的方法read读取客户端上传的文件
                        int len = 0;
                        byte[] bytes = new byte[1024];
                        //7. 使用本地的字节输入流FileOutputStream对象的write方法，把读取到的文件保存到服务器的硬盘上
                        while ((len = is.read(bytes)) != -1) {
                            fos.write(bytes, 0, len);
                        }
                        //8. 使用Socket中的方法getOutputStream获取网络字节输出流OutputStream对象
                        //9. 使用网络字节输出流OutputStream对象的方法write，给客户端回写“上传成功”
                        socket.getOutputStream().write("上传成功".getBytes());
                        //10. 释放资源(FileOutputStream,Socket,ServerSocket)
                        fos.close();
                        socket.close();
                    } catch (IOException e) {
                        e.printStackTrace();
                    }
                }
            }).start();

        }
        //服务器不用关闭
        //ss.close();
    }
}
```