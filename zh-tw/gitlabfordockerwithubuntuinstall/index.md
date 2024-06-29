# Gitlab 淺嘗 : GitLab EE on Docker


## Gitlab 淺嘗 : GitLab EE on Docker

### 前言

因為最近有個案子，需要使用到 Gitlab，所以就來研究一下 Gitlab 的使用方式，由於客戶使用的是舊版本，所以我也只能使用舊版本來研究，不過基本上新版本的操作方式都差不多，只是介面改變而已

所以接下來的內容我會用最新版本說一下，因為我也想試試新的版本

### 什麼是 Gitlab

Gitlab 是一個基於 Git 的版本控制倉庫管理軟體，可以在網頁上直接操作 Git 的功能，並且提供了一些額外的功能，例如：Issue、Wiki、CI/CD 等等

其實大部分都跟 Azure DevOps 很像，只是 Gitlab 是開源的，而且可以自己架設，所以如果公司不想要使用 Azure DevOps，可以考慮使用 Gitlab

### Gitlab 安裝

> - 這裡我所講的 OS 是 Ubuntu 22.04 LTS 安裝方式，其他 OS 可以參考[官方文件](https://about.gitlab.com/install/)
>   且這裡會需要安裝`OpenSSH Server` 以及 `Docker`，如果是使用 Ubuntu 官方提供的 ISO 安裝的話，在安裝時就會有選項可以選擇是否安裝，如果是使用其他的方式安裝的話，就需要自己安裝
> - 為什麼不用 `Docker for Windows`，因為 Gitlab 官方有提到沒有支援，且有一些已知的問題，所以就選擇使用 `Ubuntu Server` 來安裝

#### 1. 安裝 `Ubuntu Server (minimized)`

- 先到[Ubuntu](https://ubuntu.com/download/server)下載最新 `Ubuntu Server (minimized)` 的 ISO 檔
  ![image](https://github.com/loulazynote/loulazynote.github.io/assets/33840759/51493805-0f52-4431-ac78-f66c56d9583a)

- 利用 HyperV 建立 VM 並安裝 `Ubuntu Server (minimized)`
  ![image](https://github.com/loulazynote/loulazynote.github.io/assets/33840759/7746f0fa-1462-4dd4-b751-ff4d57e9a5b8)

#### 2. 變更預設 OpenSSH Server 的 Port 號

因為 Gitlab 會使用到 Port 22 當作 SSH 的 Port，並做為 Git 使用，所以需要變更預設的 Port 22 為其他的 Port 號

1. 先安裝 `VIM` 編輯器，或是你習慣用預設的`vi`或`nano`都可以

   ```bash
        sudo apt install vim
   ```

2. 編輯 `/etc/ssh/sshd_config` 檔案，以改變預設 Port 號

   ```bash
        sudo vi /etc/ssh/sshd_config
   ```

   ```bash
        # Port 22 <--預設就是被註解的22 port號
        Port 10022 #新增一行新port號，我設定為10022
   ```

   ![image](https://github.com/loulazynote/loulazynote.github.io/assets/33840759/aafcab66-5fc1-486c-aa07-39958dec0813)

3. 重啟 OpenSSH Server

   ```bash
        sudo systemctl restart sshd
   ```

4. 登出並重新登入

   ```bash
        exit
        ssh <your ubuntu ip> -p 10022 #登入時要加上-p 10022
   ```

   or

   ```bash
        ssh root@<your ubuntu ip> -p 10022 #登入時要加上-p 10022
   ```

   > 要以`root` SSH 進入，需要編輯`/etc/ssh/sshd_config`
   >
   > ```bash
   >     #PermitRootLogin prohibit-password <--預設就是被註解的
   >     PermitRootLogin yes #新增一行，並把註解拿掉
   > ```
   >
   > ![image](https://github.com/loulazynote/loulazynote.github.io/assets/33840759/eb674192-9658-4a95-b481-b637e6ed97df)

#### 3. 執行 Docker 並把 Gitlab 安裝

1. 設定 `GITLAB_HOME`目錄，用`root`身份執行時，一定要把`GITLAB_HOME`設定在`/root`底下

   ```bash
        export GITLAB_HOME=/root/gitlab
   ```

2. 執行 Gitlab EE 容器

   > 為什麼安裝 `Enterprise Edition`，因為沒有購買授權等同於 `Community Edition`，未來如果有需要升級直接購買授權就能直接使用，不用重新安裝

   1. 使用 Docker Engine 安裝

   ```bash
        docker run --detach \
        --hostname <your ubuntu ip> \ # 這裡可以填寫你想要的hostname或已購買的Domain，也能設定為IP
        --publish 443:443 --publish 80:80 --publish 22:22 \
        --name gitlab \
        --restart always \
        --volume $GITLAB_HOME/config:/etc/gitlab \
        --volume $GITLAB_HOME/logs:/var/log/gitlab \
        --volume $GITLAB_HOME/data:/var/opt/gitlab \
        --shm-size 256m \
        gitlab/gitlab-ee:latest
   ```

   > 可以執行 `sudo docker logs -f gitlab` 查看 log

   2. 使用 Docker Compose 安裝

      - 檢查 是否有安裝 `Docker Compose`

        ```bash
            docker-compose --version
        ```

        ![image](https://github.com/loulazynote/loulazynote.github.io/assets/33840759/6446d9c2-f0d7-47d5-9198-8dc9802dc6d2)

      - 如果沒有安裝，可以參考[Docker 文件](https://docs.docker.com/compose/install/)安裝

      - 建立 `docker-compose.yml` 檔案

        ```yaml
        version: '3.6'
        services:
        web:
          image: 'gitlab/gitlab-ee:latest'
          restart: always
          hostname: '<your ubuntu ip>' # 這裡可以填寫你想要的hostname或已購買的Domain，也能設定為IP
          environment:
          GITLAB_OMNIBUS_CONFIG: |
            external_url '<your ubuntu ip>'
          ports:
            - '80:80'
            - '443:443'
            - '22:22'
          volumes:
            - '$GITLAB_HOME/config:/etc/gitlab'
            - '$GITLAB_HOME/logs:/var/log/gitlab'
            - '$GITLAB_HOME/data:/var/opt/gitlab'
          shm_size: '256m'
        ```

      - 確定你所在目錄是在`docker-compose.yml` 檔案的目錄後執行 `docker-compose up -d`

3. 開啟預設 `root` 登入密碼

   ```bash
        sudo docker exec -it gitlab grep 'Password:' /etc/gitlab/initial_root_password
   ```

   > 安裝完後，此組密碼只有 24 小時的有效期，過期後就會失效，所以要記得在 24 小時內啟用登入並修改密碼

   - 14.0 以後的版本才有這功能
   - 而較舊的版本則是用`root`登入時，會要求設定新密碼

4. 安裝完成 可以透過瀏覽器輸入 IP 或你的 domain 進入 Gitlab

   ![image](https://github.com/loulazynote/loulazynote.github.io/assets/33840759/718f223c-9792-476d-81c1-a63aa0b622bb)

   > - 如果需要使用 `HTTPS`，可以參考[Gitlab 官方文件](https://docs.gitlab.com/omnibus/settings/nginx.html#enable-https)
   > - 保哥也有提供一些簡單的自簽憑證設定方式，可以參考[快速上手 GitLab EE on Docker 安裝：以 Ubuntu 22.04 LTS 為例](https://blog.miniasp.com/post/2023/04/26/Install-GitLab-EE-on-Docker)

下一篇會介紹 Gitlab 的一些額外設定，以及如何使用 Gitlab 來做 CI/CD

### 參考資料

- [Gitlab - GitLab Docker images](https://docs.gitlab.com/ee/install/docker.html)
  - [Gitlab - Installing GitLab with Omnibus packages](https://docs.gitlab.com/omnibus/installation/)
- [Will 保哥 - 快速上手 GitLab EE on Docker 安裝：以 Ubuntu 22.04 LTS 為例](https://blog.miniasp.com/post/2023/04/26/Install-GitLab-EE-on-Docker)
- [和艦長一起 30 天玩轉 GitLab](https://gitlab-book.tw/ithelp/intro/introduction/)

