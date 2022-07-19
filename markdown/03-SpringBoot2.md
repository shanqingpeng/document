### 一、快速开始

#### 1、SpringBoot简介



#### 2、系统要求

- Spring Boot 2.3.4.RELEASE 要求 Java8（兼容Java14）
- 要求Spring Framework 5.2.9或更高版本
- Maven 3.3+



#### 3、Maven设置

```xml
<!-- 配置阿里云镜像 -->
<mirrors>
	<mirror>  
		<id>nexus-aliyun</id>  
		<mirrorOf>central</mirrorOf>    
		<name>Nexus aliyun</name>  
		<url>http://maven.aliyun.com/nexus/content/groups/public</url>  
	</mirror> 
</mirrors>

<!-- 配置maven使用jdk1.8进行编译 -->
<profiles>
    <profile>
	  <id>jdk-1.8</id>
	  
	  <activation>
		<jdk>1.8</jdk>
		<activeByDefault>true</activeByDefault>
	  </activation>
	  
	  <properties>			
		<maven.compiler.source>1.8</maven.compiler.source>
		<maven.compiler.target>1.8</maven.compiler.target>
		<maven.compiler.compilerVersion>1.8</maven.compiler.compilerVersion>
	  </properties>
	</profile>	 
</profiles>
```



#### 4、HelloWorld

1. 引入依赖

```xml
<!--SpringBoot父项目-->
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.6.8</version>
</parent>

<dependencies>
    <!--SpringBoot-Web模块-->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
</dependencies>
```

2. 创建主启动类

```java
// 主启动类
@SpringBootApplication   // 表示这是一个SpringBoot应用
public class MainApplication {
    public static void main(String[] args) {
        // 启动应用
        SpringApplication.run(MainApplication.class, args);
    }
}
```

3. 创建Controller

```java
@RestController
public class HelloController {
    @RequestMapping("/hello")
    public String hello() {
        return "Hello World!";
    }
}
```

4. 启动项目：打开浏览器访问：<http://localhost:8080/hello>，就可以看到 Hello World!

5. 创建可执行jar

- 添加插件依赖

```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
        </plugin>
    </plugins>
</build>
```

- 执行打包，在 ```target``` 目录下会生成可执行jar

- 使用```java -jar```命令启动应用

```ruby
D:\code\springboot2>java -jar target/springboot2-1.0-SNAPSHOT.jar

 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::        (v2.3.4.RELEASE)
....... . . .
....... . . . (log output here)
....... . . .
....... Started MainApplication in 8.583 seconds (JVM running for 9.841)
```

- 使用```ctrl + c```退出应用

  

#### 5、依赖管理

1. ````spring-boot-starter-parent```的父项目```spring-boot-dependencies```中声明了开发中常用的依赖的版本号

   ```xml
   <!-- SpringBoot父项目 -->
   <parent>
       <groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-starter-parent</artifactId>
       <version>2.3.4.RELEASE</version>
   </parent>
   
   <!-- SpringBoot父项目的父项目 -->
   <parent>
       <groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-dependencies</artifactId>
       <version>2.3.4.RELEASE</version>
   </parent>
   ```

2. 开发中引入```spring-boot-dependencies```已经声明版本号的依赖时，不需要声明版本号也可以使用，例如：

   ```xml
   <!--SpringBoot-Web模块-->
   <dependency>
       <groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-starter-web</artifactId>
       <!-- 可以不写版本号 -->
   </dependency>
   
   <!--mysql驱动-->
   <dependency>
       <groupId>mysql</groupId>
       <artifactId>mysql-connector-java</artifactId>
       <!-- 可以不写版本号 -->
   </dependency>
   ```

3. 如果引入依赖时，不想使用父项目的版本号，可以自己声明版本，例如：

   ```xml
   <!-- 自己声明依赖的版本号 -->
   <properties>
       <mysql.version>5.1.43</mysql.version>
   </properties>
   
   <dependencies>
       <!--mysql驱动-->
       <dependency>
           <groupId>mysql</groupId>
           <artifactId>mysql-connector-java</artifactId>
           <version>${mysql.version}</version>
       </dependency>
   </dependencies>
   ```

   #### 6、Starters

   1. ```Starters ```称为场景启动器，maven中引入一个```Starter```的依赖，这个场景需要的常规依赖就会自动引入，如：

      ```xml
      <!--引入SpringBoot-Web场景启动器-->
      <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-web</artifactId>
      </dependency>
      
      <!-- web场景需要的常规依赖就会自动引入 -->
      spring-beans-5.2.9.RELEASE.jar
      spring-context-5.2.9.RELEASE.jar
      spring-core-5.2.9.RELEASE.jar
      spring-expression-5.2.9.RELEASE.jar
      spring-web-5.2.9.RELEASE.jar
      spring-webmvc-5.2.9.RELEASE.jar
      ...
      ```

   2. Spring 官方的 starters 命名规则是：```spring-boot-starter-*```，例如：

      ```java
      spring-boot-starter-web
      spring-boot-starter-data-redis
      spring-boot-starter-data-jdbc
      ```

   3. 第三方项目的 starters 命名规则是：```*-spring-boot-starter```，例如：

      ```java
      mybatis-spring-boot-starter
      ```

   4. 所有 starters 都要依赖 ```spring-boot-starter```

      ```xml
      <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter</artifactId>
          <version>2.3.4.RELEASE</version>
          <scope>compile</scope>
      </dependency>
      ```



#### 7、自动配置

1. 引入 Starters 依赖后，SpringBoot会自动配置好该场景对应的常用组件

   ```xml
   <!--SpringBoot-Web模块-->
   <dependency>
       <groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-starter-web</artifactId>
   </dependency>
   ```

2. 自动配置包扫描路径：默认扫描主程序所在包及其子包中所有的组件，可以使用SpringBootApplication注解的scanBasePackages属性修改

   ```java
   // 指定包扫描路径
   @SpringBootApplication(scanBasePackages = {"com.sqp.boot"})
   public class MainApplication {
       public static void main(String[] args) {
           // 启动应用
           SpringApplication.run(MainApplication.class, args);
       }
   }
   ```

3. 配置文件中可以配置的属性，都与对应类中的属性映射，如端口号

   ```properties
   server.port=8888
   ```

   ```java
   @ConfigurationProperties(prefix = "server", ignoreUnknownFields = true)
   public class ServerProperties {
   	/**
   	 * Server HTTP port.
   	 */
   	private Integer port;
   }
   ```

4. 引入了 对应的 starter 之后，该场景的自动配置才会开启

5. 所有的自动配置功能，都在```spring-boot-autoconfigure```中

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-autoconfigure</artifactId>
    <version>2.3.4.RELEASE</version>
    <scope>compile</scope>
</dependency>
```



### 二、底层注解

#### 1、Configuration

#### 2、Import

#### 3、Conditional

#### 4、ImportResource

#### 5、ConfigurationProperties

