# JDK8新特性
## 1.接口新特性
在jdk8环境中，接口中的方法不再只能有抽象方法，他可以有静态方法和default方法，所谓default方法即是使用default关键字来修饰的方法，一个接口中可以有多个静态方法和default方法，没有个数限制。实现类只需要实现接口的抽象方法。

**示例代码：**
```
public interface DefalutTest {
    static int a =5;
    default void defaultMethod(){
        System.out.println("DefalutTest defalut 方法");
    }

    int sub(int a,int b);

    static void staticMethod() {
        System.out.println("DefalutTest static 方法");
    }
}
```
<kbd>接口里的静态方法，即static修饰的有方法体的方法不会被继承或则实现，但是静态变量会被继承。default方法可以被子接口继承，也可以被其实现类所调用，当default方法被继承时，可以被子接口覆写</kbd>

**关于静态方法和default的调用**

1. 对于静态方法，没有什么特殊的地方，此方法既不能被其子接口调用，也不能被其实现类调用，静态方法只能被自身调用。
2. 对于default方法，需要有实例的对象来调用，但是要注意的是：java中是单继承的，但可以实现多个接口，当一个类实现了多个接口之后，如果多个没有继承关系接口有着相同的default方法，即方法名和参数列表相同，这个时候就需要在实现类里面显示重写default方法，且必须通过特殊语法指定实现类要实现哪个接口中的default方法
**特殊语法：<接口>.super.<方法名>([参数])**

**示例代码**
```
public class SubTestImp implements SubTest,DefalutTest{

    @Override
    public int sub(int a, int b) {
        // TODO Auto-generated method stub
        return a-b;
    }

    @Override
    public void defaultMethod() {
        // TODO Auto-generated method stub
        DefalutTest.super.defaultMethod();
    }
}
```
**重写的default方法必须的访问权限是public，因为default方法畜类没有显示的访问修饰符外，只能用public访问限定符来修饰，而在java中，要重写一个方法，访问限定符一定要大于父类或则接口指定的访问限定符范围，而且方法声明处抛出的异常也要大于后者，所以访问权限必须是public。左后当default方法和实现类继承的父类的方法名相同时，优先调用父类方法。**

**默认方法的优势**

1. 默认方法的主要优势是提供一种拓展接口的方法，而不破坏现有代码。假如我们有一个已经投入使用接口需要拓展一个新的方法，在jdk8以前，如果我们使用接口增加一个方法，则我们必须在所有实现类中添加该方法的实现，否则编译器会出现异常。如果实现类数量少并且我们有权限修改，可能会工作量较少。如果实现类或则我们没有权限修改实现类源代码，这样可能就比较麻烦。而默认方法则解决了这个问题，它提供了一个实现，当没有秀按时提供其他实现时就采用这个实现。这样新添加的方法就不会破坏现有代码。
2. 默认方法的另一个优势是该方法是可选的，子类可以根据不同的需求Override默认方法

## 2.Lambda表达式
Lambda表达式可以看成是匿名内部类，使用Lambda表达式时，接口必须是函数式接口

**函数式接口**

能够接收Lambda表达式的参数类型，是一个只包含一个方法的接口。

**基本语法**

```
<函数式接口>  <变量名> = (参数1，参数2...) -> {
                  //方法体
      }
```
**说明**

（参数1，参数2）表示参数列表；-->表示连接符；{}内部是方法体
  1. =右边的类型会根据左边函数式接口类型自行推断
  2. 如果形参列表为空，只保留()
  3. 如果形参只有一个，（）可以省略，只需要参数名称即可
  4. 如果执行语句只有1句，且无返回值，{}可以省略，若有返回值，则若想省去{}，则必须同时省略return，且执行语句也只保证只有一句
  5. 形参列表的数据类型会自动推断
  6. Lambda不会生成一个单独的内部类文件
  7. lambda表达式若访问局部变量，则局部变量必须是final的，若是局部变量没有加final，系统会自动添加，此后在修改该局部变量，会报错。

**示例代码**

