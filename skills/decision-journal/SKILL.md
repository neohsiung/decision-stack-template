---
name: decision-journal
description: 要做一個結果會揭曉的判斷時用。下決定的當下寫一筆決策日誌（預測、信心機率、依據的條目、什麼情況算錯、回看日），到期回看時先拆層再評分，落在認知的那一層依寫入程序產條目。觸發語句範例：「記一筆決策日誌」、「這個決定先寫下來」、「對一下之前的預測」、「回看那個決定」、「這件事三個月後要對答案」。
---

# decision-journal：每個判斷一筆，結果出來再對答案

判準的正本在 repo。為什麼只評推理，見 `Models/an-outcome-does-not-grade-the-decision.md`；欄位見
`Frameworks/decision-journal-before-outcome.md`；迴圈要有三環，見 `Frameworks/calibration-loop-links.md`；
回看時怎麼拆層，見 `Frameworks/outcome-attribution-by-cognition.md`。本 skill 只固化動線與產出形態，
不複製判準。複製會養出第二份各自演化的版本。

對象：陪使用者下判斷與回看的 AI agent。產出：一筆日誌，存在使用者指定的地方；回看時可能多一筆
`judgment_case` 或 `self_caught` 條目。日誌本身不進棧，它是事件，棧只收原則。

## 使用時機

- 這個決定有時限，幾個月內會知道對不對，而且事後會想知道當初怎麼想的。人事、方向、長期投入、
  大額承諾都算。
- 使用者說「先寫下來」「之後對答案」。
- 到期回看。提醒系統把日誌帶回來的那一刻。

不用在每天的小決定上。日誌的摩擦要低到願意寫，寫太多就不會回看，迴圈的第二環就斷了。

## 寫一筆：先談，談到五欄成形才建檔

使用者不是來填表的。agent 的工作是用對話把這個決定理解到能寫出五欄，然後替他寫。

1. **先聽。** 使用者描述決定時不打斷。聽完用一句話複述「你要決定的是 X，不是 Y」，錯了讓他改。
2. **問五個面，不是固定題目。** 每一面挑合當下脈絡的問法，一次最多兩題，問到那一面能寫成一行就換下一面：
   - 決定本身與替代方案：還有哪條路沒選，為什麼不選。
   - 預期的機制：你認為會發生什麼、為什麼會那樣、靠哪個條件成立。
   - 算錯的條件：看到什麼你會承認錯了；多久之內看得出來。
   - 依據：靠哪個判斷原則、哪個先例，還是憑感覺。agent 自己先跑 `stack recall --expand "<這個決定在判斷什麼>"`，
     把命中的條目拿出來對，問「這條你有沒有在想」。
   - 場域與回看日：公司、個人、生活；什麼時候回看最有意義。
3. **信心數值不直接問。** 從對話整理出三件事再提數字：基率（同類的事以前幾次裡成幾次）、要同時成立的條件
   有幾個、哪些在自己手上哪些不在。然後說「寫 X%，理由是這三點；比這高或低？」使用者只調整。數字跟
   三點理由一起進「預測」節。
4. **收尾。** 複述五欄，使用者點頭才 `stack journal new "<一句話>" --domain … --days …`，再把對話整理出的
   內容填進檔，commit。整段對話不超過十個來回；超過代表決定還沒成形，記成「待成形」，不硬寫。
5. 日誌 repo 的位置在 `tools/stack.config.toml` 的 `[journal] path`，它是獨立的私有 repo，commit 就是存。
6. 回看日由 `stack journal due` 帶回來。每日的 GTD 排程跑它一次，有到期的列進待裁決區，並在任務系統建
   一張只有標題與路徑的指標任務；生活場域的決定不經任務系統，直接看 `due` 的輸出。

## 回看：到期那天

1. 先讓使用者講結果與他的感受，不先給答案。再 `stack journal review <檔>`，它補上「回看」區塊、把狀態改成
   reviewed。接著一起讀日誌。順序是結果、日誌、對答案；先看日誌再問結果，結果會被推理牽著講。
2. 對答案：結果落在預測的哪一邊；「算錯的條件」那一行成立了沒。
3. 拆層。走 `Frameworks/outcome-attribution-by-cognition.md`：缺口落在下決定那一刻的外部世界，判斷不改，
   只更新機率；落在那一刻的認知，進下一步。
4. 落在認知的，依 `tools/context/write-procedure.md` 寫一筆。別人糾正的是 `judgment_case`，自己對
   出來的是 `self_caught`。origin 的門檻見 `tools/context/source-marking.md`，自報的「悟到了」不算。
5. 推理站得住而結果不如預期，日誌寫「運氣」，判斷留著。這是這套迴圈存在的理由：
   讓結果好的壞判斷與結果壞的好判斷分得開。

## 日誌格式

正本在 `templates/journal-entry.md`，`stack journal new` 照它建檔。frontmatter 六欄：type、decision、
domain、date、review_on、status；正文三節：預測、依據、算錯的條件。回看時 `review` 補第四節：

```
## 回看
結果      <實際發生什麼>
對答案    <結果落在預測的哪一邊；算錯的條件成立了沒>
落點      外部 ｜ 認知（策略 ｜ 人員組成 ｜ 執行）
下一步    更新機率 ｜ 寫入棧：<條目名> ｜ 運氣，判斷留著
```

`stack journal stats` 數 open、已回看、寫進棧、判為運氣各幾筆。寫進棧的那一欄是這套迴圈有沒有
在產出原則的唯一數字。

信心一律寫數字。「應該會」「大概」會把一成壓成零或壓成是非，見
`Frameworks/a-probability-is-a-number-not-a-verdict.md`。

## 跟其他兩個 skill 的分工

| skill | 什麼時候 | 產出 |
|---|---|---|
| decision-stack-bootstrap | 棧是空的 | 第一批條目 |
| decision-journal | 每個結果會揭曉的判斷 | 一筆日誌，回看時可能一筆條目 |
| decision-stack-curation | 每月，或單層明顯累積 | 升降級、合併、拆分、淘汰 |

三個都放在 repo 的 `skills/`，屬承載層。harness 端用 symlink 指到這裡，正本只有一份，接法見
`tools/context/README.md`。
