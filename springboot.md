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
    -nick
    -joe
    -hash
    -bolt
    
````











