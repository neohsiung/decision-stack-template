<!-- 正本。SPEC §來源標記 只留指標。各 harness 的保證載入通道 import 這一份。
     寫入程序 step 1 的門檻判定靠它。接線清單見 MANIFEST.tsv。 -->

## 決策棧來源標記與各來源參數（正本）

`origin` 記的是什麼揭露了這條原則。它是 provenance，不是分類。它的價值是這行統計：

```bash
grep -h '^origin:' */*.md | sort | uniq -c
```

「這條流程實際產出了什麼」只有這個欄位答得出來。所以它不隨後續事件改寫。改掉會讓統計失真。

以下把寫入者稱為「作者」。作者是人，也可能是替人寫入的 agent。

| `origin` | commit 前綴 | 什麼揭露了它 |
|---|---|---|
| `judgment_case` | `judgment:` | 對話中 plan 被實質打回、結果被糾正成「方向錯了」、追問揭露錯誤假設 |
| `code_review` | `review:` | review comment 抓到清單沒涵蓋的類型，或作者自己判斷過重 |
| `self_caught` | `self:` | 執行中有客觀證據推翻原本的做法，但沒有人糾正作者 |
| `elicited` | `elicited:` | 作者主動口述一個可複用的結構，沒有人在糾正任何事 |
| `external_reference` | `ref:` | 作者指定一個外部框架或方法論，例如書、文章、既有理論，要求整理進來。抽取的是外部的判斷，不是作者自己的 |
| `repo_sync` | `sync:` | 週期性掃作者指定的外部素材，例如組織的文化、postmortem、規範目錄 |
| `seeded` | — | bootstrap 批次。第一次建棧時一次性遷移進來的 |

兩個以上同時成立時，取離事件最近的那個，不建第二個檔。已經有檔案就在它的 `confirmed:` 追加
日期，`origin` 不動。防止分歧的機制是[寫入程序](write-procedure.md)的 dedupe。來源之間不排優先序，排了也擋不住分歧。

分界最容易混的兩組，各用一句話切開：

- `self_caught` 與 `judgment_case`：有沒有人開口。
- `elicited` 與 `judgment_case`：這次發話有沒有在糾正作者做的事。有就是 `judgment_case`。
  沒有、純粹在說明自己的模型，就是 `elicited`。

### `self_caught` 的門檻：要有客觀證據

它是沒有外部把關的兩條來源之一，會過度生產。執行過程中隨時可以宣稱「剛剛悟出一條原則」。
門檻是：必須有一個獨立於作者的東西推翻了作者原本的做法。檢查工具報出違反、查證結果與假設
相反、驗證步驟失敗，都算。

「覺得這樣比較好」不算。「注意到一個模式」也不算。沒有那個被推翻的瞬間，它只是意見，
留在當下的工作裡，不進這個 repo。

### `elicited` 的門檻：要有結構，而且說得出未來哪裡會用到

它是另一條沒有外部把關的來源，而且連被推翻的客觀瞬間都沒有。門檻自己立兩條，缺一不可：

1. 是可複用的結構。有多個軸、有序步驟，或明確判準。
2. 說得出未來哪個情境會因此改變做法。指得出具體情境，不是「以後應該有用」。
。
單一意見、個人偏好、當下的一個決定都不算。門檻不成立就不寫，也不在回覆裡報告「考慮過但沒寫」。
那會把每次閒聊都變成一段噪音。

## 各來源帶進寫入程序的四個參數

[寫入程序](write-procedure.md)所有來源共用。來源之間只帶四個參數進來：

| 來源 | `origin` | commit 前綴 | 寫 mem0 | 能不能 push |
|---|---|---|---|---|
| 判斷落差 | `judgment_case` | `judgment:` | 是，唯一會寫的來源 | 走分支、push、開 PR，不自己合併 |
| 主動口述 | `elicited` | `elicited:` | 否 | 同上 |
| 自己抓到 | `self_caught` | `self:` | 否 | 同上 |
| 外部框架 | `external_reference` | `ref:` | 否 | 同上 |
| 週期掃素材 | `repo_sync` | `sync:` | 否 | 同上 |

「能不能 push」對每個來源一樣。判準在[寫入程序](write-procedure.md) §兩道閘門，不由來源決定。
列在這裡是因為它是四個參數之一。

### 不先問確認

`judgment_case`、`elicited`、`self_caught` 三個來源都不先問確認。有基準，也就是掛得上既有
Model 或 Framework，就同一輪直接寫完，事後告知一句。唯一的例外是[寫入程序](write-procedure.md)
step 3 的基準閘門答不出來。那時照該步的兩條分支走：互動中把候選攤開討論，非互動寫進 `Pending/`。

理由：這三個來源都發生在對話進行中。停下來問會把「記錄一條原則」變成一次協商。協商的成本高到
讓門檻事實上升高，結果是該記的沒記。閘門放在合併那一關，不放在寫入那一關。

### `judgment_case` 的 mem0 呼叫形狀

它是唯一會寫 mem0 的來源，理由見[寫入程序](write-procedure.md) §mem0 鏡射閘門。scope 不寫死
在這裡。先 `stack config mem0.scope` 取，回非零代表本機沒設 mem0，整節不適用。

```
memory_store({kind:"judgment_case", scope:<stack config mem0.scope 的輸出>,
  metadata:{origin_name, trigger_type, domain, proposed_summary, actual_summary, rule},
  volatility:"stable", infer:false})
```

- `trigger_type` 是 `plan_rejection`、`result_correction`、`clarifying_pushback` 三選一。
- `origin_name` 用檔名的 kebab-case，不用底線。這是檔案與 mem0 兩側配對的唯一依據。
- `infer:false` 必填。mem0 的抽取是為第一人稱對話文調的，會丟掉宣告句與規則句，而且不報錯
。
`kind` 被拒代表 mem0 端還沒認得 `judgment_case`。不要退回 `kind:"decision"` 就算了，那會讓
兩側對帳失準。先照舊寫入，再用 `memory_update` 把 metadata 的 `kind` 補成 `judgment_case`，
並在回覆裡提一句 mem0 端需要更新。
