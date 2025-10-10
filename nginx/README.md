# phpMyAdmin Nginx Docker 构建指南

## GitHub Action 自动构建

### 设置 Docker Hub 凭据

在 GitHub 仓库设置中添加以下 Secrets：

1. 进入仓库 **Settings** > **Secrets and variables** > **Actions**
2. 添加以下 Repository secrets：
   - `DOCKERHUB_TOKEN`: 你的 Docker Hub 访问令牌
3. 添加以下 Repository variables：
   - `DOCKERHUB_USERNAME`: 你的 Docker Hub 用户名
   - `DOCKERHUB_REPO`: 你的仓库名

### 触发构建

GitHub Action 会在以下情况自动触发：

1. **自动触发**：
   - 推送到 `main` 分支
   - 修改 `nginx/` 目录下的文件
   - 修改 GitHub Action 工作流文件

2. **手动触发**：
   - 在 GitHub 仓库页面，进入 **Actions** 标签
   - 选择 "Build and Push phpMyAdmin Nginx Docker Image" 工作流
   - 点击 **Run workflow** 按钮

### 构建产物

成功构建后，将生成以下 Docker 镜像标签：

- `caijiamx/phpmyadmin:5.2.2-nginx` - 主要版本标签

## 本地构建

### 手动构建

```bash
# 进入 nginx 目录
cd nginx

# 构建镜像
docker build -t caijiamx/phpmyadmin:5.2.2-nginx .

# 推送到 Docker Hub（需要先登录）
docker login
docker push caijiamx/phpmyadmin:5.2.2-nginx
```

### 多平台构建

```bash
# 设置 buildx
docker buildx create --use

# 多平台构建并推送
docker buildx build \
  --platform linux/amd64,linux/arm64 \
  -t caijiamx/phpmyadmin:5.2.2-nginx \
  --push .
```

## 使用方法

### 基本使用

```bash
# 运行容器
docker run --name phpmyadmin \
  -d \
  -e PMA_HOST=your-mysql-host \
  -p 8080:80 \
  caijiamx/phpmyadmin:5.2.2-nginx

docker run --name phpmyadmin -d -e PMA_HOST=dbhost phpmyadmin:5.2.2-nginx
```

### Docker Compose 示例

```yaml
version: '3.8'

services:
  phpmyadmin:
    image: caijiamx/phpmyadmin:5.2.2-nginx
    restart: always
    ports:
      - "8080:80"
    environment:
      - PMA_HOST=mysql
      - PMA_PORT=3306
    depends_on:
      - mysql

  mysql:
    image: mysql:8.0
    restart: always
    environment:
      - MYSQL_ROOT_PASSWORD=your-password
    volumes:
      - mysql_data:/var/lib/mysql

volumes:
  mysql_data:
```

## 环境变量

支持所有标准 phpMyAdmin 环境变量：

- `PMA_HOST` - MySQL 服务器地址
- `PMA_PORT` - MySQL 服务器端口（默认 3306）
- `PMA_USER` - MySQL 用户名
- `PMA_PASSWORD` - MySQL 密码
- `PMA_ARBITRARY` - 允许任意服务器连接（设置为 1）

更多环境变量请参考主 README.md 文件。

## 故障排除

### 常见问题

1. **构建失败**：检查 Docker Hub 凭据是否正确设置
2. **推送失败**：确保 Docker Hub 仓库存在且有推送权限
3. **多平台构建问题**：确保使用了 `docker/setup-buildx-action`

### 日志查看

```bash
# 查看容器日志
docker logs phpmyadmin

# 进入容器调试
docker exec -it phpmyadmin /bin/bash
```

## 技术栈

- **基础镜像**：phpmyadmin:5.2.2-fpm-alpine
- **Web 服务器**：Nginx
- **操作系统**：Alpine Linux
- **进程管理**：Supervisor
- **支持平台**：linux/amd64, linux/arm64
