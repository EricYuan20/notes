## **JAVA** **反射**

#### 获取class对象的3种方式

调用某个对象的getClass()方法

```diao
Person p = new Person();
Class class=p.getClass();
```

调用某个类的class属性来获取该类对应的class对象

```java
Class clazz = Person.class;
```

使用Class类中的forName()静态方法（最安全/性能最好）

```java
Class clazz = Class.froName("类的全路径");//最常用
```

#### **创建对象的两种方法**

**Class** **对象的** **newInstance()**

1. 使用 Class 对象的 newInstance()方法来创建该 Class 对象对应类的实例，但是这种方法要求

该 Class 对象对应的类有默认的空构造器。

**调用** **Constructor** **对象的** **newInstance()**

2. 先使用 Class 对象获取指定的 Constructor 对象，再调用 Constructor 对象的 newInstance()

方法来创建 Class 对象对应类的实例,通过这种方法可以选定构造方法创建实例。

```java
//获取 Person 类的 Class 对象
Class clazz=Class.forName("reflection.Person"); 
//使用.newInstane 方法创建对象
Person p=(Person) clazz.newInstance();
//获取构造方法并创建对象
Constructor c=clazz.getDeclaredConstructor(String.class,String.class,int.class);
//创建对象并设置属性
Person p1=(Person) c.newInstance("李四","男",20);
```

