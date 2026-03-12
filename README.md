原项目没有docker hub官方镜像，只好自己构建一个。仅在原项目代码基础上构建了这个镜像，未做其他任何更改，原汁原味，跟随原项目自动更新。原项目地址 https://github.com/ZhuLinsen/daily_stock_analysis

使用方法比原项目readme里的介绍要更简单一点，就以下三步：

1.下载https://raw.githubusercontent.com/ZhuLinsen/daily_stock_analysis/refs/heads/main/.env.example
将文件名.env.example 改为.env，打开并配置好里面的一些必要参数。

2.新建docker-compose.yml文件，放在跟.env同目录下，粘贴为以下内容：

```yaml
# ===================================
# A股自选股智能分析系统 - Docker Compose
# ===================================
# 
# 使用方式:
#   定时分析模式: docker compose up -d analyzer
#   网页UI模式: docker compose up -d server
#   同时启动: docker compose up -d

version: '3.8'

x-common: &common
  image: yuntian123456/daily_stock_analysis:latest
  restart: unless-stopped
  env_file:
    - .env
  volumes:
    - ./data:/app/data
    - ./logs:/app/logs
    - ./reports:/app/reports
  environment:
    - TZ=Asia/Shanghai

services:
  # 模式一：纯后台定时任务模式
  analyzer:
    <<: *common
    container_name: stock-analyzer

  # 模式二：Web 可视化与 API 模式
  server:
    <<: *common
    container_name: stock-server
    command: python main.py --serve-only --host 0.0.0.0 --port ${WEBUI_PORT:-8000}
    ports:
      - "${WEBUI_PORT:-8000}:${WEBUI_PORT:-8000}"
```

3.进入docker-compose.yml所在文件夹，根据需求使用不同的启动命令，三选一即可（推荐方命令③）：

   ① 定时分析模式: docker compose up -d analyzer

   ② 网页UI模式: docker compose up -d server

   ③ 同时启动以上两种模式: docker compose up -d

---------------------------------------

 Q&A: 大模型API找一些免费的用就行了，比如 https://s.qiniu.com/3yAbAv ，新人注册即免费得 1000万 Token，相当于白嫖几十块钱了，够用一段时间了。

另外比较有名的第三方免费金融数据平台Tushare，也可以注册免费试试，用于获取股票数据 
https://tushare.pro/weborder/#/login?reg=934524

