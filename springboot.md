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



