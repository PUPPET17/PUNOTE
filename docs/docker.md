# 常见 Docker 指令

## 部署Springboot应用（Dockerfile）
- 编写 Dockerfile
```terminal
|# 使用官方 OpenJDK 作为基础镜像 
|# FROM openjdk:8-jdk-alpine
|FROM openjdk:17-jdk-alpine
|# 维护者信息
|LABEL maintainer="your_email@example.com"
|# 将应用的 jar 文件复制到镜像中,服务器上的 JAR 文件和 Dockerfile 应该位于同一个目录中
|COPY app.jar /app.jar
|# 暴露应用运行的端口，假设 Spring Boot 应用运行在 8080 端口
|EXPOSE 8080
|# 启动容器时执行的命令
|ENTRYPOINT ["java", "-jar", "/app.jar"]
```

- 使用`docker build -t <repository>:<tag> <path>`构建镜像
  ```terminal
  >|docker build -t my-springboot-app:latest .
  ```
- 使用`docker run -p <host_port>:<container_port> <image>`启动容器
  ```terminal
  >|docker run -d -p 8080:8080 --name my-springboot-container --restart always my-springboot-app:latest
  ```

## 部署 Redis
- 下载 redis.conf 并修改
  - 【必须】bind 127.0.0.1 把#号注释掉这部分，使redis可以外部访问
  - 【必须】daemonize 修改为no 用守护线程的方式启动
  - 【必须】requirepass 设置你的Redis链接密码
  - 【必须】appendonly 修改为yes，redis持久化　　默认是no
  - 【可选】tcp-keepalive 300 防止出现远程主机强迫关闭了一个现有的连接的错误 默认是300  
- 创建数据目录
  ```terminal
  >|sudo mkdir /data/redis
  >|sudo mkdir /data/redis/data
  ```  
- 上传 redis.conf 到 /data/redis 文件夹下    
- 启动容器
  ```terminal
  >|docker run -p 6379:6379 --name redis -v /data/redis/redis.conf:/etc/redis/redis.conf -v /data/redis/data:/data --restart always -d redis redis-server /etc/redis/redis.conf --appendonly yes
  ```

## 部署 MongoDB

- 下载 MongoDB 配置文件并修改
   - 【必须】修改 `bindIp` 为 `0.0.0.0` 以允许外部访问：`net: bindIp: 0.0.0.0`
   - 【可选】设置 `security` 选项以启用用户认证：`security: authorization: enabled`

- 创建 MongoDB 数据存储目录：`sudo mkdir -p /data/mongodb/data`

- 上传 `mongod.conf` 到 `/data/mongodb` 文件夹下。

- 使用以下命令启动 MongoDB 容器：
   ```terminal
   >|docker run -p 27017:27017 --name mongodb -v /data/mongodb/mongod.conf:/etc/mongo/mongod.conf -v /data/mongodb/data:/data/db -d mongo mongod -f /etc/mongo/mongod.conf --restart always
   ```  
   - `-p 27017:27017`：将主机的 27017 端口映射到容器的 27017 端口。
   - `--name mongodb`：为容器指定一个名称 `mongodb`。
   - `-v /data/mongodb/mongod.conf:/etc/mongo/mongod.conf`：将主机上的 `mongod.conf` 配置文件挂载到容器中。
   - `-v /data/mongodb/data:/data/db`：将主机上的数据目录挂载到容器中。
   - `-d`：后台运行容器。
   - `mongod -f /etc/mongo/mongod.conf`：使用指定的配置文件启动 MongoDB。

- 如果启用了用户认证，首次启动 MongoDB 后需要设置管理员用户：
   - 进入 MongoDB 容器：`docker exec -it mongodb mongo`
   - 切换到 `admin` 数据库：`use admin`
   - 创建管理员用户：
     ```terminal
     db.createUser({
       user: "admin",
       pwd: "password",
       roles: [ { role: "userAdminAnyDatabase", db: "admin" } ]
     })
     ```

## 部署 minio

- 拉取镜像：
  ```
  docker pull minio/minio
  ```
- 创建数据目录：
  ```
  mkdir -p /etc/docker/minio/data
  ```
- 启动容器：
  ```
  docker run -p 5555:9000 -p 5554:9001 --name minio -d --restart=always -e "MINIO_ACCESS_KEY=minioadmin" -e "MINIO_SECRET_KEY=minioadmin" -v /etc/docker/minio/data:/data minio/minio server /data --console-address ":9001" -address ":9000"
  ```
1. 拉取镜像
docker pull minio/minio
2. 启动实例
docker run --name minio -d -p 9000:9000 -p 9090:9090 minio/minio server /data --console-address ":9090" --address ":9000"
参数说明：

- */data*  数据目录，必选参数

- *--console-address*  控制台端口，用于Web管理，默认为随机端口

- *--address*  S3-API端口，用于API对接，默认为9000 
通过浏览器访问http://<ip>:9090，即可访问MinIO的Web管理界面，输入默认凭证`minioadmin/minioadmin`即可进入管理页面。 进入`Administrator/Buckets`页面，点击“Create Bucket”菜单，输入Bucket Name创建桶。
进入`User/Object Browser`页面，点击列表中的桶，可以浏览、上传文件。此时，上传的文件还无法在外部站点或笔记软件中访问。浏览器访问`http://<ip>:9000/blog/1.png`，会提示Access Denied。进入桶管理，配置匿名访问规则。Prefix输入 /，Access选择 readonly。保存后，再次访问`http://<ip>:9000/blog/1.png`可以看到图片了。 1. 持久化
由于服务运行在Docker中，需要将数据目录挂载到本地，另外服务启动时会在容器创建/root/.minio目录，也需要挂载到本地。

