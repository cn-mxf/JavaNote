# **1.Lambda表达式**

## **1.1使用匿名内部类存在的问题**

当需要启动一个线程去完成任务时，通常会通过 **Runnable** 接口来定义任务内容，并使用 **Thread** 类来启动该线程,传统代码如下：

匿名内部类做了哪些事情？

1.定义了一个没有名字的类

2.这个类实现了Runnable接口

3.创建了这个类的对象。

```plain
@Test
public void testLambda(){
    new Thread(new Runnable() {
        @Override
        public void run() {
            System.out.println("新线程代码执行了");
        }
    }).start();
```

由于面向对象的语法要求，首先创建一个 Runnable 接口的匿名内部类对象来指定线程要执行的任务内容，再将其交

给一个线程来启动。

**代码分析：**

对于 Runnable 的匿名内部类用法，可以分析出几点内容：

- Thread 类需要 Runnable 接口作为参数，其中的抽象 run 方法是用来指定线程任务内容的核心
- 为了指定 run 的方法体，**不得不**需要 Runnable 接口的实现类
- 为了省去定义一个 Runnable 实现类的麻烦，**不得不**使用匿名内部类
- 必须覆盖重写抽象 run 方法，所以方法名称、方法参数、方法返回值**不得不**再写一遍，且不能写错
- 而实际上，**似乎只有方法体才是关键所在**。

## 1.2**Lambda体验**

Lambda是一个**匿名函数**，可以理解为一段可以传递的代码。代码如下：

```plain
new Thread(()->{
    System.out.println("lambda表达式执行");
}).start();
```

只需要将代码放到一个lambda表达式中，不需要定义类，不需要创建对象。

## 1.3**Lambda表达式优点**

简化匿名内部类的使用，语法更加简单。

## 1.4Lambda表达式的标准格式

Lambda省去面向对象的条条框框，Lambda的标准格式格式由**3\****个部分**组成：

```plain
(参数类型 参数名称) -> {
    代码体;
}
```

**格式说明：

- (参数类型 参数名称)：参数列表
- {代码体;}：方法体
- -> ：箭头，分隔参数列表和方法体

*以后看到方法的参数是接口就可以考虑用Lambda表达式。可以理解为Lambda表达式就是对接口中抽象方法的重写。只需要关注抽象列表。* 	

## 1.5小结

- 了解了Lambda的标准格式

```plain
(参数列表)->{

}
```

- 以后看到调用的方法他的**参数是接口**就可以考虑使用Lambda表达式来替代匿名内部类，**但并不是所有匿名内部类都可以使用Lambda来替换。**
- Lambda表达式相当于是对接口抽象方法的重写

![img](https://raw.githubusercontent.com/cn-mxf/picture/main/img/1675164344319-bf05c45d-58ab-4d02-92a1-f63fd5ff719a.png)

# 2.Lambda表达式单独省略格式

在Lambda标准格式的基础上，使用省略写法的规则为：

1. 小括号内参数的类型可以省略
2. 如果小括号内**有且仅有一个参数**，则小括号可以省略
3. 如果大括号内**有且仅有一个语句**，可以同时省略大括号、return关键字及语句分号

## 函数式接口

函数式接口在Java中是指：**有且仅有一个抽象方法的接口**。

@FunctionalInterface注解 ：只有一个抽象方法的接口称为函数式接口，就可以使用Lambda表达式。

![img](https://cdn.nlark.com/yuque/0/2023/png/29443350/1675164344229-4c958834-b8be-4a62-835a-0b7b97468d75.png)

如果超过一个接口，注解就报错。

![img](https://raw.githubusercontent.com/cn-mxf/picture/main/img/1675164344235-7773660a-d448-4a29-b9ee-4d13690f28c6.png)

## 小结

Lambda表达式的前提条件:

1. 方法的参数或变量的类型是接口
2. 这个接口中只能有一个抽象方法

# 3.Lambda和匿名内部类对比

1. 所需类型不一样

匿名内部类，需要的类型可以是类，抽象类，接口。

Lambda表达式，需要的接口必须是接口。

​        2.抽象方法的数量不一样

匿名内部类所需的接口中抽象发放的数量随意。

Lambda表达式所需的接口只能有一个抽象方法。

3.实现原理不同

匿名内部类是在编译后会形成class

Lambda表达式实在程序运行时动态生成class。

## 小结

当接口中只有一个**抽象方法**时,建议使用Lambda表达式,其他其他情况还是需要使用匿名内部类

# 4.JDK 8接口新增的两个方法

## 4.1JDK8接口增强介绍

### 接口默认方法

JDK8以前的接口：

```plain
interfcace 接口名{
    静态常量；
    抽象方法；
}
```

JDK 8对接口的增强，接口还可以有**默认方法**和**静态方法**

```plain
interfcace 接口名{
    静态常量；
    抽象方法；
    默认方法；
    静态方法；
}
```

​	**在JDK8之前只能有抽象方法，存在问题。**

如果给接口新增抽象方法，所有实现类都必须重写这个抽象方法。不利于接口的扩展。

比如jdk8在Map接口 新增了foreach 方法，但是它的实现类有几十个，如果在Map接口中增加一个抽象方法，所有的实现类都需要去实现这个方法，那么工程量时巨大的。

![img](https://raw.githubusercontent.com/cn-mxf/picture/main/img/1675164344679-61de3d5e-aabf-4214-b7bf-7309e75229ca.png)

因此，在JDK 8时为接口新增了默认方法，效果如下：

![img](https://raw.githubusercontent.com/cn-mxf/picture/main/img/1675164344372-82dbbd51-2b38-4e37-b101-ec91ff1a81d5.png)

接口中的默认方法实现类不必重写，可以直接使用，实现类也可以根据需要重写。这样就方便接口的扩展。

代码示例：

```plain
package com.itheima.demo02interfaceupgrade;

public class Demo02UseDefaultFunction {
    public static void main(String[] args) {
        BB bb = new BB();
        bb.test01();

        CC cc = new CC();
        cc.test01();
    }
}

interface AA {
    public default void test01() {
        System.out.println("我是接口AA默认方法");
    }
}
// 默认方法使用方式一: 实现类可以直接使用
class BB implements AA {
}

// 默认方法使用方式二: 实现类可以重写接口默认方法
class CC implements AA {
    @Override
    public void test01() {
        System.out.println("我是CC类重写的默认方法");
    }
}
```

### 接口静态方法

格式：

```plain
interface 接口名 {
修饰符 static 返回值类型 方法名{
   代码;
  }
}
```

代码示例；

```plain
package com.itheima.demo02interfaceupgrade;

public class Demo03UseStaticFunction {
    public static void main(String[] args) {
        BBB bbb = new BBB();
        // bbb.test01();
        // 使用接口名.静态方法名();
        AAA.test01();
    }
}

interface AAA {
    public static void test01() {
        System.out.println("我是接口静态方法");
    }
}

class BBB implements AAA {
    /*@Override
    public static void test01() {
        System.out.println("我是接口静态方法");
    }*/
}
```

## **接口默认方法和静态方法的区别**

1. 默认方法通过实例调用，静态方法通过接口名调用。
2. 默认方法可以被继承，实现类可以直接使用接口默认方法，也可以重写接口默认方法。
3. 静态方法不能被继承，实现类不能重写接口静态方法，只能使用接口名调用。

## **小结**

接口中新增的两种方法：

默认方法和静态方法

如何选择呢？如果这个方法需要被实现类继承或重写，使用默认方法，如果接口中的方法不需要被继承就使用静态方

法