```
public interface LambdaTest {

    abstract void print();
}

public interface LambdaTest2 {

    abstract void print(String a);
}

public interface DefalutTest {

    static int a =5;
    default void defaultMethod(){
        System.out.println("DefalutTest defalut 方法");
    }

    int sub(int a,int b);

    static void staticMethod() {
        System.out.println("DefalutTest static 方法");
    }
}

public class Main {

    public static void main(String[] args) {
        //匿名内部类--java8之前的实现方式
        DefalutTest dt = new DefalutTest(){
            @Override
            public int sub(int a, int b) {
                // TODO Auto-generated method stub
                return a-b;
            }
        };

        //lambda表达式--实现方式1
        DefalutTest dt2 =(a,b)->{
            return a-b;
        };
        System.out.println(dt2.sub(2, 1));

        //lambda表达式--实现方式2，省略花括号
        DefalutTest dt3 =(a,b)->a-b;
        System.out.println(dt3.sub(5, 6));

        //测试final
        int c = 5;
        DefalutTest dt4 =(a,b)->a-c;
        System.out.println(dt4.sub(5, 6));

        //无参方法，并且执行语句只有1条
        LambdaTest lt = ()-> System.out.println("测试无参");
        lt.print();
        //只有一个参数方法
        LambdaTest2 lt1 = s-> System.out.println(s);
        lt1.print("有一个参数");
    }
}

```

### Lambda表达式的其他特性
**1.引用实例方法**
```
<函数式接口>  <变量名> = <实例>::<实例方法名>
    //调用
    <变量名>.接口方法([实际参数...])
```
将调用方法时传递的实际参数，全部传递给应用的方法，执行引用的方法

**示例代码**
```
public class Main {

    public static void main(String[] args) {

        LambdaTest2 lt1 = s-> System.out.println(s);
        lt1.print("有一个参数");

        //改写为：
        LambdaTest2 lt2 = System.out::println;
        lt2.print("实例引用方式调用");
    }
}
```
**引用类方法**
```
<函数式接口>  <变量名> = <类>::<类方法名称>
    //调用
    <变量名>.接口方法([实际参数...])
```

**示例方法**
```
public interface LambdaTest3 {

     abstract void sort(List<Integer> list,Comparator<Integer> c);
}

public class Main {

    public static void main(String[] args) {
        List<Integer>  list = new ArrayList<Integer>();
        list.add(50);
        list.add(18);
        list.add(6);
        list.add(99);
        list.add(32);
        System.out.println(list.toString()+"排序之前");
        LambdaTest3 lt3 = Collections::sort;
        lt3.sort(list, (a,b) -> {
            return a-b;
        });
        System.out.println(list.toString()+"排序之后");
    }
}
```
**引用类的实例方法**
```
//定义接口
   interface <函数式接口>{
       <返回值> <方法名>(<类><类名称>,[其他参数...]);
   }
   <函数式接口>  <变量名> = <类>::<类实例方法名>
   //调用
   <变量名>.接口方法(类的实例,[实际参数...])
```
**示例代码**
```
public class LambdaClassTest {

    public int add(int a, int b){
        System.out.println("LambdaClassTest类的add方法");
        return a+b;
    }
}

public interface LambdaTest4 {

    abstract int add(LambdaClassTest lt,int a,int b);
}

public class Main {

    public static void main(String[] args) {
        LambdaTest4 lt4 = LambdaClassTest::add;
        LambdaClassTest lct = new LambdaClassTest();
        System.out.println(lt4.add(lct, 5, 8));
    }
}

```

## 函数式接口

如果一个接口只有一个抽象方法，则该接口称之为函数式接口，因为默认方法不算抽象方法，所以可以给函数式接口添加默认方法。

函数式接口可与使用Lambda表达式，Lambda表达式会被匹配到这个抽象方法上，我们可以你讲lambda表达式当做任意只包含一个抽象方法的接口类型，确保你的接口一定要达到这个要求，你只需要给你的额借口添加@FunctionalInterface注解，编译器如果发现你标注了这个注解的接口有多余一个抽象方法的时候就会报错。

## Lambda作用域
在Lambda表达式中访问外层作用域和匿名内部类对象的方式很相似。你可以直接访问标记了final的外部局部变量，或则实例的字段以及静态变量。

我们可以直接在lambda表达式中访问外层的局部变量，但是该局部变量必须是final的，即使没有加final关键字，之后我们无论在哪（lambda表达式内部或外部）修改该变量，均报错。

##  访问接口的默认方法
 **Predicate接口**

 Predicate接口只有一个参数，返回boolean类型。该接口包含多种默认方法来讲Predicate组合成其他复杂的逻辑（比如：与，或，非）。该接口包含一个抽象方法，3个默认方法以及一个静态方法，抽象方法为test。

 **示例代码**
 ```
 public static void main(String[] args) {
         Predicate<String> predicate = (s) -> s.length() > 0;
         System.out.println(predicate.test("foo"));              // true
         System.out.println(predicate.negate().test("foo"));     // false
         Predicate<Boolean> nonNull = Objects::nonNull;
         Predicate<Boolean> isNull = Objects::isNull;
         Predicate<String> isEmpty = String::isEmpty;
         Predicate<String> isNotEmpty = isEmpty.negate();
         System.out.println(nonNull.test(null));
         System.out.println(isNull.test(null));
         System.out.println(isEmpty.test("sss"));
         System.out.println(isNotEmpty.test(""));
```
**Function接口**
Function接口有一个参数并返回一个结果，并附带了一些可以和其他函数组合的默认方法

