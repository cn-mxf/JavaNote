## Java8实战

### 初始牛刀：删选绿苹果

```java
public static List<Apple> filetGreenApples (List<Apple> inventory){
    //累积苹果得列表
    ArrayList<Apple> objects = new ArrayList<>();
    for (Apple apple : inventory) {
        if(apple.getColor().equals("green")){//仅仅选出绿苹果
            objects.add(apple);
        }
    }
    return objects;
}
```

但是农民伯伯改变注意了，他还想要删选红苹果。应该怎么做？

简单得解决办法就是复制这个方法，把名字改为filterRedApples，然后更改if条件来匹配红苹果。然而农民伯伯想要删选更多得颜色，这种方法就应付不了了。一个良好的原则是在编写类似得代码后，尝试将其抽象化。

### 再展身手：将颜色作为参数

一种做法就是给方法添加一个参数，把颜色作为参数，这样就要可以灵活得变化了。

```java
ArrayList<Apple> objects = new ArrayList<>();
for (Apple apple : inventory) {
    if(apple.getColor().equals(color)){
        objects.add(apple);
    }
}
return objects;
```

现在只需要向下面一样调用方法，农民伯伯就饿很满意了：

```java
filetApplesByColor(inventory,"green");
filetApplesByColor(inventory,"red");
```

那这个时候农名伯伯又要能区分苹果得重量，重得苹果大于150g 这时候怎么办呢？

```java
public static List<Apple> filetApplesByWeight (List<Apple> inventory,Long weight){
    //累积苹果得列表
    ArrayList<Apple> objects = new ArrayList<>();
    for (Apple apple : inventory) {
        if(apple.getWeight()>weight){
            objects.add(apple);
        }
    }
    return objects;
}
```

上面代码解决方案是可以的，但是复制了大部分代码来实现遍历库存，并对每个苹果应用筛选条件。打破了DRY的软件工程原则。如果想改变筛选遍历方式来提升性能，那就要修改所有方法的实现，而不是只改一个。



### 第三次尝试：对能想到的属性做筛选

一种就是最笨拙的方式：把所有属性结合起来

```java
public static List<Apple> filetApples (List<Apple> inventory,Long weight,String color){
    //累积苹果得列表
    ArrayList<Apple> objects = new ArrayList<>();
    for (Apple apple : inventory) {
        if(apple.getWeight()>weight && apple.getColor().equals(color)){
            objects.add(apple);
        }
    }
    return objects;
}
```

这个解决方案还是没有能够很好的应对变化的需求。	



### 行为参数化

需要一种比添加很多参数更好的解决办法来应对变化的需求，比如：对选择标准建模，考虑的是苹果，需要根据Apple的某些属性（颜色是绿色吗？，重量大于150g吗？）来返回一个Boolean值。我们把它称为谓词（就是一个返回Boolean的函数）。

首先定义一个接口：

```java
public interface ApplePredicate {

    boolean test (Apple apple);
}
```

现在就可以根据ApplePredicate的不多个实现来代表不同的选择标准了

```java
public class ApplePredicateColor implements ApplePredicate {
    @Override
    public boolean test(Apple apple) {
        return apple.getColor().equals("green");

    }
}
public class ApplePredicateWeight implements ApplePredicate {
    @Override
    public boolean test(Apple apple) {
        return apple.getWeight()>150;

    }
}
```

![img](https://raw.githubusercontent.com/cn-mxf/picture/main/img/1685599573421-797149f3-e0b4-40e8-a369-4c3d07155f12.png)

可以把这些标准看作filter方法的不同行为，定义一族算法，把他们封装起来（称为策略），然后再运行时选择一个算法。算法族就是 ApplePredicate，不同的策略就是ApplePredicateColor和ApplePredicateWeight。

 但是，该怎么利用Apple Predicate的不同实现呢? 需要filterApples方法接受Apple Predicate对象，对Apple做条件测试，这就是行为参数化：让方法接受多种行为作为参数，并在内部使用，来完成不同的行为。

**第四次尝试：根据抽象条件筛选**

利用ApplePredicate改过之后，filter方法是这样的：

```java
public static List<Apple> filetApples (List<Apple> inventory,ApplePredicate applePredicate){
    //累积苹果得列表
    ArrayList<Apple> objects = new ArrayList<>();
    for (Apple apple : inventory) {
        if(applePredicate.test(apple)){ //谓词对象封装了测试苹果的条件
            objects.add(apple);
        }
    }
    return objects;
}
```

庆祝一下，这样的话代码就灵活多了，读起来用起来也很方便！现在只需要创建不同的Apple Predicate对象，并将它们传递给filter Apples方法。比如，如果需要找出所有重量超过150g的红苹果，只需要创建一个类来实现ApplePredicate就行了。代码足够灵活，可以应对任何涉及苹果属性的需求变更了：

```java
public class AppleRedAndHeavyPredicate implements ApplePredicate {
    @Override
    public boolean test(Apple apple) {
        return apple.getWeight()>150 && apple.getColor().equals("red");
    }
}

 public static List<Apple> filterApples (List<Apple> inventory, AppleRedAndHeavyPredicate appleRedAndHeavyPredicate){
     
 }
**filterApples方法的行为取决于你通过ApplePredicate对象传递的代码。也就是说把 filterApples方法的行为参数化了。**
```

但是，由于该filterApples方法只能接受对象，所以必须把代码包裹在ApplePredicate对象里。通过一个实现了test方法的对象来传递布尔表达式的。

![img](https://raw.githubusercontent.com/cn-mxf/picture/main/img/1685607256796-26dc5bdb-25ca-47be-8202-97a925340b93.png)

![img](https://raw.githubusercontent.com/cn-mxf/picture/main/img/1685608896144-87e1ec3a-1799-4206-8dba-f960dc0eaeb9.png)

### 使用匿名内部类

下面的代码显示了如何通过创建一个匿名内部类实现ApplePredicate的对象，重写筛选的例子;

```java
filterApples(inventory, new ApplePredicate() {
    @Override
    public boolean test(Apple apple) {
        return apple.getWeight()>150;
    }
});
```

使用匿名内部类的缺点：往往很笨重，占用了很多空间。用起来让人费解



### 使用Lambda表达式

```java
 List<Apple> apples = filterApples(inventory, (Apple apple) -> apple.getColor().equals("red"));
```

代码看上去干净很多，看起来更像问题陈述本身了，已经解决了啰嗦的问题。



### 将List类型抽象化

目前，filterApples方法还是只适用于Apple。可以将List类型抽象化，从而超越你眼前要处理的问题。

```java
public interface Predicate<T> {
    boolean test (T t);
}

public  static <T>List<T> filter(List<T>list , Predicate<T> p){
        ArrayList<T> objects = new ArrayList<>();
        for (T t : list) {
            if(p.test(t)){
                objects.add(t);
            }
        }
        return objects;
    }
```

现在可以把filter方法用在香蕉、橘子、Integer或String的列表上了。下面有一个例子。

![img](https://raw.githubusercontent.com/cn-mxf/picture/main/img/1685611188447-8b8b2ada-d811-4f2c-a65f-4e05b73e636e.png)



## 真实的例子







## 小结

![img](https://raw.githubusercontent.com/cn-mxf/picture/main/img/1685611707487-d34341c5-f88c-4b30-9fcd-f48c951fbc36.png)