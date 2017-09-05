---
layout: post
title: "Java中Object类的equals()和hashCode()"
date: 2017-09-04
categories: Java
tags: Java
---
* content
{:toc}

## 1. 说一说对Object对象中hashCode和equals方法的理解
```
  public native int hashCode();
  public boolean equals(Object obj) {
    return (this == obj);
  }
```
### hashCode()
从定义可以看出，hashCode()是一个native方法，也就是它的具体实现不在java中，而是在操作系统的c/c++中。
默认的hashCode方法会利用对象的地址来计算hashcode值，也就是说，不同对象的hashcode值是不一样的（即使对象类型相同、属性值完全相同）。

**hashCode()方法应该满足的基本原则：**
  * java应用的同一次执行过程中，在对象上 equals 比较中所用的信息没有被修改的前提下，多次调用相同对象的hashCode()必须得到相同的hashCode。
  上一次应用执行与下一次应用执行中的产生的hashCode值不要求保持一致。
  * 如果obj1.equals(obj2)判定两个对象相等，那么obj1.hashCode()必须等于obj2.hashCode()。
  * 如果obj1.equals(obj2)判定两个对象不相等，那么obj1.hashCode()与obj2.hashCode()的值可以相等，也可以不相等。
  当然，程序员应该注意到不相等的对象产生不相等的hashCode会提升hash表的性能（比如HashMap中使用到的）。

### equals()
从源码中可以看出Object类中的equals方法与“==”是等价的，也就是说判断对象的地址是否相等。
也就是说，<font color='red'>Object类中的equals方法进行的是基于内存地址的比较，而不是基于内容的比较。</font>
默认行为不符合编程的需要，所以在自定义类中需要重写equals方法，使得其基于内容来比较。


## 2. Java中重写Object类的equals方法和Hashcode方法的注意事项

### 在重写任何类的equals方法是必须遵循以下几点

1. **对称性**：如果x.equals(y)返回是“true”，那么y.equals(x)也应该返回是“true”。

2. **反射性（自反性）**：x.equals(x)必须返回是“true”。

3. **类推性（传递性）**：如果x.equals(y)返回是“true”，而且y.equals(z)返回是“true”，那么z.equals(x)也应该返回是“true”。

4. **一致性**：如果x.equals(y)返回是“true”，只要x和y内容一直不变，不管你重复x.equals(y)多少次，返回都是“true”。

5. 任何情况下，x.equals(null)，永远返回是“false”；

### 在重写任何类得hashcode方法是必须遵循以下几点

1. 在Java应用的同一次执行过程中，同一对象被多次调用，则他们的hashcode值必然相同。
而对于同一个应用的两次不同的调用，它们的Hashcode值可以相同，也有可能不同。

2. 对于两个对象来说，如果他们的equals方法比较返回true，那么这两个对象的hashcode必然相同。
这也解释了为什么String类中，如果两个对象的equals方法相同，则他们的hashcode值一定相同。

3. 对于两个对象来说，如果使用equals方法返回为false，则他们的hashcode的值有可能相等也可能不等，
（如果不同会提高性能，因为在集合中类判断两个对象是否相等，如果其hashcode不等就直接不用判断equals方法了）

4. 对于Object对象来说，不同的Object对象的hashcode是不同的，它们返回的是对象的地址，equals返回的也是对象的地址。
所以在自己定义的类中如果要添加到集合对象中，最好是要重写hashcode和equals方法，不然会自动继承自Object类中的两个方法根据对象地址来判断。
在重写自己定义的类时，通常是在类中的根据某个值如name.hashcode();来进行判断。

## 3. 如何正确的重写equals() 和 hashCode()方法
**POJO - User.java**
```
public class User {
    private String name;
    private int age;
    private String passport;

    //getters and setters, constructor
}
```
**1. 经典方式**  
这种17和31散列码的想法来自经典的Java书籍——《Effective Java》第九条。下面我们来看看是如何实现的...

```
public class User {  
    private String name;  
    private int age;  
    private String passport;  
    //getters and setters, constructor  
    @Override  
    public boolean equals(Object o) {  
        if (o == this) return true;  
        if (!(o instanceof User)) {  
            return false;  
        }  
        User user = (User) o;  
        return user.name.equals(name) &&  
                user.age == age &&  
                user.passport.equals(passport);  
    }  
    //Idea from effective Java : Item 9  
    @Override  
    public int hashCode() {  
        int result = 17;  
        result = 31 * result + name.hashCode();  
        result = 31 * result + age;  
        result = 31 * result + passport.hashCode();  
        return result;  
    }  
}

```

**2. JDK 7**  
对于JDK7及更新版本，你可以是使用java.util.Objects 来重写 equals 和 hashCode 方法，代码如下
```
import java.util.Objects;

public class User {
    private String name;
    private int age;
    private String passport;

    //getters and setters, constructor

    @Override
    public boolean equals(Object o) {
        if (o == this) return true;
        if (!(o instanceof User)) {
            return false;
        }
        User user = (User) o;
        return age == user.age &&
                Objects.equals(name, user.name) &&
                Objects.equals(passport, user.passport);
    }

    @Override
    public int hashCode() {
        return Objects.hash(name, age, passport);
    }

}
```

**3. Apache Commons Lang**  
或者,您可以使用Apache Commons LangEqualsBuilder 和HashCodeBuilder 方法。代码如下
```
import org.apache.commons.lang3.builder;

public class User {
    private String name;
    private int age;
    private String passport;
    //getters and setters, constructor

     @Override
    public boolean equals(Object o) {
        if (o == this) return true;
        if (!(o instanceof User)) {
            return false;
        }
        User user = (User) o;

        return new EqualsBuilder()
                .append(age, user.age)
                .append(name, user.name)
                .append(passport, user.passport)
                .isEquals();
    }
    @Override
    public int hashCode() {
        return new HashCodeBuilder(17, 37)
                .append(name)
                .append(age)
                .append(passport)
                .toHashCode();
    }
}
```
**重写equals的建议**
* 首先检测 this 与 otherObject 是否引用同一对象
```
if(this==otherObject) return true;
```  
* 然后检测 otherObject是否为 null，如果为 null 返回 false，这是必须的
```
if(otherObject == null) return false;
```
* 其次比较 this 与 otherObject 是否同属于一个类；如果 equals 语义在子类中有所改变，则 使用 getClass 检测
```
if(this.getClass()!=otherObject.getClass()) return false;
```
* 最后将 otherObject 强制转换为当前类型，并进行属性值检测；
  注意：<font color='red'>如果在子类中重写的equals，则需要在重写时首先进行 super.equals(other) 判断</font>



参考：  
[重写equal()时为什么也得重写hashCode()之深度解读equal方法与hashCode方法渊源](http://blog.csdn.net/javazejian/article/details/51348320)  
[浅谈 java中的 equals 和 hashcode](https://mritd.me/2016/02/02/%E6%B5%85%E8%B0%88-java%E4%B8%AD%E7%9A%84-equals-%E5%92%8C-hashcode/)
