# 使用Angular及Teams ToolKit建立Teams App


在B站看到 2021年9月 在對岸 微軟 _Reactor_ 關於 _Teams App_ 的活動， 也剛剛好~公司也有小小的需求希望能在 _Teams_ 上做一些操作， 所以就順便動手研究了。<br/>

爬了很多文看了很多Doc， 發現大部分都是用 _React_ 寫的， 關於 _Angular_ 的部分可以說是幾乎沒有（不知道是太少人用還是太Easy🤣）。<br/>

所以這篇就記錄一下研究的過程！

## 準備 Microsoft 365 組織

- [Prepare your Microsoft 365 tenant - Teams | Microsoft Docs](https://bit.ly/3cDbE2O)
    > 申請免費的 [Microsoft 365 Developer Program](https://bit.ly/3cFbDvp)

## 必要開發工具

- [_Visual Studio Code_](https://bit.ly/3JavZZx)

  ```bash
  choco install vscode -y
  ```

- [_Teams Toolkit extension_](https://bit.ly/3z25wZD)

  ```bash
  code --install-extension TeamsDevApp.ms-teams-vscode-extension
  ```

- [_Node.js LTS_](https://bit.ly/3zurFRv)
  
  有裝 [_NVM_](https://bit.ly/3oyNSYl) 的直接下此 Command 就好

  ```bash
  nvm install lts
  ```

  沒有的話就用 [_Chocolatey_](https://bit.ly/3PxTUEN) 或到[Node.js 官網](https://bit.ly/3SbQ1Hp)下載了

  ```bash
  choco install nodejs-lts -y
  ```

- [_TeamsFx CLI_](https://bit.ly/3zueoIM)

  ```bash
  npm install -g @microsoft/teamsfx-cli
  ```

- [_Angular Cli_](https://bit.ly/3z2KtGh)

  ```bash
  npm install -g @angular/cli
  ```

## 建立步驟

1. 建立一個新資料夾

    ```bash
    cd D:\
    mkdir Demo
    ```

2. 在此資料夾內以範本建立專案

    ```bash
    teamsfx new template hello-world-tab-without-sso --folder "./Demo"
    cd ./Demo/hello-world-tab-without-sso
    code .
    ```

3. 將 tabs 資料夾內全部檔案刪除, 並新增 Angular 專案

    ![image](https://user-images.githubusercontent.com/33840759/180796003-47d65766-49d6-48e5-9323-b55285092bd9.png)

   ```bash
    ng new tabs
    cd ./tabs
   ```

4. 加入Teams App 在 Compiler 時必要套件

   ```bash
    npm install @microsoft/teams-js
    npm install @types/node env-cmd cross-env --save-dev #後兩套件是對於開發環境變數做操做的, 如不需要可以不用裝(根據TeamsToolKit預設是有安裝的)
   ```

5. 開啟 tabs內的`Package.json` 加入以下命令

   ```json
    "dev:teamsfx": "env-cmd --silent -f .env.teamsfx.local npm run start",
    "install:teamsfx": "npm install",
    "build:teamsfx": "cross-env-shell \"env-cmd -f .env.teamsfx.${TEAMS_FX_ENV} npm run build\"",
    "build:teamsfx:dev": "cross-env TEAMS_FX_ENV=dev npm run build:teamsfx"
   ```

    ![image](https://user-images.githubusercontent.com/33840759/180758144-796ba196-56ae-49ba-b9e8-f3ab0c869167.png)

6. 點選 _Teams ToolKit_, 並點選 編輯 `manifest`

    ![image](https://user-images.githubusercontent.com/33840759/180759058-2937357d-b283-4896-b15d-c922b4d3520e.png)

7. 編輯 `manifest` , 修改以下欄位

    ![image](https://user-images.githubusercontent.com/33840759/180762949-7df6b1d7-660f-45ea-a678-a38ee049a9fa.png)

   安裝時所顯示的畫面

    ![image](https://user-images.githubusercontent.com/33840759/180763375-e275734b-fd59-4ef5-87de-d837d8302e7e.png)

8. 建立 _Teams Config Component_

   ```bash
    ng new TeamsConfig
   ```

9. _import `teams-js`_ 並寫入設定(:exclamation:不加此設定,會無法顯示:exclamation:)

    ```typescript
        import { Component, OnInit } from '@angular/core';

        import * as microsoftTeams from '@microsoft/teams-js';//引入 teams-js

        @Component({
        selector: 'app-teams-config',
        templateUrl: './teams-config.component.html',
        styleUrls: ['./teams-config.component.scss']
        })
        export class TeamsConfigComponent implements OnInit {

        constructor() { }

        ngOnInit(): void {
            // Initialize the Microsoft Teams SDK
            microsoftTeams.initialize();

            microsoftTeams.settings.registerOnSaveHandler((saveEvent) => {
                const baseUrl = window.location.protocol + '//' + window.location.host + '/';
                microsoftTeams.settings.setSettings({
                    suggestedDisplayName: 'Tab Test',
                    entityId: 'index', // 與 manifast內的staticTabs底下entityId需相同
                    contentUrl: baseUrl,
                    websiteUrl: baseUrl,
                });
                saveEvent.notifySuccess();
            });

            
            microsoftTeams.settings.setValidityState(true);
          }
        }
    ```

10. 編輯 `teams-config.component.html`

    ![image](https://user-images.githubusercontent.com/33840759/180786400-7f9a59d5-25a8-4982-86d9-1259e7b0c570.png)

11. 建立 _Tab Component_ 並撰寫內容

    ```bash
        ng new tab
    ```

    ![image](https://user-images.githubusercontent.com/33840759/180786400-7f9a59d5-25a8-4982-86d9-1259e7b0c570.png)

12. 修改 `app-routing.module.ts`及`app.module.ts`

    ```typescript
    //app-routing.module.ts
    import { NgModule } from '@angular/core';
    import { RouterModule, Routes } from '@angular/router';
    import { TabComponent } from './components/tab/tab.component';
    import { TeamsConfigComponent } from './components/teams-config/teams-config.component';

    const routes: Routes = [
        { path: '', pathMatch: 'full', redirectTo: '/tab' },
        { path: 'tab', component: TabComponent },
        { path: 'config', component: TeamsConfigComponent },
        { path: '**', pathMatch: 'full', redirectTo: '/tab' }, // catch any unfound routes and redirect to home page
    ];

    @NgModule({
        imports: [RouterModule.forRoot(routes)],
        exports: [RouterModule],
    })
    export class AppRoutingModule {}
    ```

    ```typescript
    //app.module.ts
    import { NgModule } from '@angular/core';
    import { BrowserModule } from '@angular/platform-browser';

    import { AppRoutingModule } from './app-routing.module';
    import { AppComponent } from './app.component';
    import { TabComponent } from './components/tab/tab.component';
    import { TeamsConfigComponent } from './components/teams-config/teams-config.component';

    @NgModule({
        declarations: [AppComponent, TabComponent, TeamsConfigComponent],
        imports: [BrowserModule, AppRoutingModule],
        providers: [],
        bootstrap: [AppComponent],
    })
    export class AppModule {}

    ```

    > 修改完別忘了將 app.component.html也加上 `<router-outlet></router-outlet>`

13. 建立 [開發測試用途的自簽憑證 (_Self-Signed Certificate_)](https://bit.ly/3J1nYWS), 並放入tabs資料夾的根目錄

    ![image](https://user-images.githubusercontent.com/33840759/180790126-6a3cc4e6-960f-4378-bc03-575451c707ed.png)

14. 修改 `package.json`的 `scripts`,將 `ng serve`修改為下列命令列

    ```bash
        ng serve --ssl --ssl-key server.key --ssl-cert server.crt
    ```

    ![image](https://user-images.githubusercontent.com/33840759/180790641-375f0c3e-9bde-4e9f-a89a-b5f78f9f2fe2.png)

    > 因Teams App要Tab顯示內容,包含的網頁必須是有 __SSL__ ,所以要為Angular加入憑證

15. F5執行
    - 執行時會看到 OUTPUT跑出下圖內容

        ![image](https://user-images.githubusercontent.com/33840759/180791651-71a5ef4a-f518-424a-b7b6-f59cbddd5f9d.png)

    - compiler成功後就會自動開啟網頁版Teams

        ![image](https://user-images.githubusercontent.com/33840759/180792325-737075f3-d75c-47de-a4fa-c8014972ba5e.png)

    - 因選擇Add to a teams(加入到團隊頻道), 故此處要選擇團隊頻道

        ![image](https://user-images.githubusercontent.com/33840759/180792638-9c763695-abfc-48c6-8307-6f40d80186e3.png)

    - 此處顯示的是 `teams-config.component.html`的內容

        ![image](https://user-images.githubusercontent.com/33840759/180794247-e521c1cc-4141-474b-b69e-e8adc305318b.png)

    - 點選 about 裡面顯示的是 `manifast` 上 的內容

        ![image](https://user-images.githubusercontent.com/33840759/180794518-825852a3-705b-4d4f-8de3-264bae189314.png)

    - 加入安裝完後, 顯示的是 `tab.component.html`的內容

        ![image](https://user-images.githubusercontent.com/33840759/180794772-ec94b426-c7a5-4e78-87b9-7c37547139b1.png)

### 可能出現的錯誤

如果有跳出有關 `env` 的錯誤訊息的話, 較有可能的就是 需要 `env-cmd`或 `cross-env`這類套件(通常沒用到是不會有錯拉)

還有就是要從 Teams Toolkit內設定<br>

![image](https://user-images.githubusercontent.com/33840759/180797830-74fe2060-1e97-475a-aae9-7134e5f18275.png)

## 參考資料

- [The Will Will Web - 使用 Teams Toolkit 開發 Teams 應用程式：起手式](https://bit.ly/3osqLyN)
- [线下活动 | 从零开始开发一款属于你的Microsoft Teams小程序](https://bit.ly/3otWsb0)
- [Github - OfficeDev/TeamsFx](https://bit.ly/3Bg8V9W)
- [Github - microsoft/DevAppsForTeams](https://bit.ly/3ztGJiy)
- [MSDN - Teams Toolkit for Visual Studio Code](https://bit.ly/3vdqQKG)
- [MSDN - Build apps with the Teams Toolkit and Visual Studio Code](https://bit.ly/3J1Iyq7)
- [Microsoft Teams Blog - Build apps faster with the latest Microsoft Teams Toolkit](https://bit.ly/3cF8kUJ)
- [PnP Samples](https://bit.ly/3z5Hz3v)

