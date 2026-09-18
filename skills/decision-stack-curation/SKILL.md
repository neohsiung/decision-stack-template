---
name: decision-stack-curation
description: 個人決策棧的週期性覆盤與整理程序。機械訊號（lint、shape、eval、usage）→ 逐條檢視 → 分支上套用升降級、合併、拆分、淘汰 → 回歸驗證 → 覆盤報告。觸發語句範例：「整理決策棧」、「決策棧覆盤」、「跑一次 curation」、「檢視決策棧分層」、「決策棧該收斂了」、「查棧」、「棧裡有沒有」、「recall 一下」。
---

# decision-stack-curation：決策棧覆盤程序

判準的正本在 repo。分層與四個異動方向見 SPEC §各層契約。寫法見 WRITING §寫作指南。寫入程序
與兩道閘門見 `tools/context/write-procedure.md`。本 skill 只固化執行順序與產出形態，不複製判準。
複製會養出第二份各自演化的版本。

對象：執行覆盤的 AI agent。產出：一條 `curation/<日期>` 分支的逐筆 commit，加一份覆盤報告。
交人在 PR 上審查合併。不自行合併。

## 使用時機

- 每月覆盤，例行。
- 單層條目明顯累積時。Decisions 自前一輪覆盤增加 10 筆左右是一個參考值。
- PR review 產生批量修正之後。review comments 指向系統性問題時。
- 大批拆分、合併、或改動嵌入模型之後。這種情境重點跑 Step 7 的 eval 回歸。

本 skill 放在 repo 的 `skills/`，屬承載層。它服務這份內容本身，內容搬到哪它跟到哪。
harness 端用 symlink 指到這裡，正本只有這一份。怎麼接見 `tools/context/README.md`。

## Step 0：前置檢查

1. `cd "$(stack root)" && git fetch origin`。確認在 main、與 origin/main 同步、工作區乾淨。
2. 服務先救起來。`curl -m 60 http://localhost:11434/api/embed -d '{"model":"bge-m3","input":["ping"]}'`
   通了才繼續，沒跑先 `ollama serve`。大批重嵌很慢，數百視角要幾十分鐘。超時不等於服務掛了。
   放背景等，不要改走降級路徑。
3. 未合併分支盤點。`git branch --no-merged origin/main`。回報累積幾條與最舊一條的日期。
   兩道閘門要求：積壓是待辦不是狀態。分支上的條目不在這次整理範圍，它們還沒過審。

   要判斷某條分支的內容是否已進 trunk，例如 squash merge 之後，用逐檔存在性查詢：
   `git cat-file -e origin/main:<path>`。不要只用 diff 比較。diff 型的檢查寫錯比較對象時
   會退化成恆真，對每條分支都回答「可刪」，而刪除不可逆。
4. `Pending/` 非空時先清再整理。逐筆跟使用者過基準閘門的兩問：定基準入棧，或使用者明示裁決
   留孤兒。清完才進機械訊號。待議條目不在索引裡，晾著就是流失。
5. 開分支：`git checkout -b curation/$(date +%F)`。

## Step 1 到 4：工具可算出的指標

1. `stack lint`。先修結構錯誤：索引斷鏈、frontmatter、upstream 方向、詞彙表雙向、壞連結。
   每修一類一個 commit。lint 尾端的 upstream 覆蓋率缺口清單是本輪收斂的原料。目標是全連，
   但不為單筆孤兒硬造 1:1 復述的上游。
2. `stack shape`。收斂候選是群距離過近的，拆分候選是內在凝聚度低的。讀法照工具輸出：
   Frameworks 天生並列教訓，凝聚度低屬正常；Models 與 Decisions 低才是缺陷。
   只看標 ★ 的收斂候選。標「父子／兄弟，非候選」的是刻意建的結構，上下游本來就語意相近。
3. `stack eval`。記下基線：top-3、top-5、平均 rank，與檢索吸子清單。用 `--save-baseline`
   存檔，不抄進文件。
4. `stack usage`。淘汰候選是 log 覆蓋不小於觀察窗、零命中、且無 confirmed 的條目。
   逐筆過人工判準，見 SPEC §各層契約·淘汰。處置與「沒動的候選加理由」都寫進覆盤報告。

## Step 5：逐條檢視，五問

每筆條目過五問。判準一律回 README 查，不憑印象。

