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
上面的这一个User实体类就相当于我们建立一张名称为User的数据表，其中的id,name,age,phoneNumber就相当于表中的字段。每一个实体类必须有一个
