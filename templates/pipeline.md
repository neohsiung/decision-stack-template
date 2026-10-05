---
name: <與檔名同的 kebab-case，用動作或產物命名，例 weekly-status-report>
description: <一行摘要。這一行決定檢索時的相關性判斷。寫這條流程在做的事，用別人會問的講法>
type: pipeline
origin: <elicited | repo_sync | self_caught | judgment_case | external_reference>
recorded: <入棧當天，YYYY-MM-DD。不回填>
pipeline_kind: <技術 | 管理 | 後設>
cadence: <每日 | 每週 | 每月 | 每季 | 每半年 | 每年 | 事件觸發>
upstream: <選用。這條流程依據哪筆判斷，指進 Models/、Frameworks/ 或 Decisions/>
related: <選用。<條目檔名> — 那筆答〈一句〉；本筆答〈一句〉。常用來對照它產出的那一格 Expression>
downstream: <現在的實作在哪：harness repo 的排程任務、skill 或 agent 的名字。只寫名字與所屬 repo，不寫資源 ID>
---

## 觸發

**<一句話。什麼時候跑這條流程，一個子句。>**

<辨識訊號，或排程的時點。事件觸發的寫「收到什麼」。>

## 輸入

<讀哪些東西。用抽象能力名：任務系統、行事曆、郵件、文件、票務、程式碼平台、CI、監控、聊天室、
人資表單、session 紀錄、記憶層、決策棧。不寫系統名。>

## 步驟

1. <有序步驟，三到六步。每步寫做什麼、做完知道了什麼。>
2. <可以在別的工具上重做的寫法。「用腳本算」可以，「跑 xxx.py」不行。>
3. <閘門寫成問句：全過才往下嗎？>

## 輸出

<產出什麼、送到哪種渠道、給哪個角色（不具名）。>

## 需要的能力

<逗號分隔的抽象能力清單。重建時拿這一行對當下的工具，一個能力對一個工具。>

## 來由

<為什麼有這條流程、它取代了什麼、哪些細節是綁組織的（用通用詞描述）。
從某條 skill 或排程抽出來的，寫「通用半邊在這裡，綁定半邊在 harness repo 的哪個檔」。>

<!--
兩個軸必填，值要在受控詞彙裡。軸是組織鍵不是檢索鍵：它管 lint、管索引分組、
管矩陣看得出哪一格沒填。檢索走 description 加步驟的段落。

Pipelines 是目錄不是層。它不走基準閘門，不進 stack tree 的格狀結構，
也不進 upstream 覆蓋率的缺口報告。前五節是機器層，來由是人層。

去識別化三組問題都要過。資源 ID、帳號、組織代碼、人名一個都不進來；
那些放 harness repo 的 binding 檔，downstream 指過去。

這條在哪台裝置跑、實作在本機哪裡、上一次對帳是哪天，不寫在這裡。那是每台裝置自己的啟用表
（tools/local/pipelines.tsv），stack pipelines 維護。
-->
