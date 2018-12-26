# dubbo入门

## dubbo是什么？

Dubbo是一个分布式服务框架，致力于提供高性能和透明化的RPC远程服务调用方案，以及SOA服务治理方案。简单的说，dubbo就是个服务框架，如果没有分布式的需求,其实是不需要用的，只有在分布式的时候，才需要dubbo这样的分布式服务框架的需求，并且本质上是个服务调用的东东。如果经常用WebService的小伙伴，应该对这个东西就比较快熟悉了。

现在托管于github.

网址:http://dubbo.apache.org/en-us/
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181225214707296.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2phdmFib3l3ZW5n,size_16,color_FFFFFF,t_70)
## 它能做什么？

1.透明化的远程方法调用，就像调用本地方法一样调用远程方法，只需简单配置，没有任何API侵入。

2.软负载均衡及容错机制，可在内网替代F5等硬件负载均衡器，降低成本，减少单点。

3.服务自动注册与发现，不再需要写死服务提供方地址，注册中心基于接口名查询服务提供者的IP地址，并且能够平滑添加或删除服务提供者。 

## dubbo入门

首先要准备以下几个东西：

1.zookeeper-3.4.8.tar.gz
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181225214748831.png)
2.dubbo-dubbo-2.5.3.zip	| 	hessian-lite.zip	| 	opensesame.zip
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181225214805164.png)
### 步骤(Zookeeper)

说明：为什么安装zookeeper？在dubbo中扮演注册中心的角色。

##### 1.先解压zookeeper-3.4.8.tar.gz并安装

> 解压到目录后，将conf文件下的zoo.sample.cfg改成zoo.cfg。

> 在一个位置创建一个文件夹放置data文件的。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181225214816829.png)

> 然后把zoo.cfg中修改一行配置指向这个data文件夹
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181225214829674.png)

##### 2.启动zookeeper。在bin目录下双击zkServer.cmd
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181225214842404.png)

##### 3.默认zookeeper的端口号：2181



### 步骤(Dubbo源码构建)

把压缩包dubbo-dubbo-2.5.3.zip	| 	hessian-lite.zip	| 	opensesame.zip进行解压，是三个maven工程

##### 1.先打开hessian-lite和opensesame项目，直接点击Idea中的install 安装到本地仓库，或者命令mvn:install
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181225214854611.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2phdmFib3l3ZW5n,size_16,color_FFFFFF,t_70)

##### 2.在本地仓库中安装好了hessian-lite和opensesame之后才可以安装dubbo-2.5.3.zip，因为前两个是后面的依赖。打开dubbo-dubbo-2.5.3项目

