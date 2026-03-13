# Spring Boot 快速入门

Spring Boot 是基于 Spring 框架的开发框架，它简化了 Spring 应用的初始搭建和开发过程。

## 快速开始

### 1. 创建项目

使用 Spring Initializr 创建项目：

- 访问 https://start.spring.io/
- 选择项目配置：
  - Project: Maven
  - Language: Java
  - Spring Boot: 3.x
  - Group: com.example
  - Artifact: demo
- 添加依赖：
  - Spring Web
  - Spring Data JPA
  - MySQL Driver

### 2. 项目结构

```
src/
├── main/
│   ├── java/com/example/demo/
│   │   ├── DemoApplication.java      # 主程序
│   │   ├── controller/               # 控制器
│   │   ├── service/                  # 服务层
│   │   ├── repository/               # 数据访问层
│   │   └── model/                    # 实体类
│   └── resources/
│       ├── application.properties   # 配置文件
│       └── static/                   # 静态资源
└── test/                             # 测试代码
```

## 核心概念

### 1. 自动配置

Spring Boot 根据依赖自动配置应用：

```java
@SpringBootApplication
public class DemoApplication {
    public static void main(String[] args) {
        SpringApplication.run(DemoApplication.class, args);
    }
}
```

### 2. RESTful API

创建 REST 控制器：

```java
@RestController
@RequestMapping("/api/users")
public class UserController {
    
    @Autowired
    private UserService userService;
    
    @GetMapping
    public List<User> getAllUsers() {
        return userService.findAll();
    }
    
    @GetMapping("/{id}")
    public User getUserById(@PathVariable Long id) {
        return userService.findById(id);
    }
    
    @PostMapping
    public User createUser(@RequestBody User user) {
        return userService.save(user);
    }
    
    @PutMapping("/{id}")
    public User updateUser(@PathVariable Long id, @RequestBody User user) {
        user.setId(id);
        return userService.save(user);
    }
    
    @DeleteMapping("/{id}")
    public void deleteUser(@PathVariable Long id) {
        userService.deleteById(id);
    }
}
```

### 3. 数据访问

使用 Spring Data JPA：

```java
@Entity
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    private String name;
    private String email;
    
    // getters and setters
}

public interface UserRepository extends JpaRepository<User, Long> {
    List<User> findByEmail(String email);
    List<User> findByNameContaining(String name);
}
```

### 4. 服务层

```java
@Service
@Transactional
public class UserService {
    
    @Autowired
    private UserRepository userRepository;
    
    public List<User> findAll() {
        return userRepository.findAll();
    }
    
    public User findById(Long id) {
        return userRepository.findById(id)
            .orElseThrow(() -> new RuntimeException("User not found"));
    }
    
    public User save(User user) {
        return userRepository.save(user);
    }
    
    public void deleteById(Long id) {
        userRepository.deleteById(id);
    }
}
```

## 配置文件

### application.properties

```properties
# 服务器端口
server.port=8080

# 数据库配置
spring.datasource.url=jdbc:mysql://localhost:3306/mydb
spring.datasource.username=root
spring.datasource.password=password
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver

# JPA 配置
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.format_sql=true
```

### application.yml

```yaml
server:
  port: 8080

spring:
  datasource:
    url: jdbc:mysql://localhost:3306/mydb
    username: root
    password: password
    driver-class-name: com.mysql.cj.jdbc.Driver
  
  jpa:
    hibernate:
      ddl-auto: update
    show-sql: true
    properties:
      hibernate:
        format_sql: true
```

## 常用注解

| 注解 | 说明 |
|------|------|
| `@SpringBootApplication` | 启动类注解 |
| `@RestController` | REST 控制器 |
| `@RequestMapping` | 请求映射 |
| `@GetMapping` | GET 请求 |
| `@PostMapping` | POST 请求 |
| `@PutMapping` | PUT 请求 |
| `@DeleteMapping` | DELETE 请求 |
| `@Autowired` | 依赖注入 |
| `@Service` | 服务层 |
| `@Repository` | 数据访问层 |
| `@Entity` | JPA 实体 |
| `@Transactional` | 事务管理 |

## 总结

Spring Boot 通过约定优于配置的理念，极大地简化了 Spring 应用的开发。掌握以上基础知识，就可以快速构建生产级的应用程序。

## 参考资料

- [Spring Boot 官方文档](https://spring.io/projects/spring-boot)
- [Spring Data JPA 文档](https://spring.io/projects/spring-data-jpa)

---

*最后更新时间: {docsify-updated}*