mkdir -p /opt/minio/{data,.minio}

docker run --name minio -d \
  -p 9000:9000 -p 9090:9090 \
  -v /opt/minio/data:/data \
  -v /opt/minio/.minio:/root/.minio \
  minio/minio server /data --console-address ":9090" --address ":9000"


2. 指定登录凭证
docker run --name minio -d \
  -p 9000:9000 -p 9090:9090
  -e MINIO_ROOT_USER=admin \
  -e MINIO_ROOT_PASSWORD=admin... \
  -v /opt/minio/data:/data \
  -v /opt/minio/.minio:/root/.minio \
  minio/minio server /data --console-address ":9090" --address ":9000"
3. Nginx配置域名访问
如果你拥有一个域名，希望通过域名访问MinIO服务，可以通过Nginx配置反向代理。

例如，通过`http://oss.example.net/ui`访问管理页面，并通过`http://oss.example.net/blog/1.png`预览图片。

## 配置管理页面地址  http://oss.example.net
server {
    listen 80;
    server_name oss.example.net;

    location / {
        proxy_set_header  Host $http_host; # 防止上传时提示SignatureDoesNotMatch错误
        proxy_connect_timeout 300;
        proxy_http_version 1.1;
        proxy_pass http://localhost:9000;
    }

    location /ui/ {
        rewrite ^/ui/(.*) /$1 break;
        # 添加websocket支持（防止桶浏览页面一直loading）
        proxy_http_version 1.1;
        proxy_connect_timeout 300;
        proxy_set_header  Upgrade $http_upgrade;
        proxy_set_header  Connection "upgrade";
        proxy_next_upstream  http_500 http_502 http_503 http_504 error timeout invalid_header;
        proxy_set_header  Host $http_host;
        proxy_set_header  X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_pass http://localhost:9090;
    }
}


同时，需要在启动MinIO服务时指定环境变量`MINIO_BROWSER_REDIRECT_URL=http://oss.example.net/ui`

使用docker compose启停服务
编辑docker-compose.yml

version: '3'
services:
  minio:
    image: minio/minio:latest
    ports:
      - "9001:9001"
      - "9090:9090"
    volumes:
      - "/Users/mlamp/minio/data:/data"
      - "/Users/mlamp/minio/.minio:/root/.minio"
    environment:
      - "MINIO_ROOT_USER=admin"
      - "MINIO_ROOT_PASSWORD=admin..."
      - "MINIO_BROWSER_REDIRECT_URL=http://oss.example.net/ui"
    command: server /data --console-address ":9090" --address ":9000"
    restart: always
使用`docker-compose up -d`启动服务
## Docker Compose
- `docker-compose up`
  - 启动 Docker Compose 服务。
- `docker-compose down`
  - 停止并删除 Docker Compose 服务及其资源。
- `docker-compose build`
  - 构建 Docker Compose 文件中的服务镜像。
- `docker-compose logs`
  - 查看 Docker Compose 服务的日志。

## 镜像管理
- `docker pull <image>`
  - 从 Docker Hub 或指定仓库下载镜像。例如：
    ```terminal
    >|docker pull ubuntu
    ```
- `docker build -t <name>:<tag> <path>`
  - 从 Dockerfile 构建镜像。例如：
    ```terminal
    >|docker build -t my-image:latest .
    ```
- `docker images`
  - 列出本地所有镜像。
- `docker rmi <image>`
  - 删除指定镜像。例如：
    ```terminal
    >|docker rmi my-image:latest
    ```
  - `-f`：强制删除，即使容器正在运行。  
- `docker system prune`
  - 清理未使用的数据，例如未使用的镜像和容器。

## 容器管理
- `docker run <options> <image>`
  - 创建并启动一个新容器。例如：
    ```terminal
    >|docker run -it ubuntu
    ```
- `docker ps`
  - 列出当前运行的容器。
- `docker ps -a`
  - 列出所有容器，包括已停止的。
- `docker stop <container>`
  - 停止运行中的容器。
- `docker start <container>`
  - 启动已停止的容器
- `docker restart <container>`
  - 重启容器
- `docker rm <container>`
  - 删除容器

## 容器操作
- `docker exec -it <container> <command>`
  - 在运行中的容器中执行命令。例如：
    ```terminal
    >|docker exec -it my-container bash
    ```
- `docker logs <container>`
  - 查看容器的日志。例如：
    ```terminal
    >|docker logs my-container
    ```

## 网络管理
- `docker network ls`
  - 列出所有 Docker 网络。
- `docker network create <network>`
  - 创建一个新网络。例如：
    ```terminal
    >|docker network create my-network
    ```
- `docker network rm <network>`
  - 删除网络。例如：
    ```terminal
    >|docker network rm my-network
    ```

## 数据卷管理
- `docker volume ls`
  - 列出所有 Docker 卷。
- `docker volume create <volume>`
  - 创建一个新卷。例如：
    ```terminal
    >|docker volume create my-volume
    ```
- `docker volume rm <volume>`
  - 删除卷。例如：
    ```terminal
    >|docker volume rm my-volume
    ```

## 其他命令
- `docker system df`
  - 查看 Docker 系统的磁盘使用情况。