![在这里插入图片描述](https://img-blog.csdnimg.cn/20181225214920580.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2phdmFib3l3ZW5n,size_16,color_FFFFFF,t_70)

用Eclipse的童鞋可能是

![在这里插入图片描述](https://img-blog.csdnimg.cn/20181225214933199.png)

##### 3.先Clean，在Package就能得到这两个jar包。当然在maven中直接引用依赖。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181225214942860.png)

##### 4.创建工程

首先先创建一个普通的Maven工程B，作为服务提供方，将服务暴露出去。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20181225214953256.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2phdmFib3l3ZW5n,size_16,color_FFFFFF,t_70)

依赖：

```xml-dtd
<dependencies>
    <!--springmvc-->
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-webmvc</artifactId>
      <version>4.1.3.RELEASE</version>
    </dependency>
    <!--dubbo-->
    <dependency>
      <groupId>com.alibaba</groupId>
      <artifactId>dubbo</artifactId>
      <version>2.5.3</version>
      <exclusions>
        <exclusion>
          <groupId>org.springframework</groupId>
          <artifactId>spring</artifactId>
        </exclusion>
      </exclusions>
    </dependency>
    <!--slf-->
    <dependency>
      <groupId>org.slf4j</groupId>
      <artifactId>slf4j-log4j12</artifactId>
      <version>1.7.12</version>
    </dependency>
    <!--zookeeper-->
    <dependency>
      <groupId>org.apache.zookeeper</groupId>
      <artifactId>zookeeper</artifactId>
      <version>3.3.3</version>
    </dependency>
    <!--zkclient-->
    <dependency>
      <groupId>com.github.sgroschupf</groupId>
      <artifactId>zkclient</artifactId>
      <version>0.1</version>
    </dependency>
  </dependencies>
```

创建User实体类

```java
package com.weng.dubbo.entity;

import java.io.Serializable;
/**
*@discrption:User实体类
*@user:wengzhongjie
*@createTime:2018-12-25 20:40
*/
public class User implements Serializable {
    private String uid;
    private String username;
    private int age;

    public String getUid() {
        return uid;
    }

    public void setUid(String uid) {
        this.uid = uid;
    }

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }
}

```

创建UserService接口和他的实现。

```java
package com.weng.dubbo.service;

import com.weng.dubbo.entity.User;

import java.util.List;

/**
*@discrption:UserService接口
*@user:wengzhongjie
*@createTime:2018-12-25 20:42
*/
public interface UserService {
    /**
     * 查询所有用户
     * @return
     */
    List<User> queryAll();
}
```

UserService实现类UserServiceImpl,这里为了省去连接数据库的麻烦，直接模拟数据返回

```java
package com.weng.dubbo.service.impl;

import com.weng.dubbo.entity.User;
import com.weng.dubbo.service.UserService;

import java.util.ArrayList;
import java.util.List;

public class UserServiceImpl implements UserService {
    @Override
    public List<User> queryAll() {
        List<User> users = new ArrayList<>();
        for (int i = 0; i <10; i++) {
            User user = new User();
            user.setUsername("name"+i);
            user.setUid(i + "");
            user.setAge(i);
            users.add(user);
        }
        return users;
    }
}

```

在Resources目录下创建以下两个文件

1.dubbo-provider.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:tx="http://www.springframework.org/schema/tx"
       xmlns:dubbo="http://code.alibabatech.com/schema/dubbo"
       xsi:schemaLocation="
        http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd
        http://www.springframework.org/schema/tx
        http://www.springframework.org/schema/tx/spring-tx.xsd
        http://www.springframework.org/schema/aop
        http://www.springframework.org/schema/aop/spring-aop.xsd
http://code.alibabatech.com/schema/dubbo http://code.alibabatech.com/schema/dubbo/dubbo.xsd">

        <!--服务提供方-->
        <dubbo:application name="dubbo-b-server"/>
        <!--注册中心-->
        <dubbo:registry address="127.0.0.1:2181" client="zkclient"/>
        <!--用dubbo的协议暴露-->
        <dubbo:protocol name="dubbo" port="20881"/>
        <!--将接口暴露出去-->
        <dubbo:service interface="com.weng.dubbo.service.UserService" ref="userService"></dubbo:service>
        <!--userService的实现类-->
        <bean id="userService" class="com.weng.dubbo.service.impl.UserServiceImpl"></bean>
</beans>
```

2.log4j.properties

```properties
log4j.rootLogger=INFO,A1
log4j.appender.A1=org.apache.log4j.ConsoleAppender
log4j.appender.A1.layout=org.apache.log4j.PatternLayout
log4j.appender.A1.layout.ConversionPattern=%-d{yyyy-MM-dd HH:mm:ss,SSS} [%t] [%c]-[%p] %m%n
```

3.web.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://java.sun.com/xml/ns/javaee" xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd" id="WebApp_ID" version="2.5">

  <display-name>dubbob</display-name>
  <context-param>
    <param-name>contextConfigLocation</param-name>
    <param-value>classpath:dubbo-*.xml</param-value>
  </context-param>
  <!--springContext的监听器-->
  <listener>
    <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
  </listener>
</web-app>

```

启动B工程。这里就不需要我去描述怎么启动maven工程把，装插件tomcat也行，装在自己tomcat上也行。

注意：zookeeper记得开起来，那是注册中心哟，要把服务注册到里面的。以下就算完事，控制台没报错就行。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20181225215023372.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/2018122521503913.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2phdmFib3l3ZW5n,size_16,color_FFFFFF,t_70)

接下来创建A工程，作为消费者，消费服务，这次直接用web工程，用简单的java工程即可。

依赖依旧是老配方，多加一个spring-test用于测试

