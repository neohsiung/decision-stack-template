# decision-stack

[English](README.en.md)

**存放你自己判斷原則的地方，換工作時整份帶著走。**

三層合稱決策棧。「決策」取「判斷」的廣義，不只指 `Decisions/` 那一層。跟任何組織無關，
內容不含組織專屬的專案名、人名、系統名或機密數字。

## 為什麼

在組織裡累積的東西分兩種。信任、關係、脈絡綁在組織，換環境就歸零。被校準的判斷力綁在人，
換環境時跟著走。

這個 repo 存的是判斷力那一種。

## 跟筆記系統的差別

筆記系統只要求你寫下來。這裡多要求一件事：**說得出這條原則的上游是什麼。**

每一筆判斷原則要指名它走的是哪副骨架，每一副骨架要指名它背後是哪個信念。兩題都答得出來才
建檔，而 `stack lint` 會把答不出來的那些列出來。

少了這道閘門，寫下的東西會愈積愈多，而它們彼此無關，下次遇到新問題還是從零開始想。

三層是：

| 層 | 放什麼 | 回答 |
|---|---|---|
| `Models/` | 少數不變的信念 | 為什麼這樣想 |
| `Frameworks/` | 思考與作業骨架 | 怎麼下判斷 |
| `Decisions/` | 由事件揭露的原則 | 這次該怎麼做 |

另有 `Expressions/` 放一格產出該用什麼詞、什麼語氣、放哪些內容，照你本人的講法寫。
它不是第四層，不在推導鏈上。

## 快速開始

```bash
git clone <這個 repo> my-stack && cd my-stack
ollama pull bge-m3                     # 嵌入模型，recall 要用
tools/stack init                       # 建目錄與索引。已存在的檔案一律不覆蓋
tools/stack install --yes              # 接線、建 PATH 上的 stack shim、設 hooksPath
cp -r tools/local.example tools/local  # 本機區。沒有它 doctor 每一列都停在「還沒驗過」
tools/stack index                      # 建向量索引。純衍生物，不進版控
stack doctor                           # 看接線與缺什麼
```

`install` 預設是 dry-run 只列計畫，加 `--yes` 才寫入。`doctor` 唯讀，含死接線對帳。

`init` 之後棧是空的，而空的棧寫不出第一筆。寫入程序要求新原則掛上游，這時還沒有上游可掛。
`skills/decision-stack-bootstrap/` 帶你從既有素材反向萃取出最小自洽集合。
`examples/` 有一組三筆的完整推導鏈可以照著看。

檢索需要本機的 Ollama 加 `bge-m3`。它不在的時候 `recall` 會明確報錯並提示改讀索引，不會回
一個看起來正常的空結果。

## 你會拿到什麼

```bash
stack recall "<一段話>"   # 語意檢索，top-k 加一跳連結鄰居
stack lint                # 分節、連結、索引涵蓋、推導鏈方向
stack lint --shippable    # 去識別化：這句話換一台機器、換一個人還成立嗎
stack eval                # 對你自己寫的題目跑檢索回歸，跟基線對照
                          # 要先 cp tools/regression.tsv.example tools/regression.tsv
stack usage               # 哪些條目從來沒被任何情境需要
stack prose <檔案>        # 量任意一段文字的句子形狀，對照棧內條目
stack tree                # 推導鏈的格狀結構與孤兒
```

以下四件事由程式檢查，不靠人記得：

- **推導鏈的完整性。** 孤兒與方向錯誤由 `lint` 與 `tree` 列出來。
- **去識別化。** `lint --shippable` 跑在會被複製出去的那一區，pre-commit 每次 commit 都跑它。
- **檢索品質。** 寫好 `tools/regression.tsv`（範例檔在 `tools/regression.tsv.example`）之後，
  改一批條目再跑 `eval` 跟基線對照，退步會出現在對照行裡。**語料不在的時候 `eval` 直接跳過，
  不會有任何檢查發生**，所以這一項要你先寫題目才成立。
- **淘汰。** `usage` 只報缺席，不自動刪。缺席要累積夠久才算訊號。

## 這個 repo 沒有內容

它是模板：工具、規格、程序、空目錄，加上 `examples/` 那組示範。條目是你的，留在你的 repo。

`stack upgrade` 把這裡的工具更新拉過去，`stack contribute` 把你對工具的改動送回來。

兩者都只碰**可升級區**，也就是這個模板負責維護的那些檔案：工具、規格文件、模板、協作條款。
你的條目、索引與詞彙表不在裡面，一個都不會被碰到。兩者也都不自動合併。

## 語言

工具的輸出、說明與必填分節標題都是繁體中文，`Models/` 的分節標題必須寫成「信念」「適用界線」
「不適用的情況」，`stack lint` 照這組字面檢查。換一種語言寫條目會過不了檢查。

方法論本身跟語言無關，English 讀者可以從 [README.en.md](README.en.md) 讀方法，
但工具現階段只服務中文。

## 不做的事

不引入任何 pip 依賴。不送任何內容到外部服務。工具只產候選與報告，拆分、合併、改寫全部人工
定案。完整清單在 `tools/stack` 開頭的模組說明裡。

## 往下讀

| 文件 | 內容 | 什麼時候讀 |
|---|---|---|
| [SPEC.md](SPEC.md) | 分層判準、去識別化、寫入程序、兩道閘門、目錄、檢索 | 查閱用。操作時遇到「這該放哪一層」「這句能不能公開」時翻 |
| [WRITING.md](WRITING.md) | 命名、正文結構、句子規則、frontmatter | 每次寫條目之前讀一次 |
| [examples/](examples/) | 一組三筆的完整推導鏈 | 寫第一筆之前看 |

