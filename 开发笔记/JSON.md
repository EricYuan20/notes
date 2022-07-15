### 1、 pojo 转 Json 字符串

```java
String s1 = JSON.toJSONString(user);
```

### 2、 json字符串 转 json 对象

```java
JSONObject jsonObject = JSONObject.parseObject(s1);

Object id = jsonObject.get("id"); //拿到id
System.out.println(id);//1
System.out.println("json对象转json字符串 "+jsonObject.toJSONString());
```

### 3、 json字符串 转 pojo 对象

```java
User user1 = JSON.parseObject(s1, User.class);
System.out.println(user1.getName());
```

### 4、 json字符串 转 list 对象

```java
List<User> users = JSONArray.parseArray(s1, User.class);
users.forEach(obj-> System.out.println(obj.toString()));
```

### 5、 list转json字符串

```java
Object toJSON = JSON.toJSON(list);
System.out.println("list-->jsonStr"+toJSON);
```

### 6、 json字符串 转 Map

```java
String str = "{\"1\":\"zs\",\"2\":\"ls\",\"4\":\" ww\",\"5\":\"ml\"}";
//第一种
Map maps = (Map) JSON.parse(str);
System.out.println("第1种");
for (Object obj : maps.keySet()) {
    System.out.println("key：" + obj + "value：" + maps.get(obj));
}

//第二种
Map mapTypes = JSON.parseObject(str);
System.out.println("第2种");
for (Object obj : mapTypes.keySet()) {
    System.out.println("key：" + obj + "value：" + mapTypes.get(obj));
}

//第三种
Map mapType = JSON.parseObject(str, Map.class);
System.out.println("第3种");
for (Object obj : mapType.keySet()) {
    System.out.println("key：" + obj + "value：" + mapTypes.get(obj));
}

//第四种
/**
     * JSONObject是Map接口的一个实现类
     */
Map json = (Map) JSONObject.parse(str);

//第五种
Map jsonMap = (Map) JSONObject.parseObject(str);
```