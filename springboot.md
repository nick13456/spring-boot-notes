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











