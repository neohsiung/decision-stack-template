---
name: <與檔名同的 kebab-case 主張句>
description: <一行摘要。這一行決定檢索時的相關性判斷，不是裝飾>
type: model
origin: <seeded | judgment_case | code_review | self_caught | elicited | external_reference | repo_sync>
recorded: <入棧當天，YYYY-MM-DD。不回填>
---

## 信念

**<一句話。只講一個意思，一個子句。不用破折號、分號、冒號接第二個意思。這一節只有這一句。>**

## 適用界線

<在什麼範圍內成立。>

## 不適用的情況

<什麼時候不要拿它判斷。想不出來就寫「待補」，不要刪掉這一節。>

## 來由

<為什麼這樣想。換掉所有情境與步驟仍然成立的理由，寫給 review 的人。
有外部出處時第一行寫 `出處：[名稱](URL)`。>

<!--
Model 不設 upstream，它是最上游。stack lint 會擋。
預期規模 5 到 15 筆。長出檢查面或步驟清單時，它其實是 Framework。
信念、適用界線、不適用的情況是機器層，agent 讀到這裡就夠；來由是人層。
-->
