# 如何設定GitLab Runner?



## 安裝GitLab Runner

安裝Runner的方式會因為環境不同而有不同的做法，這邊會以Windows及Docker為例子

### Windows GitLab Runner

1. 建立資料夾並下載安裝檔

    ```bash
    mkdir D:\gitlab-runner
    cd D:\gitlab-runner
    ```

    點[Windows GitLab Runner](https://GitLab-runner-downloads.s3.amazonaws.com/latest/binaries/GitLab-runner-windows-amd64.exe)下載安裝檔至`D:\\gitlab-runner`

1. 安裝

    ```bash
    .\gitlab-runner.exe install
    .\gitlab-runner.exe start
    ```

   安裝完後執行`.\gitlab-runner.exe --version`確認是否安裝成功

   ![image](https://github.com/loulazynote/loulazynote.github.io/assets/33840759/c1028651-082c-46a3-be50-c4ee3885b5ad)

### Docker GitLab Runner

1. 安裝[Docker](https://docs.docker.com/get-docker/)
2. 執行Docker安裝GitLab Runner

   ```bash

    docker run -d --name gitlab-runner --restart always \\
        -v /srv/GitLab-runner/config:/etc/GitLab-runner \\
        -v /var/run/docker.sock:/var/run/docker.sock \\
        gitLab/gitlab-runner:latest

   ```

1. 確認是否安裝成功

   ```bash
   docker exec -it gitlab-runner gitlab-runner --version
   ```

   ![image](https://github.com/loulazynote/loulazynote.github.io/assets/33840759/3a30d76a-52c7-4745-ab3e-ba4cc86b51a4)

## 註冊GitLab Runner

- Runner有3種類別
  - Shared Runner : 所有專案都可以使用的Runner
  - Group Runner : 群組專案可以使用的Runner
  - Specific Runner : 特定專案可以使用的Runner

  > 而註冊的方式都大同小異，這邊以Shared Runner為例

1. 取得GitLab Runner Token

   - Shared Runner : 登入GitLab -> Admin Area -> Overview -> Runners

     ![image](https://github.com/loulazynote/loulazynote.github.io/assets/33840759/87771aae-b9bf-4044-88d1-2aeda5d2ad7c)

   - Specific Runner的Token可以在專案的設定中取得

     ![image](https://github.com/loulazynote/loulazynote.github.io/assets/33840759/916259a5-94eb-4feb-8288-71e29537235e)

   - Group Runner的Token可以在群組的設定中取得

     ![image](https://github.com/loulazynote/loulazynote.github.io/assets/33840759/2475e0b6-53a1-48ac-8d6c-19f21b290796)

1. 執行`.\gitlab-runner.exe register`或`docker exec -it gitlab-runner gitlab-runner register`註冊GitLab Runner

1. 填寫 註冊資訊
    - Gitlab instance URL : GitLab的網址
    - registration token : Runner的Token
    - description : Runner的描述
    - tags : Runner的標籤
    - maintenance note : Runner的維護訊息
    - executor : Runner的執行方式

    ![image](https://github.com/loulazynote/loulazynote.github.io/assets/33840759/90df8709-b592-414e-8b58-f4d56637ca5a)

    > - 如果是用Docker去跑CI/CD的話，executor要選docker
    > - 這裡我是希望可以自行設定整個CI/CD的環境，所以選shell

1. 成功後會提示如下圖

    ![image](https://github.com/loulazynote/loulazynote.github.io/assets/33840759/0a8356a5-2cd5-436b-9b80-0d912242f27f)

    查看Gitlab Runner狀態

    ![image](https://github.com/loulazynote/loulazynote.github.io/assets/33840759/46d3c00e-f881-41a0-95bb-26e2ca8e751d)

## 參考資料

- [Run GitLab Runner in a container | GitLab Docs](https://docs.gitlab.com/runner/install/docker.html#install-the-docker-image-and-start-the-container)
- [Registering runners | GitLab Docs](https://docs.gitlab.com/runner/register/index.html#windows)

