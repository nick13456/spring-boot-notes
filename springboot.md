# 一、入门

## 简化部署

```xml

<!--用于把项目打包成jar包的插件-->
<build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
```

## 父项目: spring-boot-starter-parent

```xml
<parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.6.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>

<!-- 他的父项目是↓↓↓ : spring-boot-dependencies -->
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-dependencies</artifactId>
    <version>2.1.6.RELEASE</version>
    <relativePath>../../spring-boot-dependencies</relativePath>
  </parent>

```

在spring-boot-dependencies中配置了很多需要的jar包的版本，因此再导入相同的jar包是不需要再配置版本了。如果是其中不包括的jar包，则需要配置版本。

## 编写controller

```java
package com.autospring.atspring.controllers;

import com.autospring.atspring.dao.AccountDao;
import com.autospring.atspring.domain.Account;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;
import java.util.List;

@RestController  /*相当于@ResponseBody + @Controller*/
@ConfigurationProperties(prefix = "nick")
public class HelloController {
    @RequestMapping("/hello")
    public String hello(){
        return "hello springboot";
    }
}
```

@RestController  /*相当于@ResponseBody + @Controller*/ 

@ReponseBody，将方法返回值直接显示在浏览器上

## 文件夹说明

![](C:\Users\nick\Desktop\spring-boot-notes\images\Snipaste_2019-07-16_18-35-49.png)

<u>templates:</u> spring boot默认是嵌入式tomcat，不支持jsp页面，因此需要使用模板引擎(freemaker、thymeleaf等).

# 二、配置

## yaml格式以及注入属性

支持的文件配置: 1,yml 	2,yaml 	3,properties

````xml
<resource>
        <filtering>true</filtering>
        <directory>${basedir}/src/main/resources</directory>
        <includes>
            <!--支持一下三种两种文件配置，最下面的会覆盖上面的-->
          <include>**/application*.yml</include>
          <include>**/application*.yaml</include>
          <include>**/application*.properties</include>
        </includes>
</resource>
````

增强配置yml和properties提示属性的处理器：

````xml
 <dependency>
     <groupId>org.springframework.boot</groupId>
     <artifactId>spring-boot-configuration-processor</artifactId>
</dependency>
````

yml: 格式

````yml
#对象↓
spring:
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql:///test?serverTimezone=UTC
    username: root
    password: luohepeng
#数组↓    
people:
    - nick
    - joe
    - hash
    - bolt
    
````

## @ConfigurationProperties

利用yaml的配置和@ConfigurationProperties(prefix = "nick")给对象注入属性:

````java
package com.autospring.atspring.domain;
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.stereotype.Component;
import java.util.Arrays;
import java.util.Map;

@Component
@ConfigurationProperties(prefix = "nick")
public class Nick {
    private String lastName;
    private Integer age;
    private Dog xiaobai;
    private Map<Integer,String> map;
    private Integer[] nums;

    public String getLastName() {
        return lastName;
    }

    public void setLastName(String lastName) {
        this.lastName = lastName;
    }

    public Integer getAge() {
        return age;
    }

    public void setAge(Integer age) {
        this.age = age;
    }

    public Dog getXiaobai() {
        return xiaobai;
    }

    public void setXiaobai(Dog xiaobai) {
        this.xiaobai = xiaobai;
    }

    public Map<Integer, String> getMap() {
        return map;
    }

    public void setMap(Map<Integer, String> map) {
        this.map = map;
    }

    public Integer[] getNums() {
        return nums;
    }

    public void setNums(Integer[] nums) {
        this.nums = nums;
    }

    @Override
    public String toString() {
        return "Nick{" +
                "lastName='" + lastName + '\'' +
                ", age=" + age +
                ", xiaobai=" + xiaobai +
                ", map=" + map +
                ", nums=" + Arrays.toString(nums) +
                '}';
    }
}

````

### yaml配置:

````yaml
nick:
  last-name: luo
  age: 18
  nums: [3,4,6,8,0]
  map:
    1: book
    2: phone
  xiaobai:
    name: xiaobai
    age : 3
````

### properties配置:

```properties
nick.last-name=luo
nick.age=17
nick.xiaobai.name=xiaobai
nick.xiaobai.age=5
nick.map.1=java
nick.map.2=js
nick.nums=1,4,6,8,9
```

## @ConfigurationProperties @Value区别

|                      | @ConfigurationProperties | @Value     |
| -------------------- | ------------------------ | ---------- |
| 功能                 | 批量注入配置文件中的属性 | 一个个指定 |
| 松散绑定（松散语法） | 支持                     | 不支持     |
| SpEL                 | 不支持                   | 支持       |
| JSR303数据校验       | 支持                     | 不支持     |
| 复杂类型封装(Map等)  | 支持                     | 不支持     |

所谓松散语法也就是属性命名规则:（Relaxed binding）以下写法在松散绑定语法中都一样

```
- person.firstName:使用标准方式
- person.first-name:大写用-         
- person.first_name:大写用_
- PERSON_FIRST_NAME: 系统属性推荐使用这种写法
```

#### JSR303校验:

```java
import javax.validation.constraints.NotNull;

import org.hibernate.validator.constraints.Email;
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.validation.annotation.Validated;

@ConfigurationProperties
@Validated		// 开启属性注入校验
public class Properties {

    @NotNull		// 校验必须是非空
    private String userName;
    
    @Email			// 校验必须是email格式 ****@**.**
    private String email;
    
}
```

#### SpEl

