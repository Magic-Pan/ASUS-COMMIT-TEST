自動記錄commit資訊
1. 觸發時機：PR Marged
2. branch：在branchtrack.txt文件內文所列的branch進行PR Marged，沒有在文件內文的branch PR marged則不觸發。
3. 只針對team_users.txt內指定Team人員所發的PR才觸發。
4. 文件格式：csv檔
5. 文件欄位：
    - No：流水號
    - PR Marged日期：yyyy-mm-dd 
    - commit message
    - commit id
    - Marged branch
    - PR日期：yyyy-mm-dd
    - action：如果是 marged，紀錄 Marged；如果是 revert，紀錄 revert。

補充說明：
- Project repo：project，為執行 PR Marged 的 branch。
- Document repo：document，commit-log.csv 檔案紀錄於此。
- 當觸發時，依據文件欄位紀錄 PR Marged 時 commit 與 PR 相應的資料：
    - 取得 PR Marged 日期（yyyy-mm-dd）
    - 取得 commit message
    - 取得 commit id
    - 取得 Marged branch
    - 取得 PR 建立日期（yyyy-mm-dd）
    - 依序編號 No（流水號）
    - 以 CSV 格式寫入 document repo 的 commit-log.csv 檔案

=======================================================
你可以依以下步驟在 GitHub 上實現此需求：

1. **建立 GitHub Actions workflow**  
   在 project repo 的 `.github/workflows/` 資料夾新增 workflow 檔（例如 `pr-merged-log.yml`）。

2. **設定 workflow 觸發條件**  
   使用 `pull_request` 事件，並設定 `types: [closed]`，判斷是否為 merged 狀態。

3. **判斷 branch 與 PR 作者**  
   workflow 內讀取 `branchtrack.txt` 與 `team_users.txt`，確認 branch 與作者是否符合條件。

4. **收集 PR 與 commit 資訊**  
   取得 PR Marged 日期、commit message、commit id、Marged branch、PR 建立日期等資料。

5. **將資料寫入 document repo 的 commit-log.csv**  
   使用 workflow 的 `actions/checkout` 及 `git` 指令，將 document repo clone 下來，寫入 commit-log.csv，並 push 回 document repo。

6. **範例 workflow 步驟**  
   - 觸發時判斷條件
   - 收集資料
   - 更新 document repo 的 commit-log.csv
   - commit & push

這樣就能自動依據需求，在 PR Marged 時記錄 commit 與 PR 資訊到 document repo 的 commit-log.csv。


你可以依下列格式撰寫 `branchtrack.txt` 與 `team_users.txt`：

### branchtrack.txt  
每行一個 branch 名稱，**完全符合 PR 合併的目標 branch 名稱**，無需加引號或其他符號，例如：

````plaintext
main
develop
release
feature/abc
````

---

### team_users.txt  
每行一個 GitHub 使用者帳號（login），**完全符合 PR 作者的帳號**，無需加引號或其他符號，例如：

````plaintext
alice
bob
charlie
````

---

這樣 workflow 就能正確判斷 branch 與作者是否符合條件。