```xml
<dependencies>
    <!--springmvc-->
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-webmvc</artifactId>
      <version>4.1.3.RELEASE</version>
    </dependency>
    <!--dubbo-->
    <dependency>
      <groupId>com.alibaba</groupId>
      <artifactId>dubbo</artifactId>
      <version>2.5.3</version>
      <exclusions>
        <exclusion>
          <groupId>org.springframework</groupId>
          <artifactId>spring</artifactId>
        </exclusion>
      </exclusions>
    </dependency>
    <!--slf-->
    <dependency>
      <groupId>org.slf4j</groupId>
      <artifactId>slf4j-log4j12</artifactId>
      <version>1.7.12</version>
    </dependency>
    <!--zookeeper-->
    <dependency>
      <groupId>org.apache.zookeeper</groupId>
      <artifactId>zookeeper</artifactId>
      <version>3.3.3</version>
    </dependency>
    <!--zkclient-->
    <dependency>
      <groupId>com.github.sgroschupf</groupId>
      <artifactId>zkclient</artifactId>
      <version>0.1</version>
    </dependency>
    <!--spring-test-->
    <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-test</artifactId>
            <version>4.1.3.RELEASE</version>
        </dependency>
     <!--junit-->
    <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
        </dependency>
  </dependencies>
```

在Resources目录下创建这两个文件

1.dubbo-consumer.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:tx="http://www.springframework.org/schema/tx"
       xmlns:dubbo="http://code.alibabatech.com/schema/dubbo"
       xsi:schemaLocation="
        http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd
        http://www.springframework.org/schema/tx
        http://www.springframework.org/schema/tx/spring-tx.xsd
        http://www.springframework.org/schema/aop
        http://www.springframework.org/schema/aop/spring-aop.xsd
http://code.alibabatech.com/schema/dubbo http://code.alibabatech.com/schema/dubbo/dubbo.xsd">
    <!--服务消费方-->
    <dubbo:application name="dubbo-a-consumer"/>
    <!--注册中心-->
    <dubbo:registry address="zookeeper://127.0.0.1:2181" client="zkclient"/>
    <!--消费的服务-->
    <dubbo:reference interface="com.weng.dubbo.service.UserService" id="userService"/>
</beans>
```

2.log4j.properties

```properties
log4j.rootLogger=DEBUG,A1
log4j.appender.A1=org.apache.log4j.ConsoleAppender
log4j.appender.A1.layout=org.apache.log4j.PatternLayout
log4j.appender.A1.layout.ConversionPattern=%-d{yyyy-MM-dd HH:mm:ss,SSS} [%t] [%c]-[%p] %m%n

```

把刚才在b工程中的UserService和User拷贝过来到A工程，UserServiceImpl记住不要拷贝，不然还玩什么远程调用。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20181225215057636.png)



写一个测试单元类，调用UserService接口获取数据

```java
package com.weng.dubbo.test;

import com.weng.dubbo.entity.User;
import com.weng.dubbo.service.UserService;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.AbstractJUnit4SpringContextTests;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;

import javax.annotation.Resource;
import java.util.List;

/**
*@discrption:测试单元类，消费B工程暴露出的服务
*@user:weng
*@createTime:2018-12-25 21:29
*/
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations={"classpath*:dubbo-*.xml"})
public class TestDubbo extends AbstractJUnit4SpringContextTests {
    
    @Resource
    private UserService userService;
    
    @Test
    public void testUserService() {
        List<User> users=userService.queryAll();
        for (User user : users) {
            System.out.println(user.getUsername()+"---"+user.getAge());
        }
    }
}

```

结果

![在这里插入图片描述](https://img-blog.csdnimg.cn/20181225215110460.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2phdmFib3l3ZW5n,size_16,color_FFFFFF,t_70)

可能有些人会纳闷，为什么A工程中还要在写B工程中的User和UserService岂不是很麻烦，B工程肯定是一个团队在开发，A工程是另一个团队在开发，难道还要再写一次。其实在开发中，B工程中的这个要暴露的服务的接口会单独抽离成一个工程，到时候A只要引用依赖就可以了，B同样也引用依赖，这样就A就不用再写了。



最后结语：

本人dubbo也只是略知皮毛，只是入门，望大神们看到不喜勿喷哟。
