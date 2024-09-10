# 關於WSL的設定檔 - 不可不知的眉角


:exclamation:這個設定檔很重要:exclamation:

:exclamation:這個設定檔很重要:exclamation:

:exclamation:這個設定檔很重要:exclamation:

為什麼這麼說呢?

在前陣子我的電腦重灌後就遺忘了設定檔這件事，這幾天突然發現我的RAM的使用率大飆升,每天電腦都像飛機要起飛一樣，且導致電腦變的很慢。
查看工作管理員 才想到 我的 WSL 裝好後都沒有設定它，所以這裡記錄一下該設定的設定檔內容。

## WSL 2 的 `.wslconfig` 設定檔

- 編輯 `%UserProfile%\.wslconfig` 檔案

    ```cmd
    code %UserProfile%\.wslconfig
    ```

  > 沒有此檔案的話，可以自行建立一個在`%UserProfile%` 根目錄下

- 設定內容說明

    ```ini
    [wsl2]
    kernel=<path>              # 自定義 Linux 的絕對路徑
    memory=<size>              # 給予 WSL2 虛擬機的記憶體大小
    processors=<number>        # 給 WSL2 虛擬機分配的處理核心數
    swap=<size>                # 給 WSL2 虛擬機分配的交換空間 (設定 0 的話表示沒有給予交換空間)
    swapFile=<path>            # 自定義交換虛擬硬碟vhd的絕對路徑
    localhostForwarding=<bool> # 允許 WSL2 的port轉發至本地主機 (default true).

    # <path> 必須是反斜線的絕對路徑, for example: C:\\Users\\Ben\\kernel
    # <size> 必須在數字後加上單位, for example: 8GB or 512MB
    ```

    | 參數                  | 參數類型    | 預設值                                        | 說明                                                                                 |
    |---------------------|---------|--------------------------------------------|------------------------------------------------------------------------------------|
    | kernel              | string  | 微軟預設提供的 WSL 核心                             | 連接自定義 Linux 的絕對路徑                                                                  |
    | memory              | size    | Windows 全部記憶體的 80%                         | 給予 WSL2 虛擬機的記憶體大小                                                                  |
    | processors          | number  | 相同於Windows 本地主機的處理器核心                      | 給 WSL2 虛擬機分配的處理核心數                                                                 |
    | localhostForwarding | boolean | TRUE                                       | 是否應可通過 `localhost:port` 指定綁定到 WSL2 VM 中的通配符或本地主機的埠連接到本地主機的布林值 (允許 WSL2 的port轉發至本地主機) |
    | kernelCommandLine   | string  | none                                       | 額外的核心命令列參數                                                                         |
    | swap                | size    | Windows 上所有記憶體的的 25%                       | 給 WSL2 虛擬機分配的交換空間，設定 0 的話表示沒有給予交換空間                                                |
    | swapFile            | string  | `%USERPROFILE%\AppData\Local\Temp\swap.vhdx` | 交換虛擬硬碟vhd的絕對路徑                                                                     |

## 參考資料

1. [Release Notes for Windows Subsystem for Linux | Microsoft Docs - Build 18945](https://bit.ly/3xgTnz7)
1. [WSL 中的進階設定組態](https://bit.ly/3trK9yC)
2. [Dev on Windows with WSL - 配置 WSL 2 全局设置](https://bit.ly/3xk9ko5)

