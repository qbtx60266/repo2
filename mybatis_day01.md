[TOP]

# Mybatis第一天

## 概念
JavaSE  :Java基础
前端    :能够看得懂前端的代码
Javaweb :JavaEE的核心基础
框架    : SSM
项目    : 品优购 

怎么去学习框架
1. API的学习 配置(核心配置文件:连接数据库的环境信息 指定映射配置文件的位置 映射配置文件:)  注解(最低要求  必须要掌握)
2. 学习框架的高级的编程思想  设计原则(了解)

目的 :能够使用框架进行开发

什么是框架
设计模式: 解决某一类问题的最优方案 没有具体的代码实现 只是一种技术或者是一种思想
开发模式: MVC 为了优化代码的一种编程思想
工具类: 代码的封装 
框架: 很大的工具类  半成品  我们可以基于框架继续开发
使用框架的优点:　提高开发效率
使用框架的缺点： 执行效率会降低　可能会有隐藏的Bug


三层架构
web层
service层 
持久层

使用mybatis只需要关注sql本身  结果集的封装
ORM思想
user表           user实体类
username         username
age              age
telephone        tel

如果不一致怎么办  没关系  你可以告诉框架 表的字段和实体类的属性的对应关系


## Mybatis使用

1. 使用资料中提供的sql文件创建数据库及表和表数据
2. 创建项目使用Maven 创建java或者javaweb都可以
3. 补充pom.xml
   ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <project xmlns="http://maven.apache.org/POM/4.0.0"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
        <modelVersion>4.0.0</modelVersion>

        <groupId>com.itheima</groupId>
        <artifactId>day01_01_mybatis</artifactId>
        <version>1.0-SNAPSHOT</version>
        <packaging>jar</packaging>

        <dependencies>
            <dependency>
                <groupId>org.mybatis</groupId>
                <artifactId>mybatis</artifactId>
                <version>3.4.5</version>
            </dependency>
            <dependency>
                <groupId>mysql</groupId>
                <artifactId>mysql-connector-java</artifactId>
                <version>5.1.6</version>
            </dependency>
            <dependency>
                <groupId>log4j</groupId>
                <artifactId>log4j</artifactId>
                <version>1.2.12</version>
            </dependency>
            <dependency>
                <groupId>junit</groupId>
                <artifactId>junit</artifactId>
                <version>4.10</version>
                <scope>test</scope>
            </dependency>
        </dependencies>
    </project>
   ```
4. 创建包结构 dao  domain 
5. 创建类 com.itheima.domain.user  com.itheima.dao.IUserDao 
6. 创建mybatis的核心配置文件
   ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE configuration
            PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
            "http://mybatis.org/dtd/mybatis-3-config.dtd">
    <!-- mybatis的主配置文件 -->
    <configuration>
        <!-- 配置环境 -->
        <environments default="mysql">
            <!-- 配置mysql的环境-->
            <environment id="mysql">
                <!-- 配置事务的类型-->
                <transactionManager type="JDBC"></transactionManager>
                <!-- 配置数据源（连接池） -->
                <dataSource type="POOLED">
                    <!-- 配置连接数据库的4个基本信息 -->
                    <property name="driver" value="com.mysql.jdbc.Driver"/>
                    <property name="url" value="jdbc:mysql://localhost:3306/mybatis"/>
                    <property name="username" value="root"/>
                    <property name="password" value="root"/>
                </dataSource>
            </environment>
        </environments>

        <!-- 指定映射配置文件的位置，映射配置文件指的是每个dao独立的配置文件 -->
        <mappers>
            <mapper resource="com/itheima/dao/IUserDao.xml"/>
        </mappers>
    </configuration>
   ```
7. 创建映射文件
   ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE mapper
            PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
            "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
    <mapper namespace="com.itheima.dao.IUserDao">
        <!--配置查询所有-->
        <select id="findAll" resultType="com.itheima.domain.User">
            select * from user
        </select>
    </mapper>
   ```
8. 使用mybatis
   ```java
   public static void main(String[] args)throws Exception {
        //1.读取配置文件
        InputStream in = Resources.getResourceAsStream("SqlMapConfig.xml");
        //2.创建SqlSessionFactory工厂
        SqlSessionFactoryBuilder builder = new SqlSessionFactoryBuilder();
        SqlSessionFactory factory = builder.build(in);
        //3.使用工厂生产SqlSession对象
        SqlSession session = factory.openSession();
        //4.使用SqlSession创建Dao接口的代理对象
        IUserDao userDao = session.getMapper(IUserDao.class);
        //5.使用代理对象执行方法
        List<User> users = userDao.findAll();
        for(User user : users){
            System.out.println(user);
        }
        //6.释放资源
        session.close();
        in.close();
    }
   ```