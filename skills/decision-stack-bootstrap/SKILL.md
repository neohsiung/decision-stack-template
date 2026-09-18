---
name: decision-stack-bootstrap
description: 空的決策棧要長出第一批條目時用。從既有素材反向萃取三條通道（外部框架、掃素材、回顧訪談），產出最小自洽集合 3 Models ＋ 3 Frameworks ＋ 5 Decisions，並說明 bootstrap 批次對基準閘門的例外。觸發語句範例：「決策棧怎麼開始」、「跑 bootstrap」、「棧是空的」、「第一批條目」、「幫我建決策棧」。
---

# decision-stack-bootstrap：空棧的第一批條目

判準的正本在 repo。分層見 SPEC §內容分層，寫法見 WRITING §寫作指南，寫入程序見
`tools/context/write-procedure.md`。本 skill 只處理一件事：棧是空的時候，第一批條目從哪裡來。

對象：陪使用者開棧的 AI agent。產出：一批條目，加一次通過的 `stack lint` 與一次有命中的 recall。

## 空棧有兩個問題

**第一個是規格上的死結。** 寫入程序 step 3 要求新 Decision 掛 Framework、Framework 掛 Model。
空棧裡沒有任何 Model 可以掛，所以第一筆條目照規格寫不出來。

**第二個是檢索上的。** `recall` 預設回 top-5。條目少於 5 筆時每次都回全部，等於沒有檢索。
這是「這套系統從第幾筆開始有用」的具體數字。

## bootstrap 例外

**同一個 bootstrap 批次內，Decisions 可以先寫、Models 後抽。批次結束前補齊 `upstream:`。**

這條例外只在 bootstrap 批次內成立，不是常設豁免。批次結束後回到常規閘門：新 Decision 一律
先有基準才建檔。

`stack lint` 的 upstream 覆蓋率缺口清單是批次結束前的檢查表，`stack tree` 印出來沒有孤兒才算完。

## 目標：3 Models ＋ 3 Frameworks ＋ 5 Decisions

為什麼是這個數：基準閘門要求每個 Decision 掛 Framework、每個 Framework 掛 Model。3／3／5 是
能全部掛滿的最小形狀。而 5 筆 Decisions 讓 `recall` 的 top-5 開始需要做選擇。

不用湊數。萃取得出來幾筆就幾筆，掛不滿就少抽一副骨架。

## 三條萃取通道

**不要問「你有哪些原則」。** 憑空想會產出「我覺得這樣比較好」，而那正是 `source-marking.md` 的
`self_caught` 與 `elicited` 門檻明確擋掉的東西。真正的判斷原則只能從真實事件反向萃取。

三條通道對應現有的 `origin` 值。速度與價值相反，先跑快的讓棧活起來，再跑慢的。

### 通道一：外部框架（`origin: external_reference`）

最快，不需要素材。問三題：

- 下決定時腦中常浮現哪套方法論、哪本書、哪個框架？
- 它主張什麼？用一句話說。
- 它在什麼情況下不管用？

第三題最重要。答得出失效模式的才是真的在用它，答不出的只是聽過。

產出 2 到 3 個 Framework，加上它們共用的 1 到 2 個 Model。外部方法論的 Model 通常是
「為什麼這套方法值得用」那一句。

### 通道二：掃既有素材（`origin: repo_sync`）

產量最大。素材是使用者指定的目錄：事故檢討、個人筆記、過去的 review 留言、PR 討論、
交接文件。

逐份問：這份東西裡有沒有一句話，換掉裡面所有專案名與人名之後還站得住？有就是候選。

**掃出來的候選要當場過去識別化的兩組問題**，見 SPEC §去識別化判準。素材多半來自組織情境，
這一步不能留到最後。

### 通道三：回顧式訪談（`origin: judgment_case`）

最慢，價值最高。一次訪談產 1 到 3 條。問句形狀固定：

- 最近三個月，哪一次你的計畫被打回，或結果被糾正成「方向錯了」？
- 原本以為什麼？
- 實際是什麼？
- 下次碰到什麼情況，你會因為這件事做得不一樣？

第四題答不出來就不要寫。那代表這件事還沒有變成可遷移的規則。

`judgment_case` 是唯一會寫 mem0 的來源，形狀見 `tools/context/source-marking.md`。

## 步驟

1. 確認 `stack init` 跑過：三層目錄、`MEMORY.md`、`GLOSSARY.md`、`tools/local/` 都在。
2. 跑通道一。每筆用 `templates/` 的骨架起稿，分節照該層固定順序。
3. 跑通道二或通道三，看使用者手上有什麼。素材多走二，素材少走三。
4. 每寫一筆就跑 `stack recall "<這條新規則>"` 做 dedupe。空棧初期會撈不到東西，那是正常的，
   但到第五筆之後它開始有意義。
5. 批次結束前補齊 `upstream:`，跑 `stack lint` 與 `stack tree`。
6. `stack index`，然後跑一次 recall，把命中行印出來。那一行證明棧活著。

## 上路的節奏

一次開放全部來源，新手會用 `elicited` 灌進一堆意見。分階段：

| 階段 | 做什麼 |
|---|---|
| 第一天 | `stack init` 加這個 bootstrap |
| 第一週 | 只檢索，不寫入。每次接到任務就 `stack recall`。建立肌肉記憶 |
| 第二週起 | 開始寫入，但只開 `judgment_case` 一種來源。門檻最清楚，最不會產生噪音 |
| 一個月後 | 開放其餘來源。這時你已經知道什麼樣的東西值得進來 |

第一週不寫入是刻意的。檢索的習慣沒有建立起來，寫進去的東西不會被撈出來用，那等於沒寫。
