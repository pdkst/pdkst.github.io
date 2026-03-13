# Docker 实践指南

Docker 是一个开源的应用容器引擎，可以轻松地将应用打包到轻量级、可移植的容器中。

## 基础概念

### 核心概念

- **镜像 (Image)**: 只读的应用包，包含运行应用所需的所有内容
- **容器 (Container)**: 镜像的运行实例
- **仓库 (Repository)**: 存储和分发镜像的地方

### 安装 Docker

```bash
# Ubuntu/Debian
sudo apt-get update
sudo apt-get install docker.io

# CentOS/RHEL
sudo yum install docker

# 验证安装
docker --version
docker run hello-world
```

## Docker 命令

### 镜像操作

```bash
# 搜索镜像
docker search nginx

# 拉取镜像
docker pull nginx:latest

# 查看本地镜像
docker images

# 删除镜像
docker rmi nginx:latest

# 构建镜像
docker build -t myapp:1.0 .
```

### 容器操作

```bash
# 运行容器
docker run -d -p 80:80 --name mynginx nginx

# 查看运行中的容器
docker ps

# 查看所有容器
docker ps -a

# 停止容器
docker stop mynginx

# 启动容器
docker start mynginx

# 删除容器
docker rm mynginx

# 进入容器
docker exec -it mynginx /bin/bash

# 查看容器日志
docker logs mynginx

# 查看容器资源使用
docker stats
```

## Dockerfile

### 基本结构

```dockerfile
# 基础镜像
FROM openjdk:17-jdk-slim

# 维护者信息
LABEL maintainer="pdkst <pdkstudio@163.com>"

# 设置工作目录
WORKDIR /app

# 复制文件
COPY target/myapp.jar app.jar

# 设置环境变量
ENV JAVA_OPTS=""

# 暴露端口
EXPOSE 8080

# 运行命令
ENTRYPOINT ["java", "-jar", "app.jar"]
```

### 多阶段构建

```dockerfile
# 构建阶段
FROM maven:3.8-openjdk-17 AS builder
WORKDIR /app
COPY pom.xml .
COPY src ./src
RUN mvn clean package -DskipTests

# 运行阶段
FROM openjdk:17-jre-slim
WORKDIR /app
COPY --from=builder /app/target/myapp.jar app.jar
EXPOSE 8080
ENTRYPOINT ["java", "-jar", "app.jar"]
```

### Spring Boot 应用 Dockerfile

```dockerfile
FROM openjdk:17-jre-slim

LABEL maintainer="pdkst <pdkstudio@163.com>"

WORKDIR /app

COPY target/spring-boot-app.jar app.jar

EXPOSE 8080

ENTRYPOINT ["java", "-Djava.security.egd=file:/dev/./urandom", "-jar", "app.jar"]
```

### MySQL Dockerfile

```dockerfile
FROM mysql:8.0

LABEL maintainer="pdkst <pdkstudio@163.com>"

ENV MYSQL_ROOT_PASSWORD=root123
ENV MYSQL_DATABASE=mydb

COPY init.sql /docker-entrypoint-initdb.d/

EXPOSE 3306
```

## Docker Compose

### 基本用法

```yaml
version: '3.8'

services:
  # MySQL 数据库
  mysql:
    image: mysql:8.0
    container_name: myapp-mysql
    environment:
      MYSQL_ROOT_PASSWORD: root123
      MYSQL_DATABASE: mydb
    ports:
      - "3306:3306"
    volumes:
      - mysql-data:/var/lib/mysql
      - ./init.sql:/docker-entrypoint-initdb.d/init.sql
    networks:
      - myapp-network

  # Redis 缓存
  redis:
    image: redis:alpine
    container_name: myapp-redis
    ports:
      - "6379:6379"
    networks:
      - myapp-network

  # Spring Boot 应用
  app:
    build: .
    container_name: myapp
    ports:
      - "8080:8080"
    environment:
      SPRING_DATASOURCE_URL: jdbc:mysql://mysql:3306/mydb
      SPRING_DATASOURCE_USERNAME: root
      SPRING_DATASOURCE_PASSWORD: root123
      SPRING_REDIS_HOST: redis
      SPRING_REDIS_PORT: 6379
    depends_on:
      - mysql
      - redis
    networks:
      - myapp-network

volumes:
  mysql-data:

networks:
  myapp-network:
    driver: bridge
```

### 使用命令

```bash
# 启动所有服务
docker-compose up -d

# 查看服务状态
docker-compose ps

# 查看日志
docker-compose logs -f app

# 停止所有服务
docker-compose down

# 停止并删除卷
docker-compose down -v

# 重新构建并启动
docker-compose up -d --build
```

## 最佳实践

### 1. 最小化镜像大小

```dockerfile
# 使用 alpine 基础镜像
FROM openjdk:17-jre-alpine

# 清理不必要的文件
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*
```

### 2. 利用构建缓存

```dockerfile
# 先复制依赖文件
COPY pom.xml .
RUN mvn dependency:go-offline

# 再复制源代码
COPY src ./src
```

### 3. 使用 .dockerignore

```
.git
.gitignore
target
*.log
.idea
.vscode
```

### 4. 多阶段构建

```dockerfile
# 构建阶段
FROM node:16 AS build
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

# 生产阶段
FROM nginx:alpine
COPY --from=build /app/dist /usr/share/nginx/html
EXPOSE 80
```

### 5. 健康检查

```dockerfile
HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
  CMD curl -f http://localhost:8080/actuator/health || exit 1
```

## 实用技巧

### 查看容器资源使用

```bash
# 实时查看
docker stats

# 查看特定容器
docker stats mynginx

# 查看历史统计
docker stats --no-stream
```

### 容器数据备份

```bash
# 从容器复制文件
docker cp mynginx:/etc/nginx/nginx.conf ./nginx.conf

# 将文件复制到容器
docker cp ./nginx.conf mynginx:/etc/nginx/nginx.conf

# 备份卷
docker run --rm -v my-volume:/data -v $(pwd):/backup \
  alpine tar czf /backup/backup.tar.gz /data
```

### 网络管理

```bash
# 创建网络
docker network create my-network

# 连接容器到网络
docker network connect my-network mynginx

# 查看网络
docker network ls
docker network inspect my-network

# 断开网络
docker network disconnect my-network mynginx

# 删除网络
docker network rm my-network
```

### 日志管理

```bash
# 查看日志
docker logs mynginx

# 实时跟踪日志
docker logs -f mynginx

# 显示最后 100 行
docker logs --tail 100 mynginx

# 显示时间戳
docker logs -t mynginx

# 限制日志大小（在 daemon.json 配置）
{
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "10m",
    "max-file": "3"
  }
}
```

## 常见问题

### 1. 容器无法访问宿主机服务

```bash
# 使用 host.docker.internal
docker run --add-host=host.docker.internal:host-gateway myapp

# 或使用 host 网络模式
docker run --network=host myapp
```

### 2. 容器时间不同步

```bash
# 挂载时区
docker run -v /etc/localtime:/etc/localtime:ro myapp

# 或设置环境变量
docker run -e TZ=Asia/Shanghai myapp
```

### 3. 容器磁盘空间不足

```bash
# 清理未使用的资源
docker system prune -a

# 清理所有未使用的镜像
docker image prune -a

# 清理未使用的卷
docker volume prune
```

## 参考资料

- [Docker 官方文档](https://docs.docker.com/)
- [Docker Hub](https://hub.docker.com/)
- [Docker Compose 文档](https://docs.docker.com/compose/)

---

*最后更新时间: {docsify-updated}*