**示例方法**
```
Function<String, Integer> toInteger = Integer::valueOf;
        System.out.println(toInteger.apply("123").getClass());
        Function<String, Object> toInteger2 = toInteger.andThen(String::valueOf);
        System.out.println(toInteger2.apply("123").getClass());

```

**Consumer消费型函数式接口**
代表了接受一个输入参数并且无返回的操作

**accept方法使用**
```
public static void modifyTheValue3(int value, Consumer<Integer> consumer) {
       consumer.accept(value);
   }

   public static void main(String[] args) {
       // (x) -> System.out.println(x * 2)接受一个输入参数x
       // 直接输出，并没有返回结果
       // 所以该lambda表达式可以实现Consumer接口
       modifyTheValue3(3, (x) -> System.out.println(x * 2));
   }
```
**Comparator接口**

Comparator是java中的经典接口，java8在此之上添加了多种默认方法：
```
Comparator<Person> comparator = (p1, p2) -> p1.firstName.compareTo(p2.firstName);
Person p1 = new Person("John", "Doe");
Person p2 = new Person("Alice", "Wonderland");
comparator.compare(p1, p2);                    // > 0
comparator.reversed().compare(p1, p2);         // < 0
```

**Optional接口**

Optional不是函数式接口，这个是用来防止NullPointerException异常的辅助类型。
Optional被定义为一个简单的容器，其值可能是null或则不是null。在java8之前一般某个函数应该返回非空对象但是偶尔却可能返回null，而在java 8，不推荐使用null而是返回Optional
```
Optional<String> optional = Optional.of("bam");
optional.isPresent();           // true
optional.get();                 // "bam"
optional.orElse("fallback");    // "bam"
optional.ifPresent((s) -> System.out.println(s.charAt(0)));     // "b"

```
**Stream 接口                          重要**

**什么是Stream？**

Stream是元素的集合，这点让Stream看起来类似Iterator。可以把Stream当成是一个高级版本的IT二胺投入。原始的Iterator，用户只能一个一个的遍历元素并对其执行某些操作，高级版本的Stream，用户只要给出其包含的元素执行什么操作比如“过滤掉大于10的字符串等”，具体这些操作如何应用到每个元素上，Stream可以完成。

首先对Stream的操作可以分为两类，中间操作和结束操作
1. 中间操作总是会惰式执行，调用中间操作只会生成一个标记了该操作的新stream。
2. 结束操作触发实际计算，计算发生时会把所有的中间操作积攒的操作以pipeline的方式执行，这样可以减少迭代次数。计算完成之后stream就会失效。

虽然大部分情况下stream是容器动用Collection.stream()方法得到的，但stream和collections有以下不同：
1. 无存储。stream不是一种数据结构，它只是某种数据结构的视图，数据源可以使一个数组，java容器或I/O channel等。
2. 为函数式编程而生，对stream的任何修改都不会修改背后的数据源，比如对stream执行过滤操作并不会删除被过滤的元素，而是会产生一个不包含被过滤元素的新的stream
3. 惰式执行。stream上的操作并不会立即执行，只有等到用户正宗需要结果的时候才会执行。
4. 可消费性 stream只能被消费一次，一旦遍历过就会失效，就像容器的迭代器那样，想要再次遍历必须重新生成。

java.util.Stream表示能应用在一组元素上一次执行的操作序列

java 8 扩展了集合类，可以通过Collection.stream()或则Collection.parallelStream()来创建一个Stream。Stream有串行和并行两种，串行Stream上的操作是在一个线程中一次完成的，而并行Stream则是在多个线程上同时执行的。

利用串行的Stream可以提升性能

**1.Stream方法的使用**

**获取Stream**
```
// 1、数组
    String[] arr = new String[]{"ab", "cd", "ef"};
    Stream<String> arrStream = Arrays.stream(arr);
    // 2、集合
    List<String> list = Arrays.asList("ab", "cd", "ef");
    Stream<String> colStream = list.stream();
    // 3、值
    Stream<String> stream = Stream.of("ab", "cd", "ef");
```

