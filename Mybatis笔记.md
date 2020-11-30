# Mybatis笔记

## 一、简介

### 1.1 什么是mybatis

>MyBatis 是一款优秀的`持久层`框架，它支持自定义 SQL、存储过程以及高级映射。
>
>MyBatis 免除了几乎所有的 JDBC 代码以及设置参数和获取结果集的工作。
>
>MyBatis 可以通过简单的 XML 或注解来配置和映射原始类型、接口和 `Java POJO`为数据库中的记录。

**持久层框架：**所谓“持久”就是将数据保存到可掉电式存储设备中以便今后使用，简单的说，就是将内存中的数据保存到关系型数据库、文件系统、消息队列等提供持久化支持的设备中。持久层就是系统中专注于实现数据持久化的相对独立的层面。

>**常见的持久层框架**
>
>- \-Hibernate 
>- \- MyBatis 
>- \- TopLink 
>- \- Guzz 
>- \- JOOQ 
>- \- Spring Data 
>- \- ActiveJDBC

### 1.2 Mybatis的历史

MyBatis项目继承自`iBATIS3.0`，其维护团队也包含iBits的初创成员。

2010年5月19日项目创建。当时Apache iBATIS 3.0发布，其开发团队宣布会在新的名字、新的站点中继续开发

2013年11月10日，项目迁移到了[GitHub](https://github.com/mybatis/mybatis-3)

### 1.3 为什么要使用Mybatis框架

#### 1.3.1 传统方式JDBC存在的问题

>1、使用JDBC访问数据库有大量重复代码（比如注册驱动、获取连接、获取传输器、释放资源等）；
>
>2、JDBC自身没有连接池，会频繁的创建连接和关闭连接，效率低； 
>
>3、SQL是写死在程序中，一旦修改SQL，需要对类重新编译； 
>
>4、对查询SQL执行后返回的ResultSet对象，需要手动处理，有时会特别麻烦；

#### 1.3.2 使用Mybatis的好处

> 1、Mybatis对JDBC对了封装，可以简化JDBC代码； 
>
> 2、Mybatis自身支持连接池（也可以配置其他的连接池），因此可以提高程序的效率；
>
>  3、Mybatis是将SQL配置在mapper文件中，修改SQL只是修改配置文件，类不需要重新编译。
>
>  4、对查询SQL执行后返回的ResultSet对象，Mybatis会帮我们处理，转换成Java对象。

## 二、使用

### 2.1 搭建第一个Mybatis项目

下载[最新版`Jar`包，](https://github.com/mybatis/mybatis-3/releases)以及`MySQL_Jar包`放置在项目中，并添加到项目的构建路径；maven项目即添加依赖置于 pom.xml：

```xml
<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis</artifactId>
    <version>x.x.x</version>
</dependency>
```

新建资源文件夹`resources`文件夹，在文件夹中新建mybatis的主配置文件`mybatis-config.xml`，拷贝官网xml文件到其中：

![mybaits-01](https://image.icelo.cn/mybatis-01.png)

**主配置文件**：包含了对 MyBatis 系统的核心设置，包括获取数据库连接实例的数据源（DataSource）以及决定事务作用域和控制方式的事务管理器

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
  PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    	<!-----数据库环境---->
  <environments default="development">
    <environment id="development">
        	<!----事务类型--->
      <transactionManager type="JDBC"/>
        	<!----数据库连接池类型--->
      <dataSource type="POOLED">
        		<!---数据库驱动类型---->
        <property name="driver" value="${driver}"/>
          			<!---数据库连接url--->
        <property name="url" value="${url}"/>
          				<!---数据库连接用户名--->
        <property name="username" value="${username}"/>
          					<!---数据库连接密码--->
        <property name="password" value="${password}"/>
      </dataSource>
    </environment>
  </environments>
  <mappers>
    <mapper resource="org/mybatis/example/BlogMapper.xml"/>
  </mappers>
</configuration>
```

#### 2.1.1 配置文件结构

> * configuration（配置）
>   * [properties（属性）](#4.1 properties)
>   * [settings（设置）](#4.2 settings)
>   * [typeAliases（类型别名）](#4.3 typeAliases )
>   * typeHandlers（类型处理器）
>   * objectFactory（对象工厂)
>   * plugins（插件）
>   * environments（环境配置）
>     - environment（环境变量）
>       - transactionManager（事务管理器）
>       - dataSource（数据源）
>   * databaseIdProvider（数据库厂商标识）
>   * [mappers（映射器）](#4.4 mappers)

#### 2.1.2 修改主配置文件配置

```xml
<property name="driver" value="com.mysql.jdbc.Driver"/>
<property name="url" value="jdbc:mysql://localhost:3306/mybatis?useUnicode=true&amp;characterEncoding=utf-8&amp;useSSL=false&amp;serverTimezone = GMT"/>
<property name="username" value="root"/>
<property name="password" value="root"/>
```

**注意：修改数据库名和密码为你的数据库密码！**

#### 2.1.3 创建数据库

```mysql
CREATE TABLE `student`  (
`id` int(20) NOT NULL AUTO_INCREMENT,
`name` varchar(255) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL,
`tid` int(20) NULL DEFAULT NULL,
PRIMARY KEY (`id`) USING BTREE
) ENGINE = MyISAM AUTO_INCREMENT = 5 CHARACTER SET = utf8 COLLATE = utf8_general_ci ROW_FORMAT = Dynamic;

INSERT INTO `student` VALUES (1, '张三', 1);
INSERT INTO `student` VALUES (2, '李四', 1);
INSERT INTO `student` VALUES (3, '王武', 1);
INSERT INTO `student` VALUES (4, '张散散', 1);

CREATE TABLE `teacher`  (
`id` int(20) NOT NULL AUTO_INCREMENT,
`name` varchar(255) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL,
PRIMARY KEY (`id`) USING BTREE
) ENGINE = MyISAM AUTO_INCREMENT = 2 CHARACTER SET = utf8 COLLATE = utf8_general_ci ROW_FORMAT = Dynamic;

INSERT INTO `teacher` VALUES (1, 'hresh');
```

#### 2.1.4 新建POJO类

```java
@Data
public class Student {
    private int id;
    private String name;
    private int tid;
}
```

#### 2.1.5 新建数据访问对象（DAO）接口

新建`IStudentDao`接口

```java
public interface IStudentDao {
   List<Student> queryStudent();
}
```

#### 2.1.6 创建接口映射文件

新建`IAuthorDao.xml`映射文件（从官网拷贝并修改如下内容）

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<!-- namespace需要绑定接口的全限定名-->
<mapper namespace="cn.icelo.demo.dao.IStudentDao">
    <!-- id 接口中的方法，resultType 查询返回的类型，需要写全限定名-->
    <select id="queryStudent" resultType="cn.icelo.demo.pojo.Student">
        <!-- SQL语句-->
    select * from student
  </select>
</mapper>
```

每一个接口的映射文件都需要在主配置文件（mybatis-config.xml）中注册，修改如下内容：

```xml
<mappers>
    <!-- 这里使用的是package方式，详情见配置文件解析 -->
    <package name="cn.icelo.demo.dao"/>
</mappers>
```

#### 2.1.7 编写测试类

```java
package cn.icelo.demo.test;


import cn.icelo.demo.dao.IStudentDao;
import cn.icelo.demo.pojo.Student;
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;

import java.io.IOException;
import java.io.InputStream;
import java.util.List;

/**
 * @author:icelo
 * @date: 2020/11/24
 * @time: 14:49
 */
public class App {

    private static SqlSessionFactory factory;
    private static SqlSession session;
    private static IStudentDao studentDao;

    /**
     * 官网中构建Sqlsession的方法，
     * <p>
     * 每个基于 MyBatis 的应用都是以一个 SqlSessionFactory 的实例为核心的。
     * SqlSessionFactory 的实例可以通过 SqlSessionFactoryBuilder 获得。
     * 而 SqlSessionFactoryBuilder 则可以从 XML 配置文件或一个预先配置的
     * Configuration 实例来构建出 SqlSessionFactory 实例。
     */
    private static void init() {
        String resource = "mybatis-config.xml";
        try {
            InputStream inputStream = Resources.getResourceAsStream(resource);
            factory = new SqlSessionFactoryBuilder().build(inputStream);
            session = factory.openSession();
            studentDao = session.getMapper(IStudentDao.class);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    /**
     * 关闭session所需要的资源
     */
    private static void clear() {
        if (session != null) {
            session.commit();
            session.close();
        }
    }

    public static void main(String[] args) {
        init();
        List<Student> students = studentDao.queryStudent();
        for (Student student : students) {
            System.out.println(student);
        }
        clear();
    }
}
```

#### 2.1.8 调试

![mybatis-02](https://image.icelo.cn/mybatis-02.png)

#### 2.1.9可能遇到的问题

* 配置文件没有注册

  > 在测试类中确保resource的引入的mybatis-config.xml主配置文件路径正确

* 绑定接口错误

  > 在映射文件中绑定的namespace一定为接口的全限定名`一个接口对应一个映射文件`

* 方法名不对

  > 在映射文件中的`<select>`标签下的id是在接口中的方法，方法名唯一

* 返回类型不对

  > 在`select`语句下记得返回类型，即resultType属性，为查询返回的类的全限定名；` 注意，如果返回的是集合，那应该设置为集合包含的类型，而不是集合本身的类型。`

## 三、CRUD操作

使用Mybatis进行一些基本的增删改查操作

### 3.1 select

```xml
<select id="queryStudent" resultType="cn.icelo.demo.pojo.Student">
  select * from student
</select>
```

* id：在命名空间中唯一的标识符，可以被用来引用这条语句。

  > 通常在每一条SQL语句中都需要这个参数，和映射文件对应的接口类中方法名一致
  >
  > 如上面的`	queryStudent`在它对应的接口中有一个未实现的接口方法！
  >
  > ```java 
  > public interface IStudentDao {
  >    /**
  >     * 查询所有学生
  >     * @return 查询到的学生集合
  >     */
  >    List<Student> queryStudent();
  > }
  > ```

* resultType：期望从这条语句中返回结果的类全限定名或别名。 注意，如果返回的是集合，那应该设置为集合包含的类型，而不是集合本身的类型。

  > 在`select`的SQL语句中特别重要不要忘记返回类型，一般是`POJO`包中的实体类的全限定名，

#### 3.1.1 需要带参的select查询语句

在查询语句中需要根据条件查询，如下面：查询id为3的学生的信息：

```java
// IStudentDao.java文件中
/**
 * 根据id查询学生
 * @param id 传入的id
 * @return 返回的学生
 */

Student queryById(int id);
```

```xml
<!--IStudentDao.xml文件-->
<select id="queryById" resultType="cn.icelo.demo.pojo.Student">
    select  * from  student where id= #{id}
</select>
```

```java
// 调用方法
Student student = studentDao.queryById(3);
```

 在MyBatis 创建一个预处理语句（PreparedStatement）参数，使用`#{xxx}`来代替JDBC中的`?`，在测试类中使用代理类的方法调用接口中的函数，并传入参数；一般的查询语句不需要`parameterType`；因为这个属性是可选的，它可以通过类型处理器（TypeHandler）推断出具体传入语句的参数，默认值为未设置（unset）。

#### 3.1.2 模糊查询的三种方法

在mybaits中如何处理模糊查询呢？以下我这里总结三个方法

* **直接传参法：**

  >以查询学生表中“张”姓的学生为例
  >
  >定义接口方法：
  >
  >```java 
  >/**
  > * 模糊查询
  > * @param  name 需要传入的名字
  > * @return 学生集合
  > */
  >List<Student>  queryStudentLike(String name);
  >```
  >
  >映射文件中的SQL语句：
  >
  >```xml
  ><select id="queryStudentLike" resultType="cn.icelo.demo.pojo.Student">
  >    select * from student where name  like #{name}
  ></select>
  >```
  >
  >调用方法：
  >
  >```java
  >String name="张";
  >List<Student> students = studentDao.queryStudentLike("%"+name+"%");
  >for (Student student : students) {
  >    System.out.println(student);
  >}
  >```
  >
  >查询结果：
  >
  >```java
  >Student(id=1, name=张三, tid=1)
  >Student(id=5, name=张无忌, tid=2)
  >Student(id=6, name=张三丰, tid=2)
  >```
  >
  >   `思路：查询的关键字keyword,在代码中拼接好要查询的格式，如%name%,然后直接作为参数传入  mapper.xml的映射文件中。`

* `CONCAT()` **函数：**

  > 在MySQL中有一个比较常用的函数，`CONCAT()`函数能够将多个字符串连接成一个字符串
  >
  > 接口中的方法：
  >
  > ```java 
  > /**
  >  * 模糊查询
  >  * @param  name 需要传入的名字
  >  * @return 学生集合
  >  */
  > List<Student>  queryStudentLike(String name);
  > ```
  >
  > 映射文件中的SQL语句：
  >
  > ```xml
  > <select id="queryStudentLike" resultType="cn.icelo.demo.pojo.Student">
  >     select * from student where name  like CONCAT('%',#{name},'%')
  > </select>
  > ```
  >
  > 调用方法：
  >
  > ```Java
  > String name="张";
  > List<Student> students = studentDao.queryStudentLike(name);
  > ```
  >
  > 查询结果：
  >
  > ```java
  > Student(id=1, name=张三, tid=1)
  > Student(id=5, name=张无忌, tid=2)
  > Student(id=6, name=张三丰, tid=2)
  > ```
  >
  > `思路：在SQL语句中使用CONCAT()函数，在调用函数处传入需要查询的值`
  >
  > **注意：在调用CONCAT()函数时，应该用英文逗号`,`分割开，需要拼接的字符串应该用`''`来包含，其中的`#{}`OGNL表达式不用被包含，函数名必须大写**

* **Mybatis的bind**

  > 在Mybatis的OGNL表达式中，`bind` 元素允许创建一个变量，并将其绑定到当前的上下文。其可以用于模糊查询。
  >
  > 接口中的方法：
  >
  > ```java
  > /**
  >  * 模糊查询
  >  * @param  name 需要传入的名字
  >  * @return 学生集合
  >  */
  > List<Student>  queryStudentLike(String name);
  > ```
  >
  > 映射文件中的SQL语句：
  >
  > ```xml
  > <select id="queryStudentLike" resultType="cn.icelo.demo.pojo.Student">
  >     <bind name="pattern" value="'%'+name+'%'"/>
  >     select * from student where name  like #{pattern}
  > </select>
  > ```
  >
  > 调用方法：
  >
  > ```java 
  > String name="张";
  > List<Student> students = studentDao.queryStudentLike(name);
  > ```
  >
  > 查询结果：
  >
  > ```java
  > Student(id=1, name=张三, tid=1)
  > Student(id=5, name=张无忌, tid=2)
  > Student(id=6, name=张三丰, tid=2)
  > ```
  >
  > `思路：用bind在<select>内定义一个变量，属性name用作替换like后面的值，属性value是需要被替换部分的字符串拼接`
  >
  > 注意：在value中使用`''`单引号包裹需要拼接的字符串，需要传入的变量不需要使用单引号包裹！

### 3.2 insert

在mybatis中使用插入语句，如下面：插入一个老师到数据库中：

```java
// ITeacherDao.java文件中
/**
* 添加老师
* @param  t 需要添加的老师对象
* @return 添加的结果
*/
int insertTeacher(Teacher t);
```

```xml
<!--ITeacherDao.xml文件-->
<mapper namespace="cn.icelo.demo.dao.ITeacherDao">
    <insert id="insertTeacher">
      insert into teacher (id,`name`) values (#{id},#{name})
    </insert>
</mapper>
```

```java
// 调用方法
Teacher t=new Teacher();	//构建Teacher对象
t.setId(2);		// 设定参数
t.setName("icelo");
int result = teacherDao.insertTeacher(t);
System.out.println(result==1?"添加成功！":"添加失败！");
```

#### 3.2.1 多行数据插入的使用

在mybatis中如果你的数据库还支持多行插入, 你也可以传入一个`Student` 数组或集合，并返回自动生成的主键。

```java
/**
 * 插入多条学生数据
 * @param students 需要插入的学生数据集合
 * @return 返回的结果
 */
int insertStudentMore(List<Student> students);
```

```xml
<insert id="insertStudentMore" parameterType="java.util.List">
    insert into student (id,name,tid) values
    <foreach item="item" collection="list" separator=",">
        (#{item.id}, #{item.name},#{item.tid})
    </foreach>
</insert>
```

```java
Student s1=new Student();
s1.setId(7);
s1.setName("赵四");
s1.setTid(2);
Student s2=new Student();
s2.setId(8);
s2.setName("赵高");
s2.setTid(2);
List<Student> students=new ArrayList<>();
students.add(s1);
students.add(s2);
int result = studentDao.insertStudentMore(students);
```

多条数据插入时使用了Mybatis中的OGNL表达式`foreach`的使用；后面会介绍相关内容！这里的返回类型是数据库中的影响的行数

### 3.3 update

使用mybatis语句完成数据库中的修改的操作，如下面：修改id为4的学生的姓名为朱晓明

```java
/**
 * 修改学生
 * @param student 需要修改的学生
 * @return 修改的结果
 */
// IStudentDao.java
int updateStudent(Student student);
```

```xml
<!--IStudentDao.xml-->
<update id="updateStudent">
    update student set id=#{id},name=#{name},tid=#{tid} where id= #{id}
</update>
```

```java
//调用方法
Student s1=new Student();
s1.setId(4);
s1.setName("朱晓明");
s1.setTid(2);
int result = studentDao.updateStudent(s1);
```

在update语句中可以类比insert方法，

### 3.4 delete

使用mybatis语句完成数据库中的删除的操作，如下面：删除id为8的学生

```Java
/**
 * 删除学生
 * @param id 学生的id
 * @return 结果
 */
int deleteStudent(int id);
```

```xml
<delete id="deleteStudent">
    delete from student where id=#{id}
</delete>
```

```java
// 调用方法
int result = studentDao.deleteStudent(8);  
System.out.println(result);
```

### 3.5 万能的Map

在mybaits中还可以使用Map的方法来实现多个参数的传递，这是一种不规范的使用，map就意味着可读性的下降，**所以这不是推荐的方式**！

* 假设实体类或者数据库中的表的字段过多，应当考虑使用Map，可以不用把表的属性全写出来，只要写需要的属性。

  > 如果在开发过程，实体类的字段很多，在SQL语句中可以使用在使用Map传递参数，然后直接在SQL中取出key即可
  >
  > 接口方法：
  >
  > ```java
  > /**
  >  *@parame map 用户的数据存在map中
  >  *@return 结果
  >  **/
  > int insertUser(Map<String, Object> map);
  > ```
  >
  > 映射文件中的SQL：
  >
  > ```xml
  > <!--通过map插入,字段的名字就可以随便取了-->
  > <insert id="insertUser" parameterType="map">
  >     insert into user(id,name,pwd) values (#{userId},#{userName},#{password})
  > </insert>
  > ```
  >
  > 调用方法：
  >
  > ```java 
  > UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
  > Map<String, Object> map = new HashMap<String, Object>();
  > map.put("userId", 5);
  > map.put("userName", "icelo");
  > map.put("password", "123456");
  > userMapper.insertUser(map);
  > ```

## 四、配置文件详解

在mybatis中最重要的就是配置文件了，它会深深影响 MyBatis 行为的设置和属性信息。上面我们列出了主配置文件的 [文件结构](#2.1.1 配置文件结构)，**我们在编写主配置文件时，需要按照上面文件结构顺序编写。**我们这里主要分析几个比较常用配置项。

### 4.1 properties

我们可以使用这个配置项在外部进行配置一些数据库相关的参数，比如：`Driver`，`url` …

在resources目录下新建一个文件名为`db.properties`，在其中配置好数据库相关参数如下所示：

```properties
driver=com.mysql.jdbc.Driver
url=jdbc:mysql://localhost:3306/mybaitsmybaits?useSSL=true
username=root
password=root
```

在主配置文件`mybatis-config.xml`中使用properties文件，如下所示：

```xml
<properties resource="db.properties" />
	...
<property name="driver" value="${driver}" />
<property name="url" value="${url}" />
<property name="username" value="${username}" />
<property name="password" value="${password}" />
	...
```

在properties中有`resource`属性可以配置，主要是要引入的外部文件位置

```xml
<properties resource="db.properties" />
  <property name="username" value="dev_user"/>
  <property name="password" value="F2Fa3!33TYyg"/>
</properties>
```

同样可以使用property标签在主配置文件中配置数据库的相关信息，当外部文件和主配置文件中同时存在时，将会优先使用外部文件，亦可一部分在外，一部分在内。

### 4.2 settings

|              设置名              |                             描述                             |                            有效值                            |                        默认值                         |
| :------------------------------: | :----------------------------------------------------------: | :----------------------------------------------------------: | :---------------------------------------------------: |
|           cacheEnabled           |   全局性地开启或关闭所有映射器配置文件中已配置的任何缓存。   |                        true \| false                         |                         true                          |
|        lazyLoadingEnabled        | 延迟加载的全局开关。当开启时，所有关联对象都会延迟加载。 特定关联关系中可通过设置 `fetchType` 属性来覆盖该项的开关状态。 |                        true \| false                         |                         false                         |
|      aggressiveLazyLoading       | 开启时，任一方法的调用都会加载该对象的所有延迟加载属性。 否则，每个延迟加载属性会按需加载（参考 `lazyLoadTriggerMethods`)。 |                        true \| false                         |     false （在 3.4.1 及之前的版本中默认为 true）      |
|    multipleResultSetsEnabled     |     是否允许单个语句返回多结果集（需要数据库驱动支持）。     |                        true \| false                         |                         true                          |
|          useColumnLabel          | 使用列标签代替列名。实际表现依赖于数据库驱动，具体可参考数据库驱动的相关文档，或通过对比测试来观察。 |                        true \| false                         |                         true                          |
|         useGeneratedKeys         | 允许 JDBC 支持自动生成主键，需要数据库驱动支持。如果设置为 true，将强制使用自动生成主键。尽管一些数据库驱动不支持此特性，但仍可正常工作（如 Derby）。 |                        true \| false                         |                         False                         |
|       autoMappingBehavior        | 指定 MyBatis 应如何自动映射列到字段或属性。 NONE 表示关闭自动映射；PARTIAL 只会自动映射没有定义嵌套结果映射的字段。 FULL 会自动映射任何复杂的结果集（无论是否嵌套）。 |                     NONE, PARTIAL, FULL                      |                        PARTIAL                        |
| autoMappingUnknownColumnBehavior | 指定发现自动映射目标未知列（或未知属性类型）的行为。`NONE`: 不做任何反应`WARNING`: 输出警告日志（`'org.apache.ibatis.session.AutoMappingUnknownColumnBehavior'` 的日志等级必须设置为 `WARN`）`FAILING`: 映射失败 (抛出 `SqlSessionException`) |                    NONE, WARNING, FAILING                    |                         NONE                          |
|       defaultExecutorType        | 配置默认的执行器。SIMPLE 就是普通的执行器；REUSE 执行器会重用预处理语句（PreparedStatement）； BATCH 执行器不仅重用语句还会执行批量更新。 |                      SIMPLE REUSE BATCH                      |                        SIMPLE                         |
|     defaultStatementTimeout      |     设置超时时间，它决定数据库驱动等待数据库响应的秒数。     |                          任意正整数                          |                     未设置 (null)                     |
|         defaultFetchSize         | 为驱动的结果集获取数量（fetchSize）设置一个建议值。此参数只可以在查询设置中被覆盖。 |                          任意正整数                          |                     未设置 (null)                     |
|       defaultResultSetType       |           指定语句默认的滚动策略。（新增于 3.5.2）           | FORWARD_ONLY \| SCROLL_SENSITIVE \| SCROLL_INSENSITIVE \| DEFAULT（等同于未设置） |                     未设置 (null)                     |
|       safeRowBoundsEnabled       | 是否允许在嵌套语句中使用分页（RowBounds）。如果允许使用则设置为 false。 |                        true \| false                         |                         False                         |
|     safeResultHandlerEnabled     | 是否允许在嵌套语句中使用结果处理器（ResultHandler）。如果允许使用则设置为 false。 |                        true \| false                         |                         True                          |
|     mapUnderscoreToCamelCase     | 是否开启驼峰命名自动映射，即从经典数据库列名 A_COLUMN 映射到经典 Java 属性名 aColumn。 |                        true \| false                         |                         False                         |
|         localCacheScope          | MyBatis 利用本地缓存机制（Local Cache）防止循环引用和加速重复的嵌套查询。 默认值为 SESSION，会缓存一个会话中执行的所有查询。 若设置值为 STATEMENT，本地缓存将仅用于执行语句，对相同 SqlSession 的不同查询将不会进行缓存。 |                     SESSION \| STATEMENT                     |                        SESSION                        |
|         jdbcTypeForNull          | 当没有为参数指定特定的 JDBC 类型时，空值的默认 JDBC 类型。 某些数据库驱动需要指定列的 JDBC 类型，多数情况直接用一般类型即可，比如 NULL、VARCHAR 或 OTHER。 |       JdbcType 常量，常用值：NULL、VARCHAR 或 OTHER。        |                         OTHER                         |
|      lazyLoadTriggerMethods      |             指定对象的哪些方法触发一次延迟加载。             |                    用逗号分隔的方法列表。                    |            equals,clone,hashCode,toString             |
|     defaultScriptingLanguage     |            指定动态 SQL 生成使用的默认脚本语言。             |                  一个类型别名或全限定类名。                  | org.apache.ibatis.scripting.xmltags.XMLLanguageDriver |
|      defaultEnumTypeHandler      |    指定 Enum 使用的默认 `TypeHandler` 。（新增于 3.4.5）     |                  一个类型别名或全限定类名。                  |        org.apache.ibatis.type.EnumTypeHandler         |
|        callSettersOnNulls        | 指定当结果集中值为 null 的时候是否调用映射对象的 setter（map 对象时为 put）方法，这在依赖于 Map.keySet() 或 null 值进行初始化时比较有用。注意基本类型（int、boolean 等）是不能设置成 null 的。 |                        true \| false                         |                         false                         |
|    returnInstanceForEmptyRow     | 当返回行的所有列都是空时，MyBatis默认返回 `null`。 当开启这个设置时，MyBatis会返回一个空实例。 请注意，它也适用于嵌套的结果集（如集合或关联）。（新增于 3.4.2） |                        true \| false                         |                         false                         |
|            logPrefix             |             指定 MyBatis 增加到日志名称的前缀。              |                          任何字符串                          |                        未设置                         |
|             logImpl              |    指定 MyBatis 所用日志的具体实现，未指定时将自动查找。     | SLF4J \| LOG4J \| LOG4J2 \| JDK_LOGGING \| COMMONS_LOGGING \| STDOUT_LOGGING \| NO_LOGGING |                        未设置                         |
|           proxyFactory           |      指定 Mybatis 创建可延迟加载对象所用到的代理工具。       |                      CGLIB \| JAVASSIST                      |            JAVASSIST （MyBatis 3.3 以上）             |
|             vfsImpl              |                       指定 VFS 的实现                        |         自定义 VFS 的实现的类全限定名，以逗号分隔。          |                        未设置                         |
|        useActualParamName        | 允许使用方法签名中的名称作为语句参数名称。 为了使用该特性，你的项目必须采用 Java 8 编译，并且加上 `-parameters` 选项。（新增于 3.4.1） |                        true \| false                         |                         true                          |
|       configurationFactory       | 指定一个提供 `Configuration` 实例的类。 这个被返回的 Configuration 实例用来加载被反序列化对象的延迟加载属性值。 这个类必须包含一个签名为`static Configuration getConfiguration()` 的方法。（新增于 3.2.3） |                 一个类型别名或完全限定类名。                 |                        未设置                         |
|      shrinkWhitespacesInSql      | 从SQL中删除多余的空格字符。请注意，这也会影响SQL中的文字字符串。 (新增于 3.5.5) |                        true \| false                         |                         false                         |
|      defaultSqlProviderType      | Specifies an sql provider class that holds provider method (Since 3.5.6). This class apply to the `type`(or `value`) attribute on sql provider annotation(e.g. `@SelectProvider`), when these attribute was omitted. |          A type alias or fully qualified class name          |                        Not set                        |

MyBatis 中极为重要的调整设置，它们会改变 MyBatis 的运行时行为，这里直接使用官网文档，参考使用！

### 4.3 typeAliases

为了降低在映射文件中的全限定名的冗余，主配置文件提供为Java类型设置一个别名

在mybatis中提供了三种设置别名的方法：

* **使用类的全限定名**

  > 在`typeAliases`中可以配置很多`typeAlias`，使用这中方式需要指定类的全限定名；
  >
  > ```xml
  > <typeAlias type="cn.icelo.demo.pojo.Student" alias="stu" ></typeAlias>
  > ```
  >
  > 在`tpye`中设置类的全限定名，在`alias`中设置想要使用的别名，这个别名可以用在使用到`cn.icelo.demo.pojo.Student`的地方
  >
  > 使用：
  >
  > ```xml
  > <select id="queryById" resultType="stu">
  >     select  * from  student where id= #{id}
  > </select>
  > ```
  >
  > 在`resultType`中直接使用上面的别名替代

* **使用包扫描方式**

  > 在`typeAliases`中可以直接配置一个包中的实体类的别名，在使用时直接使用类名即可；
  >
  > ```xml
  > <typeAliases >
  >    <package name="cn.i    <select id="queryStudent" resultType="STUDENT">
  >     select * from student
  >   </select>celo.demo.pojo"/>
  > </typeAliases>
  > ```
  >
  > 使用：
  >
  > ```xml
  > <select id="queryStudent" resultType="STUDENT">
  >     select * from student
  > </select>
  > ```
  >
  > 注意：在使用时不区分大小写；

* **使用注解的方式**

  >在mybatis中还提供了一种使用注解的方式来为类取别名的方式；这种方法不能独立存在，必须依托上面的两种方式：
  >
  >```xml
  ><typeAliases>
  >        <package name="cn.icelo.demo.pojo"/>
  ></typeAliases>
  >```
  >
  >```java
  >@Alias("stu")
  >public class Student {
  >    private int id;
  >    private String name;
  >    private int tid;
  >}
  >```
  >
  >也就是说，在上面两种方法存在下，会优先使用注解的方法为别名

推荐使用第一种；

### 4.4 mappers

在主配置文件中，mapper是极其重要的，每一个要使用的映射文件都需要在主配置文件中绑定注册，主要是使用三种方式注册：

* **resource**

  > 使用这个方法是比较复杂的，所有的映射文件的都必须放置在`resources`目录下，但为了方便管理，我们经常在`resources`目录下构建一个和`src`一样的目录结构，这样为了能够让类和我们的映射文件对应起来：
  >
  > ![mybatis-04](https://image.icelo.cn/mybatis-04.png)
  >
  > 使用这个方式可以不需要让映射文件和对应的接口类的名字一致，可以`DIY`其文件名；但在IDEA中使用maven构建项目会存在xml文件不能编译的问题，解决办法：
  >
  > 首先在pom文件中找到<bulid></build>节点，改为下面代码
  >
  > ```xml
  > <build>  
  >   <resources>  
  >     <!-- mapper.xml文件在java目录下 -->
  >     <resource>  
  >       <directory>src/main/java</directory>  
  >         <includes>  
  >           <include>**/*.xml</include>  
  >         </includes>  
  >     </resource>  
  >     <!-- mapper.xml文件在resources目录下-->
  >     <resource>
  >         <directory>src/main/resources</directory> 
  >     </resource>
  >   </resources>  
  > </build>
  > ```

* **package**

  >使用package的方式可以为一整个包中的映射文件绑定注册，将映射文件放置接口包中，可以将包内的映射器接口实现全部注册为映射器 
  >
  >![mybaits-05](https://image.icelo.cn/mybatis-05.png)
  >
  >```xml
  ><mappers>
  >   <package name="cn.icelo.demo.dao"/>
  ></mappers>
  >```
  >
  >这种方式是非常推荐的，但注意：`映射文件的文件名必须要和接口类中保持一致，不能随意DIY`

* **class**

  >Mapper中可以使用接口的全限定名的方式为映射文件注册，当时有这中方式时，mybatis会扫描所有的资源目录和对应接口类下的包，找到和接口类的名字相对应的映射文件，为其绑定注册。所以使用这个方式，不能`DIY`其文件名，必须和接口类文件一致，文件位置只能在资源目录和对应接口所在的包中；
  >
  >```xml
  ><mappers>
  >   <mapper class="cn.icelo.demo.dao.IStudentDao"></mapper>
  >    <mapper class="cn.icelo.demo.dao.ITeacherDao"></mapper>
  ></mappers>
  >```

在Java项目中项目编译后不同的源码文件夹会被合并到bin目录下，形成类路径。不同的源码文件夹下的同名的包实际上是同一个包，因为编译后，包中的文件都在同一个文件夹下。

## 五、映射文件

### 5.1 参数

#### 5.1.1单个参数问题

当传入单个参数时`#{}`内的名字可以任意，它只是一个占位符的作用，注意在其前面的`id`是你需要参入参数的对应数据库中列

```java
/**
 * 根据id查询学生
 * @param id 传入的id
 * @return 返回的学生
 */

Student queryById(int id);
```

```xml
<select id="queryById" resultType="stu">
    select  * from  student where id= #{id}
</select>
```

#### 5.1.2 多个参数问题

这里使用一个业务来说明问题：查询姓名为“张三”，老师编号为1号的学生（三种方法）

接口方法：

```java
/**
 * 查询学生姓名为”张三"，教师编号为1号的学生
 * @param name 学生姓名
 * @param tid 教师编号
 * @return 学生对象
 */
Student queryStudentByTid(String name,int tid);
```

```xml
<!--配置映射文件-->
<select id="queryStudentByTid" resultType="stu">
    select * from student where name=#{name} and tid=#{tid}
</select>
```

```java
// 调用方法
Student student = studentDao.queryStudentByTid("张三",1);
```

查询结果：

```java
Caused by: org.apache.ibatis.binding.BindingException: Parameter 'name' not found. Available parameters are [arg1, arg0, param1, param2]
	at org.apache.ibatis.binding.MapperMethod$ParamMap.get(MapperMethod.java:212)
	at org.apache.ibatis.reflection.wrapper.MapWrapper.get(MapWrapper.java:45)
	at org.apache.ibatis.reflection.MetaObject.getValue(MetaObject.java:122)
	at org.apache.ibatis.executor.BaseExecutor.createCacheKey(BaseExecutor.java:219)
	at org.apache.ibatis.executor.CachingExecutor.createCacheKey(CachingExecutor.java:146)
	at org.apache.ibatis.executor.CachingExecutor.query(CachingExecutor.java:88)
	at org.apache.ibatis.session.defaults.DefaultSqlSession.selectList(DefaultSqlSession.java:147)
	... 7 more
```
调试错误：==Parameter 'name' not found. Available parameters are [arg1, arg0, param1, param2]==当传入多个参数时，mybatis要求使用`arg1, arg0...` 或者`param1, param2`对参数进行编号！

* 修改映射文件如下：

```xml
<select id="queryStudentByTid" resultType="stu">
    select * from student where name=#{arg0} and tid=#{arg1}
</select>
```

或者：

```xml
<select id="queryStudentByTid" resultType="stu">
    select * from student where name=#{param1} and tid=#{param2}
</select>
```

注意：使用`arg`从==0==开始，`param`从==1==开始

* 使用map传参：

  向上面[万能的map](#3.5 万能的Map)所介绍的一样，可以将需要传入的参数都可以封装成一个map对象，`Map<String,Oboject>`，在需要传入参数时`String`类型为`#{}`中所对应，后面的`Object`为需要传入的参数。

  接口类：

  ```java
  /**
   * 查询学生姓名为张三老师编号为1的学生
   * @param map 需要传入的参数map集合
   * @return 查询到的学生对象
   */
  Student queryStudentByTid(Map<String ,Object> map);
  ```

  映射配置文件：

  ```xml
  <select id="queryStudentByTid" parameterType="map" resultType="stu">
      select * from student where name=#{name } and tid=#{tid}
  </select>
  ```

  调用方法：

  ```Java
  Map<String,Object> map=new HashMap<>();
  map.put("name","张三");
  map.put("tid",1);
  Student student = studentDao.queryStudentByTid(map);
  ```

* 使用参数注解方式：

  在接口方法中使用`@Param`注解为所需要的参数标识，在映射文件中就能使用了，如下：

  接口类：

  ```Java
  /**
   * 查询学生姓名为张三老师编号为1的学生
   * @param name 学生姓名
   * @param tid  老师编号
   * @return 学生对象
   */
  Student queryStudentByTid(@Param("name") String name,@Param("tid") int tid);
  ```

  映射文件：

  ```xml
  <select id="queryStudentByTid" parameterType="map" resultType="stu">
      select * from student where name=#{name} and tid=#{tid}
  </select>
  ```

  调用方法：

  ```java
  Student student = studentDao.queryStudentByTid("张三",1);
  ```

  注意：使用这个方式需保证`@Param`中的标识和`#{}`一致，在接口方法中的`@Param`和参数用空格分隔，没有`逗号`。

#### 5.1.3 数组参数问题

使用数组的情况一般是批量插入，向[上面例子](#3.2.1 多行数据插入的使用)差不多，需要使用到`foreach`OGNL表达式，现在有一个业务，要查询id为1,3,5的学生：

接口类：

```Java
/**
 * 查询id为1，3，5的学生
 * @param ids 需要查询的学生数组
 * @return 学生集合对象
 */
List<Student> queryStudentByIds(List<Integer> ids);
```

映射文件：

```xml
<select id="queryStudentByIds" resultType="stu">
    select  * from student where id in
    <foreach collection="list" item="item" separator="," open="(" close=")">
       #{item}
    </foreach>
</select>
```

```java
List<Integer> ids=new ArrayList<>();
ids.add(1);
ids.add(3);
ids.add(5);
List<Student> students = studentDao.queryStudentByIds(ids);
```

注意：

* 如果传入的是单参数且参数类型是一个`List`的时，`collection`属性值为`list `.
* 如果传入的是单参数且参数类型是一个`array`数组时，`collection`的属性值为`array` .

### 5.2 结果映射

`resultMap`是mybatis中==最重要最强大==的元素:+1:

我们在查询一个学生对象时如果数据库中表的字段与`Student`类的属性名称一致，我们就可以使用`resultType`来直接放回一个`Student`返回。但是如果在数据库中的字段和类中属性名称不一致时，我们就可以使用`resultMap`来做结果集映射了。

如果`Student`类保持不表，在数据库中用`sid`代替`id`，然后查询所有的学生

```xml
<select id="queryStudent" resultType="stu">
select * from student
</select>
```

查询结果：·

```java
Student(id=0, name=张三, tid=1)
Student(id=0, name=李四, tid=1)
Student(id=0, name=王武, tid=1)
Student(id=0, name=朱晓明, tid=2)
Student(id=0, name=张无忌, tid=2)
Student(id=0, name=张三丰, tid=2)
Student(id=0, name=赵四, tid=2)
```

不会报错，但是在查询的数据中，所有学生的`id`为==0==这显然是不合理的，所以我们需要使用`resultMap`

我们需要在==映射配置文件==中编写结果集映射，如下所示：

   映射配置文件：

```xml
<resultMap id="stuMap" type="stu" >
    <id column="sid" property="id"></id>
</resultMap>
```

将`column`和数据库中的字段相对应，`property`和实体类中的属性相对应，即可完成映射。在`resultMap`中一般用`<id></id>`表示数据库中的主键的映射，使用`<result></result>`表示一般字段的映射。

### 5.3 高级结果映射[^重点]

在数据库中包含着一对多、一对一的关系。比如说一个人和他的身份证就是一对一的关系，但是他和他的银行卡就是一对多的关系。我们的生活中存在着很多这样的场景。在数据库中会存在几种不同的关系，如下：

![mybatis-06](https://image.icelo.cn/mybaits-06.jpg)

* **单向和双向问题**

  主要有一对一，一对多，多对多这三种情况，但是每一种又分为单向和双向，先说这个一对多这种关系来讲，比如有学生和老师，一个老师中有多个学生，从老师方面来看，是一对多关系，而多个学生在一个老师班上，是多对一关系，那么如果我们的业务需求只需要通过老师查找到所有的学生，那么我们就只需要进行单向一对多的映射，如果我们需要通过学生来查询出对应的老师，那么我们就需要进行单向多对一的映射，而如果我们这两个业务需求都需要实现，也就是不管从哪一方进行查找，都需要能够找到对方，那么此时就应该编写双向一对多或者双向多对一(双向一对多和双向多对一是一样的意思)。所以，不管是编写哪一种，都是根据业务需求来进行决策的。这就是单向和双向的意思。

* **什么是多对多**？

  多对多就是不管从哪一方看，都是一对多，那么该关系就是多对多。比如学生跟选修课之间，从学生方看，一个学生能选多门选修课，一对多关系，从选修课之间，一门选修课可以被多个学生选择，也是一对多关系，那么学生跟选修课就是多对多关系。多对多关系之间都会由第三张表来表示这种关系。而不会相互设置外键。

#### 5.3.1 多对一

首先我们先来创建一个多对一的情况，在学生属性中加上一个属性`Teacher`，这里只是改变了实体类，数据库中的字段不变，如下：

实体类：

```JAVA 
@Data
public class Student {
    private int id;
    private String name;

    /**
     * 学生需要关联一个老师
     * 
     */
    private Teacher teacher;
}
```

主要业务是，查询所有学生的信息，当然也包括老师的信息；

接口类：

```java
// IStudnetDao接口
/**
 * 查询所有的学生信息
 * @return 学生集合
 */
List<Student> getStudent();
```

**思路一：**查询所有的学上信息，根据查询出的学生的tid，寻找对应的老师

SQL语句：

```mysql
# 第一步：查询出所有学生
select * from student;
# 第二步：根据查询到的学生tid，查询到对应的老师
select * from teacher where id=#{tid};
```

有点类似`SQL`的子查询的语句分开写；那么在映射文件该如何写呢？

我们可以先写好==根据学生的tid查询老师==这个部分；

```xml
<select id="getTeacher" resultType="teacher">
    select * from teacher where id=#{tid}
</select>
```

这个`select`语句是在`Student`的映射文件中写的，那么在`Teacher`的接口中需不需要写这个方法的接口呢？
==答案：不需要，当要调用dao或者mapper里面调用这个teacher的时候才需要定义方法，如果在SQL中使用子查询时不需要==

查询所有学生的SQL语句：

```xml
<select id="getStudent" resultMap="???">
    select * from student
</select>
```

那么这`resultMap`中需要将两个查询结合起来，那应该怎么写呢？

我们可以先把`Student`中简单属性要映射的关系写好，然后处理复杂的，如下：

```xml
<resultMap id="StudentTeacher" type="student">
    <!--        先把简单的属性写好-->
    <result property="id" column="id"/>
    <result property="name" column="name"/>

    <!--  Teacher 属性该如何写？-->
    <result property="Teacher" column="???"
</resultMap>
```

在Mybatis中，复杂类型，我们需要单独处理，处理原则：

* 对象：`association`
* 集合：`collection`

完整的`resultMap`就是：

```xml
<resultMap id="StudentTeacher" type="student">
    <!--        先把简单的属性写好-->
    <result property="id" column="id"/>
    <result property="name" column="name"/>

    <!--  Teacher 属性该如何写？-->
    <association property="teacher" column="tid" javaType="Teacher" select="getTeacher"/>
</resultMap>
```

>在`association`中，
>
>* `property`表示的是`Student`类中`Teacher`类型属性的名称，不可随意写，（特别是不能写成类型名，如：`Teacher`）
>* `column`表示的是两个表需要建立的连接的字段名，也就是查询`Teacher`的后面的`id`的赋值
>* `javaType`即表示`property`的`Java`类型，
>* `select`表示的是该映射文件中的SQL查询，对应的是`Teacher`查询的`id`

**思路二：**根据数据库中的多表查询的复杂SQL语句方法。

SQL语句：

```mysql
# 查询所有的结果
select * from student s,teacher t where s.tid=t.id;
```

或者，查询我们需要的`SQL`字段，改写为下面的SQL

```mysql
select s.id sid,s.name sname, t.name tname from student s,teacher t where s.tid=t.id;
```

映射文件：

```xml
<select id="getStudent" resultMap="???">
    select s.id sid,s.name sname, t.id tid, t.name tname from student s,teacher t where s.tid=t.id;
</select>
```

那么这`resultMap`中需要将查到的结果进行映射，可以先写好，简单的属性，如下：

```xml
<resultMap id="StudentTeacher" type="student">
    <result property="id" column="sid"/>
    <result property="name" column="sname"/>
</resultMap>
```

这里的`column`为数据库中查询的字段，注意为该别名后的==别名==

依照我们上面的处理原则，则完整的`resultMap`为：

```xml
<resultMap id="StudentTeacher" type="student">
    <result property="id" column="sid"/>
    <result property="name" column="sname"/>
    <association property="teacher" javaType="Teacher">
        <result property="id" column="tid"/>
        <result property="name" column="tname"/>
    </association>
</resultMap>
```

`Teacher`依然是一个对象，应该使用`association`，在里面继续使用`<result>`标签写上`Teacher`对应的属性和对应的数据库字段。取别名的按照==别名==写。

> 注意：若查询的过程中，有字段中的全为空，或者全为0，请将`resultMap`中的字段写完整。

#### 5.3.2 一对多

我们再来创建一个一对多的情况，去掉上面学生属性中的`Teacher`，在`Teacher`中加上`List<Student>`属性。这样从老师角度看就是一对多了。这里同样只是改变了实体类，数据库中的字段不变，如下：

学生类：

```java
@Data
public class Student {
    private int id;
    private String name;
    private int tid;
}
```

老师类：

```java
@Data
public class Teacher {
    private int id;
    private String name;
   
    /**
     * 一个老师关联上多个学生，一个list数组
     */
    private List<Student> students;
}
```

接口类：

```java
public interface ITeacherDao {
    /**
     * 查询所有老师信息，同样包括老师信息下的学生集合
     * @return
     */
    List<Teacher> getTeacher(int id);
}
```

**思路一：*先查询出指定老师，根据老师的`id`查出学生集合。

SQL语句：

```sql
select * from teacher where id=2
```

然后根据老师的`id`查询对应的学生集合

```sql
select * from student where tid=2;
```

映射文件：

```xml
<resultMap id="TeacherStudent" type="teacher">
    <result property="id" column="id"/>
    <result property="name" column="name"/>
    <collection property="students" column="id" javaType="ArrayList" ofType="Student" select="getStudent" />
</resultMap>
```

同样是复杂属性，我们需要复杂处理，这里使用的是集合，集合中泛型信息，我们使用`ofType`获取。

**思路二：**使用复杂的SQL语句处理，对结果进行映射。

SQL语句：

```sql
select s.id sid ,s.name sname, s.tid tid ,t.name tname  from student s,teacher t where s.tid=t.id and t.id=2
```

映射文件：

```xml
<select id="getTeacher" resultMap="???">
select s.id sid ,s.name sname, s.tid tid ,t.id id,t.name tname from student s,teacher t where s.tid=t.id and t.id=#{id}
</select>
```

我们需要使用`resultMap`对查询的结果进行映射，如下：

```xml
<resultMap id="TeacherStudent" type="Teacher">
    <!-- 先写出简单属性的映射 -->
    <result property="id" column="id"/>
    <result property="name" column="tname" />
    <!-- 复杂属性单独处理 -->
    <collection property="students"  javaType="ArrayList" ofType="student">
        <result property="id" column="sid"/>
        <result property="name" column="sname"/>
        <result property="tid" column="tid"/>
    </collection>
</resultMap>
```

> 这里的`javaType`可写可不写，mybatis通常能够做出相对应的类型推断，`ofTyppe`为集合中的类型

### 5.4 缓存

MyBatis 内置了一个强大的事务性查询缓存机制，它可以非常方便地配置和定制。默认情况下，只启用了本地的会话缓存，它仅仅对一个会话中的数据进行缓存。 

#### 5.4.1 一级缓存

在mybatis中是默认开启了一级缓存，一级缓存是`SqlSession`级别的，即，同一个`SqlSession` ，多次调用同一个Mapper和同一个方法的同一个参数，只会进行一次数据库查询，然后把数据缓存到缓冲中，以后直接先从缓存中取出数据，不会直接去查数据库。如果是相同的SQL语句，会优先命中一级缓存，避免直接对数据库进行查询，提高性能。具体执行过程如下图所示。![mybatis-06](https://image.icelo.cn/mybatis-06.jpg)

 但是不同的`SqlSession`对象，因为不同的`SqlSession`都是相互隔离的，所以相同的Mapper、参数和方法，他还是会再次发送到SQL到数据库去执行，返回结果。

我们这里用根据学生`id`查询学生来举例，如下：

```java
Student s1=studentDao.getStudent(1);
System.out.println(s1);
Student s2=studentDao.getStudent(1);
System.out.println(s2);
System.out.println(s1==s2);
```

查询结果：

![查询结果](https://image.icelo.cn/mybatis-07.png)

当使用同一条`SQL`语句完成两次查询的时候，mybatis只进行一次查询，第二次查询直接使用上一次查询的结果，两个结果返回的对象一样。

**问题：哪些因素会使一级缓存失效？**

- 通过同一个SqlSession执行更新操作时，这个更新操作不仅仅指代`update`操作，还指`insert`和`delete`操作；
- 事务提交时会删除一级缓存；
- 事务回滚时也会删除一级缓存；
- 开启两个`SqlSession`，在`sqlSession1`中查询数据，使一级缓存生效，在`sqlSession2`中更新数据库，验证一级缓存只在数据库会话内部共享。

**总结：**

> 1. MyBatis一级缓存的生命周期和SqlSession一致。
> 2. MyBatis一级缓存内部设计简单，只是一个没有容量限定的HashMap，在缓存的功能性上有所欠缺。
> 3. MyBatis的一级缓存最大范围是SqlSession内部，有多个SqlSession或者分布式的环境下，数据库写操作会引起脏数据，建议设定缓存级别为Statement。

#### 5.4.2 二级缓存

 二级缓存是`mapper`级别的缓存，它的实现机制跟一级缓存差不多，也是基于`PerpetualCache`的`HashMap`本地存储。作用域为`mapper`的`namespace`，可以自定义存储，比如`Ehcache`。`Mybatis`的二级缓存是跨`Session`的，每个`Mapper`享有同一个二级缓存域。

二级缓存是需要配置来开启的，在`Mapper`映射文件中加上：

```xml
<mapper namespace="cn.icelo.demo.dao.ITeacherDao">
    <cache/>
    ...
</mapper>
```

编写不同的`Sqlsession`来测试，如下：

```java
SqlSession sqlSession1 = factory.openSession(); // 创建一个Sqlsession1
IStudentDao studentDao1 = sqlSession1.getMapper(IStudentDao.class);
Student student1 = studentDao1.getStudent(1); // SqlSession1查询的学生对象
System.out.println(student1);
sqlSession1.close(); // 关闭SqlSession1
SqlSession sqlSession2 = factory.openSession(); // 创建一个SqlSession2
IStudentDao studentDao2 = sqlSession2.getMapper(IStudentDao.class);
Student student2 = studentDao2.getStudent(1);// Sqlsession2查询的学生对象
sqlSession2.close(); // 关闭Sqlsession2
System.out.println(student2);
```

调试结果：

```java
Exception in thread "main" org.apache.ibatis.cache.CacheException: Error serializing object.  Cause: java.io.NotSerializableException: cn.icelo.demo.pojo.Student
	at org.apache.ibatis.cache.decorators.SerializedCache.serialize(SerializedCache.java:95)
	at org.apache.ibatis.cache.decorators.SerializedCache.putObject(SerializedCache.java:56)
	at org.apache.ibatis.cache.decorators.LoggingCache.putObject(LoggingCache.java:49)
	at org.apache.ibatis.cache.decorators.SynchronizedCache.putObject(SynchronizedCache.java:43)
	at org.apache.ibatis.cache.decorators.TransactionalCache.flushPendingEntries(TransactionalCache.java:116)
	at org.apache.ibatis.cache.decorators.TransactionalCache.commit(TransactionalCache.java:99)
	at org.apache.ibatis.cache.TransactionalCacheManager.commit(TransactionalCacheManager.java:44)
	at org.apache.ibatis.executor.CachingExecutor.close(CachingExecutor.java:61)
	at org.apache.ibatis.session.defaults.DefaultSqlSession.close(DefaultSqlSession.java:263)
	at cn.icelo.demo.test.App.main(App.java:57)
Caused by: java.io.NotSerializableException: cn.icelo.demo.pojo.Student
	at java.io.ObjectOutputStream.writeObject0(ObjectOutputStream.java:1184)
	at java.io.ObjectOutputStream.writeObject(ObjectOutputStream.java:348)
	at java.util.ArrayList.writeObject(ArrayList.java:766)
	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
	at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	at java.lang.reflect.Method.invoke(Method.java:498)
	at java.io.ObjectStreamClass.invokeWriteObject(ObjectStreamClass.java:1140)
	at java.io.ObjectOutputStream.writeSerialData(ObjectOutputStream.java:1496)
	at java.io.ObjectOutputStream.writeOrdinaryObject(ObjectOutputStream.java:1432)
	at java.io.ObjectOutputStream.writeObject0(ObjectOutputStream.java:1178)
	at java.io.ObjectOutputStream.writeObject(ObjectOutputStream.java:348)
	at org.apache.ibatis.cache.decorators.SerializedCache.serialize(SerializedCache.java:91)
	... 9 more

```

注意：在使用二级缓存中，一定要在使用对象实体类上实现`Serializable`接口，如下：

```java
public class Student implements Serializable {
    private int id;
    private String name;
    private int tid;
}
```

![](https://image.icelo.cn/8.png)

## 六、动态SQL语句

### 5.1 if

`if`是mybatis中的判断函数，这个有点类似Java语言中`if`语句，在mybatis中一般和`test`一起使用，来看一个简单的例子：

```java
/**
 * 查询指定学生姓名
 * @param name 学生的姓名
 * @return 学生对象
 */
List<Student> getStudentByName(@Param("name") String name);
```

这里面的`name`可以为`null`，代表不传值，即代表查询所有学生。

映射文件：

```xml
<select id="getStudentByName" resultType="student">
    select * from student
    <if test="name!=null and name !=''">
        where name =#{name}
    </if>
</select>
```

调用函数：

```java
IStudentDao studentDao= sqlSession.getMapper(IStudentDao.class);
List<Student> students = studentDao.getStudentByName(null);
for (Student student : students) {
    System.out.println(student);
}
```

>注意：在`test`中写上`if`的判断条件

### 5.2 choose (when, otherwise)

`choose`有点类似于`Java`中的`switch`，常常配合`when`和`otherwise`一起来使用。我们来看一个简单的例子：

接口类：

```java
/**
 * 查询学生
 * @param id 学生的id
  * @param tid 学生老师id
 * @param name 学生的名字
 * @return 学生对象
 */
List<Student> getStudentByName(@Param("id") int id,@Param("tid") int tid,@Param("name") String name);
```

映射文件：

```xml
<select id="getStudentByName" resultType="student">
    SELECT * FROM student WHERE 1=1
    <choose>
        <when test="id!=0">
            and 5 > #{id}
        </when>
        <when test="tid!=0">
            and tid=#{tid}
        </when>
        <otherwise>
            and name like CONCAT('%',#{name},'%')
        </otherwise>
    </choose>
</select>
```

调用类：

```java
IStudentDao studentDao= sqlSession.getMapper(IStudentDao.class);
List<Student> students = studentDao.getStudentByName(0,0,"张");
for (Student student : students) {
    System.out.println(student);
}
```

结果：

```markdown
Student(id=1, name=张三, tid=1)
Student(id=5, name=张无忌, tid=2)
Student(id=6, name=张三丰, tid=2)
```

在查询条件中，如果用户传来了`id`，那么我就查询该小于5的条件，如果用户传来了`tid`，那么我就我们添加`tid`的查询条件，最后如果用户任何一个查询条件都没有添加进来，那么默认查询条件就是模糊查询包含“张”的所有数据。

> 这里需要添加一个`where 1=1`的来消除后面`and……`的，比较麻烦，使用`where`即比较方便多了

### 5.3 where

上面例子用`where`改写为下面语句

```xml
<select id="getStudentByName" resultType="student">
    SELECT * FROM student
    <where>
        <choose>
            <when test="id!=0">
                and 5 > #{id}
            </when>
            <when test="tid!=0">
                and tid=#{tid}
            </when>
            <otherwise>
                and name like CONCAT('%',#{name},'%')
            </otherwise>
        </choose>
    </where>
</select>
```

这样，只有`where`元素中有条件成立，才会将`where`关键字组装到`SQL`中，这样就比前一种方式简单许多。`where`能够智能处理`where`后面的查询条件,如果一个查询条件都没有,就不会拼接`where`子句，如果有查询条件,会智能去掉第一个`and`

### 5.5 foreach

当传入一个数组时使用，`foreach`元素的属性主要有 `item`，`index`，`collection`，`open`，`separator`，`close`。

> `item`表示集合中每一个元素进行迭代时的别名，
> `index`指 定一个名字，用于表示在迭代过程中，每次迭代到的位置，
> `open`表示该语句以什么开始，
> `separator`表示在每次进行迭代之间以什么符号作为分隔 符，
> `close`表示以什么结束。

需要注意点：

> 1. 如果传入的是单参数且参数类型是一个`List`的时候，`collection`属性值为`list`
> 2. 如果传入的是单参数且参数类型是一个`array`数组的时候，`collection`的属性值为`array`
> 3. 如果传入的参数是多个的时候，我们就需要把它们封装成一个`Map`了，当然单参数也可以

具体实例：[多行数据插入的使用](#3.2.1 多行数据插入的使用)

---

Author：`icelo`

Date：2020年11月28日

博客地址：[冰洛博客](https://www.icelo.cn)