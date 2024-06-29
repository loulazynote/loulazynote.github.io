# Gitlab 使用外部 PostgreSQL 方法


# Gitlab 使用外部 PostgreSQL 方法

## 前言

一樣是因為最近的案子需求，所以就來研究一下 Gitlab 如果要使用外部的 PostgreSQL 如何設定，而新舊版本其實差不多，只是有部分指令已經不同

## 關於 Gitlab 與 PostgreSQL 關係

- [官方文件](https://docs.gitlab.com/omnibus/settings/database.html)有說明，如果是使用 `Omnibus GitLab` 則會內建 `PostgreSQL`，且 Gitlab 僅有支援PostgreSQL
- 當然目前的Docker也有包含 PostgreSQL 的版本
- 但如果是要使用外部的 PostgreSQL，則需要自行安裝 PostgreSQL，且版本需符合 Gitlab 所需的版本
  - Gitlab 16.0 以後的版本，需要 `PostgreSQL 13.0` 以上
  - Gitlab 15.0 以後的版本，預設使用 `PostgreSQL 13.0`
  - Gitlab 13.7 以後的版本，建議 `PostgreSQL 12.0` 以上
  - Gitlab 13.0 以前的版本，需要 `PostgreSQL 11.0` 以上
  - Gitlab 12.8 以前的版本，需要 `PostgreSQL 9.6` 以上`PostgreSQL 11.7`以下

## 安裝與設定

### 安裝 PostgreSQL

我這裡是使用Docker來安裝 `PostgreSQL` ，步驟如下：

1. 使用 `docker compose` 安裝
    - 我在本地端建立一個資料夾 `postgresql` 來放置 `docker-compose.yml` 檔案

        ```bash
        mkdir postgresql
        cd postgresql
        ```

    - 我設定了一個路徑 `export POSTGRESQL_HOME=/root/postgresql`
    - 建立 `docker-compose.yml` 檔案 `sudo vi docker-compose.yml`

        ```yaml
        version: "3.7"

        services:
        postgres:
            image: postgres:13.11
            container_name: postgres_13
            restart: always
            volumes:
            - '$POSTGRESQL_HOME/data:/var/lib/postgresql/data'
            ports:
            - '5432:5432'
            environment:
            POSTGRES_DB: gitlabhq_production # Gitlab 預設的資料庫名稱
            POSTGRES_USER: postgres
            POSTGRES_PASSWORD: 1234
        ```

    - 確定你所在目錄是在`docker-compose.yml` 檔案的目錄後執行 `docker-compose up -d`

2. 使用 Docker Engine 安裝

   ```bash
    sudo docker run -d --name postgres_13 \ 
        --restart always \
        -p 5432:5432 \
        -v $POSTGRESQL_HOME/data:/var/lib/postgresql/data \
        -e POSTGRES_DB=eflab \
        -e POSTGRES_USER=postgres \
        -e POSTGRES_PASSWORD=1234 \
        postgres:13.11
   ```

3. 測試看看能不能連上，沒有相關工具的可以使用`pgadmin4`查看

    ```bash
    # 沒有其他工具執行下列Docker可以使用 pgadmin4，這裡設定的port號是20080，故瀏覽器輸入 http://localhost:20080
    docker run -p 20080:80 \
        -e 'PGADMIN_DEFAULT_EMAIL=user@example.com' \
        -e 'PGADMIN_DEFAULT_PASSWORD=admin' \
        -d dpage/pgadmin4:latest
    ```

    這是我用DBeaver連線的畫面
    ![image](https://github.com/loulazynote/loulazynote.github.io/assets/33840759/6677b460-9289-41e5-95f6-742c2270cc10)
    ![image](https://github.com/loulazynote/loulazynote.github.io/assets/33840759/54b1a02c-261a-48e9-b326-508462db61dc)

### 更改Gitlab設定

1. 進入到gitlab的容器內

    ```bash
    docker exec -it gitlab bash
    ```

2. 編輯 `gitlab.rb` 檔案

    ```bash
    vi /etc/gitlab/gitlab.rb
    ```

3. 在VIM模式下搜尋 `postgresql`，並將下列設定開啟

   ```bash
    /postgresql
   ```

   ```bash
    # Disable the bundled Omnibus provided PostgreSQL
    postgresql['enable'] = false # 這裡要設定為false，不然會使用內建的PostgreSQL

    # PostgreSQL connection details
    gitlab_rails['db_adapter'] = 'postgresql'
    gitlab_rails['db_encoding'] = 'unicode'
    gitlab_rails['db_host'] = 'your IP/hostname of database server'
    gitlab_rails['db_password'] = '1234'
   ```

   ![image](https://github.com/loulazynote/loulazynote.github.io/assets/33840759/89a71690-9ef6-4686-b890-079f7b27def8)
   ![image](https://github.com/loulazynote/loulazynote.github.io/assets/33840759/364a8c61-dd12-4141-9c2c-e624e8e55fda)

4. 執行`sudo gitlab-ctl reconfigure`(執行後可能要等2分鐘左右)

   完成後會看到下列畫面

   ![image](https://github.com/loulazynote/loulazynote.github.io/assets/33840759/c81cfa34-8c0d-44ed-8862-4e8ba843a978)
   ![image](https://github.com/loulazynote/loulazynote.github.io/assets/33840759/a244179a-a1c4-44ff-bbd7-c9fdef72865a)

## 參考資料

- [Gitlab Doc | Database settings](https://docs.gitlab.com/omnibus/settings/database.html)
- [Gitlab Doc | Configure GitLab using an external PostgreSQL service](https://docs.gitlab.com/ee/administration/postgresql/external.html)
- [Gitlab Doc | Restore GitLab](https://docs.gitlab.com/ee/raketasks/restore_gitlab.html)