**Stream方法的使用**
1. forEach()使用该方法迭代流中的每个数据
```
  @Test
  public void testForEach(){
    // java 8 前
    System.out.println("java 8 前");
    for(User user: list){
      System.out.println(user);
    }
    // java 8 lambda
    System.out.println("java 8 lambda");
    list.forEach(user -> System.out.println(user));

    // java 8 stream lambda
    System.out.println("java 8 stream lambda");
    list.stream().forEach(user -> System.out.println(user));
```
2. sorted()使用该方法排序数据
排序是一个中间操作，返回的是排序好后的Stream。如果你不指定一个自定义的Comparator则会使用默认排序。
```
@Test
  public void testSort() {
    System.out.println("-----排序前-----");
    list.forEach(user -> System.out.println(user));
    System.out.println("-----排序后-----");
    // java 8 前
    System.out.println("java 8 前");
    Collections.sort(list, new Comparator<User>() {
      @Override
      public int compare(User o1, User o2) {
        return o1.getAge().compareTo(o2.getAge());
      }
    });
    for (User user : list) {
      System.out.println(user);
    }
    // java 8 stream 方法引用
    System.out.println("java 8 stream 方法引用");
    list.stream().sorted(Comparator.comparing(User::getAge)).forEach(user -> System.out.println(user));
  }
```
3. filter():使用该方法过滤
```
@Test
public void testFilter() {
  // 输出年龄大于50的人
  System.out.println("-----过滤前-----");
  list.forEach(user -> System.out.println(user));
  System.out.println("-----过滤后-----");
  // java 8 前
  System.out.println("java 8 前");
  for(User user: list){
    if (user.getAge() > 50) {
      System.out.println(user);
    }
  }
  // java 8 stream
  System.out.println("java 8 stream");
  list.stream().filter((User user) -> user.getAge() > 50).forEach(user -> System.out.println(user));
```
4. limit():使用该方法截断
```
@Test
  public void testLimit() {
    // 从第三个开始截断，只输出前三个
    System.out.println("-----截断前-----");
    list.forEach(user -> System.out.println(user));
    System.out.println("-----截断后-----");
    // java 8 前
    System.out.println("java 8 前");
    for (int i = 0; i < 3; i++) {
      System.out.println(list.get(i));
    }
    // java 8 stream
    System.out.println("java 8 stream");
    list.stream().limit(3).forEach(user -> System.out.println(user));
  }
```
5. ship():与limit互斥，使用该方法跳过元素
6. distinct():使用该方法去重，注意：必须重写对应泛型的hashCode()和equals()方法
7. map():接受一个方法作为参数，该函数会被应用到每个元素上，并将其映射成一个新的元素
```
@Test
  public void testMap() {
    // 只输出所有人的年龄
    list.stream().forEach(user -> System.out.println(user));
    System.out.println("映射后----->");
    List<Integer> ages = list.stream().map(user -> user.getAge()).collect(toList());
    ages.forEach(age -> System.out.println(age));

    // 小写转大写
    List<String> words = Arrays.asList("aaa", "vvvv", "cccc");
    System.out.println("全部大写---->");
    List<String> collect = words.stream().map(s -> s.toUpperCase()).collect(toList());
    collect.forEach(s -> System.out.println(s));
  }
  ```
8. flatMap():对每个元素执行mapper指定的操作，并将所有mapper返回的Stream中的元素组成一个新的Stream作为最终的返回结果，通俗易懂就是讲原来的stream中的所有元素都展开组成一个新的stream
9. reduce规约

这时一个最终操作，允许通过指定的函数来讲stream中的多个元素规约为一个元素，郭跃后的结果通过Optional接口表示
```
Optional<String> reduced =
    stringCollection
        .stream()
        .sorted()
        .reduce((s1, s2) -> s1 + "#" + s2);
reduced.ifPresent(System.out::println);
// "aaa1#aaa2#bbb1#bbb2#bbb3#ccc#ddd1#ddd2"

```
##   Date API
java 8在包java.time下包含了一组全新的时间日期API

**Clock时钟**
clock类提供了当前日期和时间的方法，clock是是时区敏感的

## java8 支持多重注解

####内部迭代与外部迭代的区别
使用for循环等利用Iterator进行迭代操作，叫做外部迭代，而使用stream流进行的迭代操作叫做内部迭代。内部迭代最明显的好处就是当数据凉很大的情况下，我们不需要对数据进行拆分，并可以调用指定的函数实现并行遍历。
1. 首先冲代码复杂度上看，外部迭代将业务条件跟具体遍历的代码混在一起，不如stream方式的迭代容易理解。
2. 外部迭代是串行的而且必须按照集合中的元素的顺序依次进行处理，集合框架无法对控制流进行优化，例如通过排序，并行，短路求值以及惰性求值改善性能。
3. 内部迭代：用户把对操作对象的控制权交还给类库，从而允许类库进行各种各样的优化（乱序执行，惰性求值和并行等等），内部迭代使得外部迭代中不可能实现的优化成为可能。



