---
title: Integer详解
date: 2022-05-03
keywords: Integer
description: Integer
tags:
  - JAVA基础
categories:
  - JAVA基础
---

将 Integer对象i和j进行互换：

```java
public class IntegetTest {

  public static void main(String[] args) {
      Integer i=1,j=2;
      swap(i,j);
      System.out.println("i="+i+",j="+j);
  }

  private static void swap(Integer i,Integer j){
    Integer tmp=j;
    j=i;
    i=tmp;
  }
}

```


以上是正常互换情形，但是输出结果却是：i=1,j=2
未发生互换。

解读：
我们输入，Integer i=1；实际上的操作是 Integer i=Integer.value(1);
此时我们可以看到Integer.value方法如下：

```java
public static Integer valueOf(int i) {
    if (i >= IntegerCache.low && i <= IntegerCache.high)
        return IntegerCache.cache[i + (-IntegerCache.low)];
    return new Integer(i);
}

```


当i>= IntegerCache.low即（-128） && i <= IntegerCache.high（127）
是从 IntegerCache.cache对应的下标获取值。即 -128到127存放在cache下标为：0到256的位置上。
所以，我们在进行 

```java
 private static void swap(Integer i,Integer j){
    Integer tmp=j;
    j=i;
    i=tmp;
  }
```
  

操作时，实际上是获取的Integer.cache中的对应的下标值，所以并没有发生变化。
由此可以发现：
在-128到127之间，我们进行互换操作时，不受影响，但是其他的值会发生变化。

```java
private static void swap(Integer i,Integer j) throws NoSuchFieldException, IllegalAccessException {
  Field value = i.getClass().getDeclaredField("value");
  value.setAccessible(true);
  int tmp=i;
  value.set(i,j);
  System.out.println(tmp);
  value.set(j, new Integer(tmp));
}


```

new Integer(tmp)的时候才会把int类型的值传递给Integer中的value属性。

反射：
 value.setAccessible(true);
会设置override属性为true

```java
private static void setAccessible0(AccessibleObject obj, boolean flag)
    throws SecurityException
{
    if (obj instanceof Constructor && flag == true) {
        Constructor<?> c = (Constructor<?>)obj;
        if (c.getDeclaringClass() == Class.class) {
            throw new SecurityException("Cannot make a java.lang.Class" +
                                        " constructor accessible");
        }
    }
    obj.override = flag;
}

public void set(Object obj, Object value)
    throws IllegalArgumentException, IllegalAccessException
{
    if (!override) {
        if (!Reflection.quickCheckMemberAccess(clazz, modifiers)) {
            Class<?> caller = Reflection.getCallerClass();
            checkAccess(caller, clazz, obj, modifiers);
        }
    }
    getFieldAccessor(obj).set(obj, value);
}

```


我们在调用Set方法时候可以看到会先判断override属性，如果是true的话才允许设置。

