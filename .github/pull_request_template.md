## 這個 PR 改了什麼

<!-- 一兩句。合併之後系統要是自洽的。 -->

## 為什麼

<!-- 問題是什麼。沒有問題就不需要這個改動。 -->

## 驗證

<!-- 跑了什麼、看到什麼。貼實際輸出，不寫「應該可以」。 -->

- [ ] `stack lint` 全綠
- [ ] `stack lint --shippable` 0 擋
- [ ] `stack doctor --self-test` 通過
- [ ] 改動檢索相關的東西時跑過 `stack eval`，命中持平或變好

## 送出前確認

- [ ] 不含第三方可識別資訊：同事姓名、專案代號、內部系統名稱、營收與人力數字
- [ ] 換一台機器、換一個人，這個 PR 加進去的每一句話都還成立
- [ ] commit 有 `Signed-off-by:`
- [ ] 沒有動 `Models/`、`Frameworks/`、`Decisions/`、`MEMORY.md`（那些不收，理由見 CONTRIBUTING）
- [ ] 這個 PR 一次 review 讀得完
