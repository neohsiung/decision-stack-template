# tools/context：保證載入片段與接線清單

規則的正本要住在有版控與審查的地方。各 harness 的載入通道只放指標。兩個載體必然各自演化，
所以這裡的檔案是正本，外面一律是指標，沒有任何一份手抄副本。

## 片段

| 檔案 | 內容 | 誰載入 |
|---|---|---|
| `usage-contract.md` | 什麼時候檢索、查詢句怎麼造、留痕行、降級行為 | 每個 session 開頭 |
| `write-procedure.md` | 七步寫入程序加兩道閘門 | 每個 session。跳過不會有訊號，不能只等人來查 |
| `source-marking.md` | 七個 `origin` 值的門檻與分界，各來源的四個參數 | 每個 session。寫入程序 step 1 的判定依據 |

片段不在掃描範圍裡。`stack` 只掃 `Models/`、`Frameworks/`、`Decisions/`、`Expressions/`，
所以片段不會被 `recall` 當內容檢索到，也不進 `MEMORY.md`。這是刻意的分層：強制執行的規則走保證載入通道，
可檢索的參考資料進語意儲存。兩者不能互相取代。

片段裡的判準是正本。README 對應三節只留 2 到 3 行指標。要改判準改片段，不改 README。

## 接線

`MANIFEST.tsv` 是清單，一列一個 `(harness, 片段)`。欄位：

| 欄 | 意義 |
|---|---|
| `harness` | 哪個 agent 工具 |
| `target_path` | 那個工具的保證載入通道 |
| `primitive` | `import`：`@<絕對路徑>` 行。`instructions[]`：JSON 陣列 append 絕對路徑。`inherit`：靠繼承上游指令檔拿到片段，沒有自己的接線點。`unwired`：宣告的缺口 |
| `fragment` | 指向本目錄的哪個片段 |
| `note` | `unwired` 列的宣告理由。其他列寫 `-` |

驗證日期不在這張表裡。它是本機的證明，記在 `tools/local/verified.tsv`，每台機器一份，不進版控。
`stack doctor` 讀那一份。

接上了不等於生效。死接線沒有訊號。驗證方法固定：在該 harness 開一個 cwd 在 `/tmp` 的新 session，
請它原文引用只存在片段裡的一句話，例如 usage-contract 那張表的 ✗ 主題句。引得出來才填日期。
`inherit` 列也要各自驗。上游載入了不等於這條通道也載入了。

驗證前先確認上游指令檔裡沒有手抄本。手抄本會餵出假通過。

本機有哪些 harness、哪些沒裝、驗過哪些，記在 `tools/local/wiring-notes.md` 與 `verified.tsv`。
`verified.tsv` 缺的列不是失敗，是還沒證明。換到裝有該 harness 的機器時補跑同樣的探針。

## 怎麼裝

```bash
git clone <remote> <你選的位置>        # 位置隨你，工具從自身位置推導 repo root
cd <你選的位置>
ollama pull bge-m3                     # 嵌入模型
tools/stack install --yes              # 接線、建 PATH 上的 stack shim、設 hooksPath
tools/stack index                      # 建向量索引，純衍生物，不進版控
cp -r tools/local.example tools/local  # 本機區，填 verified 與筆記
```

接線的兩個命令：

```bash
stack install          # 預設 dry-run 只列計畫。加 --yes 才寫入
stack doctor           # 體檢接線是否還活著。唯讀，含死接線對帳
```

## 硬規則

- 不接在任何你不擁有的版控樹內的檔案。專案內的指令檔一次 `git add .` 就會把個人路徑
  commit 進那個 repo。
- 不碰含金鑰的設定檔。`stack install` 遇到會拒絕。hook 註冊維持文件化的手動步驟。
- 片段改過就重開 session 再依賴它。harness 在 session 開頭快照指令檔，而 `recall` 讀的是
  工作目錄。中途改片段會讓 session 跑在舊載體上。
- 沒有使用者層檔案通道的 harness 是宣告的缺口，不是待辦。記成 `unwired` 列。
