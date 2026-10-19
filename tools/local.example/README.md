# tools/local — 本機區

這個目錄不進版控。裝的是換一台機器或換一個人就不成立的東西。判準寫在 SPEC §去識別化判準
的第二組問題。

| 檔 | 裝什麼 |
|---|---|
| `verified.tsv` | 各 harness 在本機驗過真的載入片段的日期。`stack doctor` 讀它 |
| `wiring-notes.md` | 本機的接線筆記：裝了哪些 harness、哪些沒裝、帳號怎麼配 |
| `incidents.md` | 事故的證據段：PR 號、commit、當天發生什麼。規則本身留在條目裡 |
| `denylist.txt` | 不可出現在可升級區的具名字串，一行一個。`stack lint --shippable` 讀它 |
| `orgs.tsv` | 組織代碼、真名、git remote 樣式。`stack lint` 擋真名出現在條目，`stack context` 用 remote 推斷組織 |
| `projects.tsv` | 每個專案的 org、role、field、文件語言。`stack context` 的第一層 |
| `context.toml` | 裝置的預設帽子。`stack context` 的最後一層 |

第一次用：`cp -r tools/local.example tools/local`，再填。