@ConfigurationProperties不支持EL表达式

application.properties文件中有字段 person.userAge=12 

```
userAge = 12   //可以
userAge = #{2*6}  //EL表达式不支持
```

#### @Value支持EL表达式

```java
`@Value(userAge=#{2*6}) ``//支持EL表达式`
```

## @PropertySource和@ImportSource

#### @PropertySource

nick.properties：

```properties
nick.last-name=luo
nick.age=17
nick.xiaobai.name=xiaobai
nick.xiaobai.age=5
nick.map.1=java
nick.map.2=js
nick.nums=1,4,6,8,9
```

Nick类:	在需要注入的类上添加@PropertySource("classpath:***")

```java
package com.autospring.atspring.domain;

import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.stereotype.Component;
import java.util.Arrays;
import java.util.Map;

@Component
@PropertySource("classpath:nick.properties")	// 添加在需要注入的类上
@ConfigurationProperties(prefix = "nick")	// 注入nick.properties中的属性
public class Nick {
    private String lastName;
    private Integer age;
    private Dog xiaobai;
    private Map<Integer,String> map;
    private Integer[] nums;

    public String getLastName() {
        return lastName;
    }

    public void setLastName(String lastName) {
        this.lastName = lastName;
    }

    public Integer getAge() {
        return age;
    }

    public void setAge(Integer age) {
        this.age = age;
    }

    public Dog getXiaobai() {
        return xiaobai;
    }

    public void setXiaobai(Dog xiaobai) {
        this.xiaobai = xiaobai;
    }

    public Map<Integer, String> getMap() {
        return map;
    }

    public void setMap(Map<Integer, String> map) {
        this.map = map;
    }

    public Integer[] getNums() {
        return nums;
    }

    public void setNums(Integer[] nums) {
        this.nums = nums;
    }

    @Override
    public String toString() {
        return "Nick{" +
                "lastName='" + lastName + '\'' +
                ", age=" + age +
                ", xiaobai=" + xiaobai +
                ", map=" + map +
                ", nums=" + Arrays.toString(nums) +
                '}';
    }
}

```

#### @ImportSource

```java
package com.autospring.atspring;

import com.autospring.atspring.config.AppConfig;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.SpringBootConfiguration;
import org.springframework.boot.autoconfigure.EnableAutoConfiguration;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.*;
import org.springframework.jdbc.datasource.DriverManagerDataSource;
import javax.sql.DataSource;
import java.lang.annotation.Documented;
import java.lang.annotation.Inherited;

@SpringBootApplication
@PropertySource("classpath:nick.properties")
//@ImportResource(locations = "classpath:bean.xml")		// 用来导入spring的xml配置,现在不用了
public class AutospringApplication {
    public static void main(String[] args) {
        SpringApplication.run(AutospringApplication.class, args);
    }

}

```

#### 直接使用@Configuration来直接写配置类:

```java
package com.autospring.atspring.config;

import org.springframework.context.annotation.Configuration;

@Configuration				// 直接表示该类是配置类，不需要在springboot配置类中用@ImportResource
public class AppConfig {

}

```

## 配置文件的占位符

```properties
nick.last-name=luo
nick.age=${random.int(100)}
nick.xiaobai.name=${nick.last-name}_xiaobai
#给未定义的 xiaobai.birth 定一个默认值↓
nick.xiaobai.age=${xiaobai.birth:5}			
nick.map.1=java
nick.map.2=js
nick.nums=1,4,6,8,9
```

## profile

指定springboot使用的配置文件：application-nick.yml或application-nick.yml

springboot的配置文件明明规则: application-\*\*.yml 或application-\*\*.properties

properties配置:

```properties
spring.profiles.active=nick
```

或yml配置:

```yaml
spring:
  profiles:
    active: nick
```

### yml文档块配置:

```yml
spring:
  profiles:
    active: dev		#选择下面哪一个文档快
---
server:
  port: 8083
spring:
  profiles: dev
---
server:
  port: 8084
spring:
  profiles: prod
---
server:
  port: 8085
spring:
  profiles: test
```

### 命令行配置:

![Snipaste_2019-07-17_14-09-05](C:\Users\nick\Desktop\spring-boot-notes\images\Snipaste_2019-07-17_14-09-05.png)

--spring.profiles.active:prod

或者:

![Snipaste_2019-07-17_14-16-11](C:\Users\nick\Desktop\spring-boot-notes\images\Snipaste_2019-07-17_14-16-11.png)

## 配置文件的位置

```xml
-file:/config/
-file:../
-classpath:/config/
-classpath:/
```

都可以存放配置文件，优先级从高到低。相同的配置，高的优先级会覆盖低的优先级。不同的配置会形成互补配置

```
--spring.config.location=G:/appcalication.properties。改名了用在服务器终端在启动部署服务的时候。↓
```

![1563346364782](C:\Users\nick\Desktop\spring-boot-notes\images\1563346364782.png)

## 常用springboot属性配置:

参考spring官方的：[Common application properties](https://docs.spring.io/spring-boot/docs/2.1.6.RELEASE/reference/html/common-application-properties.html)

```yaml
debug: true
```

开启debug，来查看哪些类已经自动配置好了(positive matches)，哪些没有配置好(nagetive matches)。

## 配置日志logback+slf4j

slf4j:

```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class HelloWorld {
  public static void main(String[] args) {
    Logger logger = LoggerFactory.getLogger(HelloWorld.class);
    logger.info("Hello World");
  }
}
```

日志的配置文件还是写实现的框架的配置