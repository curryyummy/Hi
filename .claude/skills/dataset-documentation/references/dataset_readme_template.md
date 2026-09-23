
# [資料集名稱] (Dataset Name)

## 1. 標題與概述 (Title & Overview)
* **資料集名稱與識別碼**：請提供正式名稱及系統識別碼（Dataset ID / UUID）。
* **資料集摘要**：請用 2–3 句話簡要說明此資料集的核心主旨、建立目的及欲解決的業務/研究問題。
* **維護者／擁有者**：請填寫負責團隊、作者姓名及聯絡方式（電子郵件或 Slack 頻道）。
* **版本與修訂日期**：例如 `v1.2.0`，並附上本次版本建立或更新日期（YYYY-MM-DD）。
* **業務／研究背景**：此資料集適用的具體業務場景為何？預期的主要使用者與下游系統是誰？

---

## 2. 資料蒐集方法 (Data Collection Methodology)
* **目標母體與抽樣邊界**：
  * 母體的明確定義是什麼？抽樣邊界涵蓋哪些範圍（地理、時間、客群、系統範圍等）？
  * 採用何種抽樣機制？（例如：分層隨機抽樣、便利抽樣、系統日誌全量採集、主動報名）
* **蒐集工具與管道**：
  * 原始資料是由感測器自動採集、前端埋點、第三方 API、還是紙本/線上問卷自述？
  * 蒐集過程中是否有經過調查員介入或人工二次轉譯？
* **時間範圍與取樣頻率**：
  * 資料記錄的起迄時間（精確至年／月／日）為何？
  * 是單次橫斷面快照（Snapshot），還是具連續時間戳記的串流/縱貫資料（Streaming / Longitudinal）？

---

## 3. 資料清洗與前處理血統 (Data Lineage & Preprocessing Transformations)
本區塊詳述資料從原始狀態（Raw Data）轉變為目前發布狀態的完整轉換路徑，確保分析具備可重現性（Reproducibility）：

* **上游原始資料來源 (Raw Data Sources)**：
  * 原始資料儲存於何處？（例如：`s3://raw-logs/user_events/` 或 Production MySQL 表名）
  * 擷取資料的快照時間點或 Git commit 雜湊值為何？
* **資料清洗與過濾規則 (Cleaning & Filtering Rules)**：
  * **重複值處理**：去重的唯一鍵（Unique Key）定義為何？若有重複，保留第一筆還是最新一筆？
  * **遺漏值（Missing Values）策略**：
    * 哪些欄位缺失會直接剔除該列（Drop Row）？
    * 哪些欄位採取插補（Imputation）？採用的插補方法為何（如中位數、均值、KNN、向前遞補 forward-fill）？
  * **異常值與離群值（Outliers）判定**：
    * 判斷門檻為何？（例如：3 個標準差外、IQR 1.5 倍範圍外、或業務規則如「年齡 > 120」）
    * 判定為異常後是直接刪除、設為 NULL、還是進行截斷（Winsorization）？
* **特徵轉換與衍生變數 (Feature Engineering & Derivation)**：
  * 是否進行了縮放或正規化？（例如：Min-Max 縮放、Z-score 標準化、Log 轉換）
  * 衍生欄位（Derived Features）的計算邏輯與公式為何？（例如：`duration = end_time - start_time`）
  * 類別特徵如何編碼？（例如：One-Hot Encoding、Label Encoding、Target Encoding）
* **去識別化與隱私防護 (Anonymization & Privacy Preservation)**：
  * 是否包含個人可識別資訊（PII）？採取何種處理？（例如：雜湊 SHA-256 加鹽、遮蔽掩碼、K-匿名化）
  * 是否針對敏感數值進行了粗粒度分箱（Binning）或擾動加噪（Differential Privacy）？
* **前處理程式碼與管線資訊 (Pipeline & Scripts)**：
  * 執行前處理的程式碼儲存庫連結為何？（例如：GitHub Repo URL 與版本標籤）
  * 執行的 ETL 工具與排程框架為何？（例如：dbt, Apache Airflow DAG ID, Spark Job 名稱）

---

## 4. 資料字典 (Data Dictionary)
請明確列出每個欄位的結構定義與取值邊界，避免使用者產生語義混淆：

| 變數名稱 (Variable Name) | 資料型態 (Type) | 必填 (Nullable) | 說明 (Description) | 單位／合法取值區間 (Unit / Allowed Values) |
|---|---|---|---|---|
| `[範例: user_id]` | String | 否 (NOT NULL) | 系統使用者唯一識別碼，已透過 SHA-256 雜湊處理 | N/A |
| `[範例: account_status]` | Integer | 否 (NOT NULL) | 用戶帳號當前狀態代碼 | 枚舉值：`0 = 凍結`, `1 = 正常啟用`, `2 = 已註銷` |
| `[範例: session_duration]` | Float | 是 (NULL) | 單次連線停留時長 | 單位：秒 (Seconds)，範圍：`0.0 ~ 86400.0` |
| `[欄位名稱]` | [型態] | [是/否] | [請填寫業務含義、計算來源；若為類別變數請列出枚舉對照表] | [單位、格式或範圍限制] |

---

## 5. 使用限制與授權 (License & Usage Guidelines)
* **資料授權條款 (License)**：本資料集依據何種授權協議釋出？（例如：MIT, Apache 2.0, CC BY-NC 4.0，或僅限內部指定團隊讀取）
* **資料安全分級 (Classification)**：例如：公開 (Public) / 內部內部 (Internal) / 機密 (Confidential) / 嚴格機密 (Restricted)。
* **法規遵循狀態**：是否受 GDPR、HIPAA、CCPA 或本地個人資料保護法管轄？是否已取得授權同意（Informed Consent）？
* **預期合法用途與禁止事項**：
  * **允許用途**：例如特定主題的學術研究、模型離線訓練、報表分析。
  * **嚴格禁止**：禁止嘗試對匿名化資料進行去匿名化拼湊、禁止未授權之第三方商業轉售。

---

## 6. 已知限制與外推邊界 (Known Limitations & Caveats)
* **抽樣偏差與涵蓋盲區 (Sampling Bias)**：
  * 哪些特定客群、地區、時段未被包含在此資料集中？
  * 樣本是否存在倖存者偏差、自願者回報偏誤或嚴重的資料傾斜（Imbalanced Data）？
* **前處理帶來的資訊損失 (Information Loss)**：
  * 在清洗、去識別化或聚合（Aggregation）過程中，損失了哪些原本的細節特徵？
* **外推邊界警告 (Extrapolation Boundaries)**：
  * 本資料集的推論結論**絕對不能直接套用**於哪些母體、環境、不同文化或業務場景？
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTQ1MzA3ODU1MywtMzc0ODc2ODM5LC0xNj
k0Nzc4NDIzXX0=
-->