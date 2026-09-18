# examples

一組三筆的完整推導鏈，示範形狀用，不是內容。

| 檔 | 層 | 它示範什麼 |
|---|---|---|
| `an-estimate-hides-what-is-unknown.md` | Model | 一句信念加兩條界線，沒有事件段 |
| `estimate-by-pricing-the-unknowns.md` | Framework | 有序步驟加停止條件，`upstream:` 指向那個信念 |
| `a-single-number-becomes-a-promise.md` | Decision | 規則、事件、為什麼、怎麼用四節，`upstream:` 指向骨架 |

三筆合起來回答同一件事的三個層次：為什麼這樣想、怎麼下判斷、這次該怎麼做。

**照著看，不要照著搬。** 內容是為了示範分節與 `upstream:` 而寫的，判斷原則要來自你經歷過的事件。
搬過去會變成別人的結論擺在你的棧裡，而檢索的時候你認不出它從哪來。

要試跑的話把三個檔複製到 `Models/`、`Frameworks/`、`Decisions/`，跑 `stack lint` 與
`stack tree` 看推導鏈接起來的樣子，看完刪掉。
