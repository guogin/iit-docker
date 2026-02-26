📁 文件结构

    iit-docker/
    ├── docker-compose.yml          # 编排两个服务
    ├── .dockerignore               # 全局 Docker 忽略文件
    ├── .gitmodules                 # 子模块配置
    ├── iit/                         # 依赖库（子模块）
    ├── iit-ws/
    │   └── Dockerfile              # 后端服务镜像构建
    └── iit-app/
      ├── Dockerfile              # 前端服务镜像构建
      ├── nginx.conf              # Nginx 反向代理配置
      └── .dockerignore           # 前端忽略文件

  🚀 使用方法

  从克隆到运行的完整步骤：

  ```bash
  # 克隆主仓库
  git clone https://github.com/guogin/iit-docker.git
  cd iit-docker

  # 同步并初始化子模块
  git submodule sync
  git submodule update --init --recursive
  ```

  如果子项目有更新，一般来说，下面这些步骤是主仓库的作者（我）要做的事。但如果有急需，你也可以自己更新子模块：
  ```bash
  # 拉取并将子模块检出到其远端跟踪分支最新提交
  git submodule update --remote --recursive 

  # 或者进入子模块目录手动 git fetch + git checkout <branch> + git pull

  # 最后作者应该在主仓库里
  git status
  git add
  # 再把子模块指针更新提交到主仓库

  ```

  使用 podman-compose 或 docker compose 启动：

  ```bash
  # 构建并启动所有服务
  podman-compose up --build
  # 或者使用 Docker Compose
  docker compose up --build

  # 或者在后台运行
  podman-compose up -d --build
  # 或者使用 Docker Compose
  docker compose up -d --build

  # 查看状态
  podman ps

  # 查看日志
  podman logs -f iit-ws
  podman logs -f iit-app

  # 停止服务
  podman-compose down
  ```

  🌐 访问方式

   |服务          |地址                                   |说明       |
   |--------------|---------------------------------------|-----------|
   |前端 (React)  | http://localhost:3000                 |浏览器访问 |
   |后端 API      | http://localhost:8080/api/v1/simulate | REST API  |


  🔧 关键配置说明

  1. iit-ws/Dockerfile：
    • 使用多阶段构建，先构建 iit 依赖库，再构建 iit-ws
    • 最终使用轻量级 JRE 镜像运行
  2. iit-app/Dockerfile：
    • 先构建 React 生产包，再用 Nginx 服务静态文件
    • Nginx 配置中设置了反向代理，将 /api/* 请求转发到 iit-ws:8080
  3. docker-compose.yml：
    • 两个服务在同一个 Docker 网络 iit-network 中
    • 前端通过服务名 iit-ws 访问后端（无需硬编码 IP）
