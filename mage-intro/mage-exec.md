# 啟動 MageAI

公司的 MageAI 是以 docker 來啟動的。

又由於公司有使用到舊版的 Oracle 資料庫（v11），整個 Docker 如下

## 資料夾架構

```
├─instantclient-basic-linux.x64-19.29.0.0.0dbru.zip
├─Dockerfile
└─docker-compose.yml
```

### instantclient

instantclient-basic-linux.x64-19.29.0.0.0dbru.zip 是從 Oracle 官方下載的 Oracle Instant Client，

用來讓 Mage 能處理 Oracle 11 資料庫的連線需求。

下載網址：[https://www.oracle.com/database/technologies/instant-client/linux-x86-64-downloads.html](https://www.oracle.com/database/technologies/instant-client/linux-x86-64-downloads.html)

檔案有另外備份至 SharePoint，位於: [/01.Software/開發工具/etl/instantclient-basic-linux.x64-19.29.0.0.0dbru.zip](https://dfrecyclecom.sharepoint.com/IT/Shared%20Documents/01.Software/%E9%96%8B%E7%99%BC%E5%B7%A5%E5%85%B7/etl/instantclient-basic-linux.x64-19.29.0.0.0dbru.zip)

### Dockerfile

```Dockerfile
FROM mageai/mageai:latest

# 需要 root 權限來安裝 unzip
USER root

# 將專案中的 Oracle Instant Client 壓縮檔複製進容器
COPY instantclient-basic-linux.x64-19.29.0.0.0dbru.zip /tmp/instantclient.zip

# 安裝 unzip，解壓縮 Oracle Instant Client，並設定路徑
RUN apt-get update && \
    apt-get install -y unzip && \
    apt-get install -y libaio1 && \
    rm -rf /var/lib/apt/lists/* && \
    mkdir -p /opt/oracle && \
    unzip -q /tmp/instantclient.zip -d /opt/oracle && \
    rm /tmp/instantclient.zip && \
    cd /opt/oracle && \
    ln -s instantclient_19_29 instantclient

# 設定 Oracle Client 相關環境變數
ENV LD_LIBRARY_PATH=/opt/oracle/instantclient:$LD_LIBRARY_PATH \
    ORACLE_HOME=/opt/oracle/instantclient

```

### docker-compose.yml

```yaml
services:
  mage:
    build:
      context: .               
      dockerfile: Dockerfile
    image: my-mage-oracle:latest
    pull_policy: build 
    container_name: my-mage
    # 第一次會自動建立名為 my_project 的 Mage 專案
    command: mage start my_project
    restart: unless-stopped
    environment:
      # Mage 本身的執行環境
      ENV: dev
      USER_CODE_PATH: /home/src/my_project

      # 關閉使用者認證（讓你直接進入 Mage UI，不用登入）
      DISABLE_USER_AUTHENTICATION: "True"


      # （選用）如果你想讓 Mage 把「自己的 metadata」也存到 Postgres，而不是用內建 SQLite：
      # MAGE_DATABASE_CONNECTION_URL: "postgresql+psycopg2://myuser:mypassword@postgres:5432/mydb"
      # 需要符合官方文件說明的格式 :contentReference[oaicite:0]{index=0}
      
      # 降低 Mage pipeline / block 併發 
      CONCURRENCY_CONFIG_BLOCK_RUN_LIMIT: "2"
      CONCURRENCY_CONFIG_PIPELINE_RUN_LIMIT: "1"
    ports:
      - "6789:6789"
    volumes:
      # 把當前資料夾底下的 mage 資料夾掛到容器內
      - ./mage:/home/src
      
    # 資源限制（依你主機再調）  
    cpus: 1.5        # 最多用 1.5 顆 CPU（你可以改成 1 或 2）
    mem_limit: 2g    # 記憶體上限 2GB（依你主機再調）
```

## 啟動指令

```bash
$ docker-compose up -d
```

或是使用系統管理者權限

```bash
$ sudo docker-compose up -d
```