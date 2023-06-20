### 1.安装依赖

```xml
<!-- 集成mybatis-->
<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
    <version>2.1.3</version>
</dependency>
<!-- 集成mysql连接 -->
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>8.0.22</version>
</dependency>
```

### 2.`application.properties`里添加数据库信息

```
spring.datasource.url=local
spring.datasource.username=softwarem
spring.datasource.password=aA!963028938
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
```

### 3.新建domain，mapper文件夹

### 4.在resource中新建mapper文件夹

### 5.在启动文件中加入注解

```java
@MapperScan("com.chou.softwarem.mapper")//扫描mapper
```

### 6.在`application.properties`文件中新增

```
#配置所有mybatis 所有mapper.xml
# /**/ 包含所有子文件夹
mybatis.mapper-locations=classpath:/mapper/**/*.xml
```

