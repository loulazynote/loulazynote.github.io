# Git 學習筆記


位於索引中的檔案 : Tracked files(追蹤的檔案)
不再索引內的檔案 : Untracked files(未被追蹤的檔案)

> - working tree 是工作目錄
> - 只要 **被索引中的檔案內容** 跟 **工作目錄中的檔案內容**<span style="color:red;">**一致**</span>
>   就會被稱為一個 "_Stage_"狀態
> - 只要 **被索引中的檔案內容** 跟 **工作目錄中的檔案內容**<span style="color:red;">**不一致**</span>
>   就會被稱為一個 "_Unstage_"狀態
>   <br>

## Git 指令

![](https://i.imgur.com/xFkkXIE.jpg)

Command: **`git`**

- `-m` = message
- `init` = 建立
- `add` = 加入 GIT 索引
- `rm` = 刪除
- `status` = 看目前狀態

### `git diff`

_顯示差異_

- `HEAD` = 比對變更與目前最新版的變更差異
- `-cached` = 索引裡的變更與本地儲存庫最新的變更差異
- `--binary` = 比對二進位檔案差異
- `--name-only` = 比對工作目錄檔案,列出檔案名稱
- `--name-status` = 列出工作目錄及索引之間有多少檔案是被異動的

  > - `git diff 'src-commit' 'tgt-commit'` commit 與 commit 比較差異
  > - `git diff 'commit'` 與目標 commit 差異

### `git reset`

_重置索引_

- `-p` = 選取索引中的那些變更有哪些內容要從索引中移除
- `HEAD` = 重置索引及分支(加 ~1 是恢復前一版本,以此類推)
- `--mixed` = 重置 HEAD 及索引(預設值)
- `--hard` = 重置 HEAD,索引及工作目錄
- `--hard ORIG_HEAD` = 回復前版本(永遠回復 reset 之前的版本)

### `git revert`

_還原歷史上的版本，做相反的 commit_

- `-h` = help 列出功能
- `--abort` = 取消
- `--continue` = 繼續
- `--quit` = 停止

### `git clone`

_下載遠端儲存庫_

> - `git clone --no-checkout 'URL' 'Filename'` 會以 filename 為主並且不做 checkout
> - `git clone --bare 'url'` 把儲存庫的本身下載下來

### `git log`

_看紀錄_

- `--oneline` = 短的 log 版本
- `--oneline --graph` = log 版本+線圖可加 "-數字" 以顯示筆數

  > `git log --oneline --graph --all -10` 全部的歷史資料取 10 筆

### `git reflog`

_會記錄工作目錄底下做過的每一次的版控紀錄_

### `git checkout`

_取出/取回_

- `-- 'name'` = 從索引裡面把 name 檔案的內容整個複製回工作目錄
- `--orphan` = 建立全新分支且沒有 parents

  > `checkout -b "Filename"` 可以新增分支後切換

### `git switch`

_切換分支_

### `git clean`

_清除工作目錄_

- `-f` = 強制刪除
- `-d` = 刪除整個工作目錄
- `-x` = 執行清除任務,並忽略.gitignore 設定
- `-n` = 看那些檔案會被清除

### `git stash`

_暫存工作目錄的變更_

- `save 'message'` = 暫存
- `pop` = 取回暫存

### `git branch`

_查看分支/新增分支_

- `-d` = 刪除分支(只能刪除已經合併的分支)
- `-D` = 強制刪除分支
- `-r` = 列出所有遠端追蹤分支
- `-a` = 列出所有遠端+本地分支

  > - `git branch --merged` 列出所有已合併過的分支
  > - `git branch --no-merged` 列出所有未合併過的分支
  > - `git branch --merged | egrep -v "(^\*|master|develop)" | xargs git branch -d` 刪除所有已合併過的分支(CMD/Powershell 不可用)

### `git merge`

_合併分支_

- `--ff` = 快轉合併(預設)
- `--no-ff` = 非快轉合併
- `--ff-only` = 僅有快轉合併
- `--no-commit` = 不提交的合併
- `--abort` = 放棄合併
- `--squash` = 壓縮後合併(不可以與關閉快轉機制一起使用,且不會有合併線圖出現)

  > `git merge --no-ff --no-commit 'branchName'` 合併可以不 commit

### `git rebase 'commit_id'`

_重訂基底合併_

- `-i`
- `--continue` = 繼續
- `--skip` = 跳過
- `--abort` = 取消

### `git cherry-pick`

_正向挑選合併_

- `--continue` = 繼續
- `--quit` = 停止
- `--abort` = 取消

### `git push`

_推送至遠端_

- `--all` = 全部分支推送

  > - `git push -u origin master` 將本地分支(master)與遠端分支(origin)建立連結
  > - `git push origin --delete` 'branchName' 刪除遠端分支(要先把本地刪除)

### `git fetch`

_將遠端儲存庫的變更下載回來_

- `--prune` 將遠端已刪除的分支剪除

### `git pull`

_將遠端儲存庫拉下來_

> - `pull` = `git fetch` + `git merge`
> - `git pull --rebase` = `git fetch` + `git rebase`

### `git remote`

_遠端_

- `-v` = 列出網址
- `-h` = remote 的 help
- `set-url` = 更改網址

  > `git remote set-url origin 'new url'` 將舊網址改成新網址

### `git commit`

_建立版本_

### `git apply`

_套用版本(進版)_

- `--check` = 確認檔案
- `--reverse` = 退版(簡寫-R)

<br>
<hr>

### `git config`

_GIT 的設定_

> 加上 `--global` 可以設定

- `core.quotepath` = 改 false 可顯示中文
- `core.editor` = 查看目前編輯器

  > git 只能顯示 ASCII 的字元

- git bash 以外的環境要改中文
  `set LC_ALL=C.UTF-8`

  > - 更改環境變數 (永久儲存)
    > `setx LC_ALL C.UTF-8`
  > - MAC/Linux
    > `export LC_ALL=C.UTF-8`

## 衝突問題

顯示訊息:

1. content = 內容衝突

    > 建議用手動合併

## 參考資料

1. [為自己學 GIT-另一種合併方式（使用 rebase）](https://tinyurl.com/yfgcs498)

