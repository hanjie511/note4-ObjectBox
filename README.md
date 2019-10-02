# note4-ObjectBox
## What’s in the (Object)Box?
ObjectBox是一种专为移动的和物联网的设备而设计的一种数据库和异步解决方案。它的诞生为我们的微型设备带来了边缘计算，进而使得我们的微型设备在存取数据时的速度比其在原生的数据库上存取数据的速度快了近10倍。ObjectBox的大小不超过1MB,它的部署范围可以从最小的传感器到我们的手机，智能穿戴设备，物联网设备，甚至可以在我们的服务器上使用。ObjectBox的平台支持性非常的出色，它可以在Linux, Windows, Mac/iOS, Android, Raspbian等系统平台上运行。ObjectBox还是一款ORM(对象关系映射)数据库。
## ObjectBox在Android设备上的运用
 本次，我将采用ObjectBox来代替Android的原生数据库SQLite.我为什么要使用ObjectBox呢？<br>首先SQLite不是一款ORM(对象关系映射)的数据库，这就使得我们在日常开发中在对数据库读写时，还要我们单独的将查出来的数据一条条的映射成与之相匹配的关系数据类型。而ObjectBox就直接将查询到的数据自动映射为一个个的数据实体类，它帮助我们省去了数据关系映射的这一环节。ObjectBox就这样提升了我们的开发效率。<br>  其次，SQLite要求使用者在使用的过程中每创建一个数据库或表都要求在其数据库操作类中声明，而ObjectBox只要求我们建立一个数据实体类。<br> SQLite的增删改查操作的sql语句都要求开发人员自己编写，这一做法无疑的增加了我们的开发负担，并且，大量的sql语句也不利于后期对程序的修改。而ObjectBox简洁的封装了所有的对数据库操作方法，只要我们需要，调用就行了。
## 在Android开发中的使用
### 将project对应的build.gradle文件中的内容改写为如下形式：
```java
buildscript {
    ext.objectboxVersion = '2.3.4'
    repositories {
        jcenter()
    }
    dependencies {
        // Android Gradle Plugin 3.0.0 or later supported
        classpath 'com.android.tools.build:gradle:3.3.2'
        classpath "io.objectbox:objectbox-gradle-plugin:$objectboxVersion"
    }
}
```
### 将app对应的build.gradle文件中添加如下内容：
```java
apply plugin: 'com.android.application'
apply plugin: 'io.objectbox' // Apply last.
```
### 构建实体类：
比如我们构建一个User实体类<br>
```java
// User.java
@Entity
public class User {
    @Id 
    public long id;
    public String name;
    public int age;
    public String phoneNumber;
    //getter and setter....
}
```

上面的这一个User实体类就相当于我们建立一张名称为User的数据表，其中的id,name,age,phoneNumber就相当于表中的字段。每一个实体类必须有一个@Entity的注解，如果没有它系统就不知道他是一个实体类，每一个实体类必须有一个为long类型的id,且必须要在它前面加上@Id的注解。
### build your project 
当我们把所有的实体类都建好了或则是，我们修改了一些实体类中信息后，我们都要重新构建我们的工程； using Build > Make Project in Android Studio.我们在重构好我们的工程后就以开始实例化我们的
### Initialization
初始化我们的MyObjectBox、BoxStore和Box对象，官方对他们的说明如下：<br>
* MyObjectBox: Generated based on your entity classes, MyObjectBox supplies a builder to set up a BoxStore for your app.
* BoxStore: The entry point for using ObjectBox. BoxStore is your direct interface to the database and manages Boxes.
* Box: A box persists and queries for entities. For each entity, there is a Box (supplied by BoxStore).
上面的MyObjectBox在我们建立好 我们的实体类，并Build我们的工程的时候，系统已经为我们自动生成了，它不需要我们对他进行实例化。我们只需要初始化BoxStore和Box这两个对象。
#### 初始化BoxStore对象
官方建议我们在Application中初始化，因为这样我们只需要一次实例化该对象后，他就可以在整个应用中被调用：<br>
```java
public class MyApplication extends Application {
    private static BoxStore boxStore;
    @Override
    public void onCreate() {
        super.onCreate();
        init(this);
    }
    public static void init(Context context) {
        boxStore = MyObjectBox.builder()
                .androidContext(context.getApplicationContext())
                .build();
    }
    public static BoxStore getBoxStore() { return boxStore; }
}
```
好了，boxStore对象我们就初始化完成了，接下来，我们只需要在使用它时，调用它就行了。
### 对数据表进行增删改查：
如果我们要对数据表进行操作，那我们就要实例化Box对象，因为该对象提供了对数据库的所有操作的方法。官方说明如下：<br>
These are some of the operations offered by the Box class:<br>
* put: Inserts a new or updates an existing object with the same ID. When inserting and put returns, an ID will be assigned to the just  inserted object (this will be explained below). put also supports putting multiple objects, which is more efficient.
* get and getAll: Given an object’s ID reads it back from its box. To get all objects in the box use getAll .
* remove and removeAll: Remove a previously put object from its box (deletes it). remove also supports removing multiple objects, which is more efficient. removeAll  removes (deletes) all objects in a box.
* count: Returns the number of objects stored in this box.
* query: Starts building a query to return objects from the box that match certain conditions. See queries for details.<br>
我们可以从上面得知：put()方法为我们需要的添加和修改操作，get()方法为我们需要的取数据的操作，remove()方法为我们的删除数据的操作，query()方法为我们的查询数据的操作。
### 使用示范：
```java
Box<User> userBox = MyApplication.getBoxStore().boxFor(User.class);//实例化Box对象，Box对象操作的表为我们上面声明的User实体类，即User表
        User user1 = new User();//新建四个User对象，模拟向User表中插入4条数据
        user1.setAge("13");
        user1.setName("张三");
        User user2 = new User();
        user2.setAge("15");
        user2.setName("李四");
        User user3 = new User();
        user3.setAge("15");
        user3.setName("王五");
        User user4 = new User();
        user4.setAge("17");
        user4.setName("李四");
        userBox.put(user1);//插入数据的操作，注意：ObjectBox中的表的id字段是他自己生成的，若我们没有特殊要求。
        userBox.put(user2);
        userBox.put(user3);
        userBox.put(user4);
        List<User> list=userBox.query().equal(User_.name,"李四").and().equal(User_.age,"17").build().find();//查询姓名为‘张三’且年龄是‘17’岁的用户信息。
```
#### 好了，关于ObjectBox的入门指引就到这里了，如果想要进行进一步的了解和学习请关注下面的网址：
#### [对查询语句的进一步学习](https://docs.objectbox.io/queries)
#### [官方的JAVA API文档](https://objectbox.io/files/objectbox-java/current/overview-summary.html)