####使用函数式编程的好处
1. 减少了（中间）可变量的声明。
2. 能够更好的利用并行
3. 代码更加简洁和可读

**函数式接口**

函数式接口可以为接口添加新方法并且无须考虑向后兼容性问题

**Lambda表达式**

是一段没有函数名的函数体，可以作为参数直接传递给相关的调用者

**Lambda表达式和匿名内部类的区别**

使用匿名类与Lambda表达式的一大区别在于关键词的使用。对于匿名内部类，关键词this解读为匿名类，而对于Lambda表达式，关键词this解读为写就Lambda的外部类。
 Lambda表达式与匿名内部类的另一不同在于两者的变异方法。java编译器编译Lambda表达式并将它们转化为类里面的私有函数

 **接口和抽象类的相似性**

 1 接口和抽象类都不能被实例化，它们都位于继承树的顶端，用于被其他类实现和继承。

 2 接口和抽象类都可以包含抽象方法，实现接口或继承抽象类的普通子类都必须实现这些抽象方法。

**接口和抽象类的区别**

虽然java 8的接口的默认方法就像抽象类，能够提供方法的实现，但是他们俩仍然是不可相互替代的：
1. 接口里只能包含抽象方法，静态方法和默认方法，不能为普通方法提供方法实现，抽象类则完全可以包含普通方法
2. 接口里只能定义静态常量，不能定义普通成员变量，抽象类里则既可以定义普通成员变量，也可以定义静态常量。
3. 接口不能包含构造器，抽象类可以包含构造器，抽象类里的构造器并不是用于创建对象，而是让其子类调用这些构造器来完成属于抽象类的初始化操作。
4. 从设计理念上，接口反映的是“like-a”关系，抽象类反映的是“is-a”关系。
5. 接口里不能包含初始化块，但抽象类里完全可以包含初始化块。
6. 一个类最多只能有一个直接父类，包括抽象类，但一个类可以直接实现多个接口，通过实现多个接口可以弥补Java单继承不足


**函数式编程是指函数可以作为参数的编程方法，这种编程是以函数思维作为核心，在这种思维的角度去思考问题，这猴子那个编程最重要的基础是Lambda演算，接受函数当做输入和输出**
优点：

函数式编程：支持闭包和高阶函数，闭包是一种可以起函数的作用并可以如对象般操作的对象；而高阶函数是可以以另一个函数作为输入值来进行编程。支持惰性计算，这就可以在求值需要表达式的值得时候进行计算，而不是固定在变量时计算。还有就是可以用递归作为控制流程。函数式编程所编程出来的代码相对而言少很多，而且更加简洁明了。

缺点：
所有的数据都是不可以改变的，严重占据运行资源，导致运行速度也不够快。


**面向对象编程，这种编程是把问题看做由对象的属性和对象所进行的行为组成。基于对象的概念，以类作为对象的模板，把类和继承作为构造机制，以对象为中心，来思考并解决问题**

面向对象编程：面向对象有三个主要特征，分别是封装性、继承性和多态性。类的说明展现了封装性，类作为对象的模板，含有私有数据和公有数据，封装性能使数据更加安全依赖的就是类的特性，使得用户只能看到对象的外在特性，不能看到对象的内在属性，用户只能访问公有数据不能直接访问到私有数据。类的派生功能展现了继承性，继承性是子类共享父类的机制，但是由于封装性，继承性也只限于公有数据的继承（还有保护数据的继承），子类在继承的同时还可以进行派生。而多态性是指对象根据接收的信息作出的行为的多态，不同对象接收同一信息会形成多种行为。

缺点：为了编写可以重用的代码导致许多无用代码的产生，并且许多人为了面向对象而面向对象导致代码给后期维护带来很多麻烦。

**面向接口编程**

在一个面向对象的系统中，系统的各种功能是由许许多多的不同对象协作完成的。在这种情况下，各个对象内部是如何实现自己的，对系统设计人员来讲就不难么重要了；而各个对象之间的协作关系则成为系统设计的关键。小道不同类之间的通信，达到各个模块之间的交互，在系统设计之初都是要着重考虑的，这也是系统设计的主要内容。

接口：应是定义与实现的分离。