1. 層級歸屬。它回答的問題屬於哪一層，見 §各層契約 目標欄。通不過本層判準就升級或降級。
2. 上行接線。該連的 `upstream:` 連了嗎。多筆同構是抽上游的訊號，shape 佐證。
3. 分節與首句。分節照該層固定順序了嗎。首句只講一個意思嗎。見 §寫作指南。
4. 讀者知識落差。有沒有只有作者懂的指涉。讀者從哪裡知道這個。外部術語首次出現連了
   `GLOSSARY.md` 嗎。新術語照收詞判準補節。
5. 連結分工。族譜在 `upstream:`。近似但不等價的對照在 frontmatter `related:`，寫了分界句。
   正文不留「相關：」段。

條目多時按層平行分派 subagent 產出判定清單，套用集中回主線。frontmatter 沿用規則與 commit
粒度要一致。派出讀取型 subagent 期間不切換分支：它們讀的是工作目錄，切分支等於在它們讀到
一半時換掉輸入。非切不可時，把 agent 需要的判準全文內嵌進 prompt，不叫它自己開檔案對照。

## Step 6：套用改動

- 升級，也就是抽上游：新上游條目 `origin` 與 `recorded` 沿用組內最早母條目，正文列實例。
  成員加 `upstream:`。清掉成員間因此冗餘的橫向對照。前綴 `converge:`。
- 移層：`git mv`，改 `type:`，更新 `MEMORY.md` 與所有指向連結。`origin` 與 `recorded` 不動。
  前綴 `move:`。
- 合併：留存檔 `confirmed:` 追加日期。被併檔獨有的內容整合後刪檔，清索引行。前綴 `merge:`。
- 拆分：新檔 `origin` 與 `recorded` 沿用母條目。前綴 `split:`。
- 淘汰：優先合併進上游或相鄰條目，其次刪檔加清索引行。前綴 `retire:`。
- 一律明確路徑 `git add`。不用 `-A`，那是去識別化風險。
- 詞彙表新術語的外部連結要實抓一次驗證。死連結在詞彙表裡特別貴。

## Step 7：回歸驗證與報告

1. `stack lint` 全綠。索引過期屬資訊性，跑一次 recall 讓它增量重嵌即消。
2. `stack eval` 對照 Step 3 基線，不得變差。變差就找出是哪筆改動，修或回退。

   對合併後的整體跑，不對單一分支跑。分支各自 base main 時，每條單獨看都可能全綠，合起來
   才是使用者拿到的狀態。本機開一條 `tmp/eval-all`，把各分支 `git merge` 進去，在那裡跑
   lint 與 eval，驗完丟掉不推送。合併衝突也會在這裡先浮現。

   本輪新抽的上游要檢查它有沒有變成吸子。新條目的用詞若帶通用判斷語彙，例如順序、收件人、
   外發、優先，會在語意空間裡散到不相關的鄰居上，擠掉既有答案。修法是換掉那幾個詞。
   不改 eval 的期望值，`regression.tsv` 的立意是不為分數好看而動它。
3. 覆盤報告，結論先行：動了什麼，各異動方向的筆數；upstream 覆蓋率前後；eval 前後；
   沒動的候選與理由，下次覆盤不用重想。
4. push 並分批開 PR。一個 PR 一個異動類型或一個收斂組。完整範圍的上限是一次 review 讀得完。
   一輪整理塞成一個大 PR 等於沒有被審。到此為止。合併在人看過 diff 之後。
   - gh 操作一律用 `GH_TOKEN=$(gh auth token --user "$(stack config github.account)")` 前綴。
     不切全域作用中帳號。作用中帳號可能是別的身分，切換靠人記得會漏。
   - 不開鏈狀 PR。每個 PR 的 base 一律是 main。兩個 PR 都要改 `MEMORY.md` 同一節時，
     排先後：第一個合併後 `git pull` 再切第二條分支。索引行的衝突幾秒能解。鏈狀交付的失效
     沒有訊號：base 指向已合併分支的 PR 合下去，內容只進該分支不進 main，MERGED 標記照樣亮。
     擋得住的時候選標記就是自願降級。規則因此是「一開始就不要鏈」，不是「合併時記得改」。
   - 合併後驗內容有沒有真的進 main，不看 MERGED 標記：
     `git fetch && git cat-file -e origin/main:<這個 PR 新增的檔>`。標記回答的是「有沒有併進
     它的 base」，不是「內容有沒有到主線」。

## skill 自身的優化

每次執行發現程序缺陷，直接改本檔。變更理由寫進 commit message，版本紀錄看 git history。
skill 也受收斂約束：建立新 skill 前先 dedupe，一個 skill 一個目的，可增可減。
