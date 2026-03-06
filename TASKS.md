# TASKS.md

<div align="center">

# 📋 任務清單

*日常任務自動化設定*

</div>

---

## 1️⃣ 電子帳單整理

> **規則描述：**
> 1. 搜尋 Gmail 中被轉寄來的帳單郵件
> 2. 檢查是否有「OpenClaw/已處理」標籤，沒有才繼續
> 3. 下載 PDF 附件
> 4. 嘗試開啟 PDF：
>    - 無密碼 → 讀取內容
>    - 有密碼 → 先用 H120097061 解鎖
>    - 無法開啟 → 先查 Sheet PASSWD 頁籤
>       - 查到密碼 → 嘗試解鎖
>       - 查不到 → 回報請用戶提供密碼，並記錄到 PASSWD
> 5. 提取重點資訊（金額、日期、機構名稱）
> 6. 寫入 Sheet OVERVIEW 頁籤
> 7. 加上「OpenClaw/已處理」標籤
> 8. 封存信件

### 🔐 PDF 解密方法（重要！）

**解密順序：**
1. 先嘗試無密碼開啟
2. 有密碼 → 先用 `H120097061` 解鎖
3. 失敗 → 查 Sheet PASSWD 頁籤
4. 查到密碼 → 嘗試解鎖
5. 查不到 → 詢問用戶

**使用 Python pypdf 庫：**

```python
from pypdf import PdfReader

# 嘗試順序密碼清單
passwords_to_try = ['H120097061']  # 先用預設密碼

# 查 PASSWD 頁籤取得已知密碼
known_passwords = [...]  # 從 Sheet PASSWD 讀取
passwords_to_try.extend(known_passwords)

for pwd in passwords_to_try:
    try:
        reader = PdfReader('/tmp/xxx_bill.pdf')
        result = reader.decrypt(pwd)
        if result > 0:
            print(f'✅ SUCCESS with password: {pwd}')
            text = reader.pages[0].extract_text()
            break
    except Exception as e:
        print(f'❌ Failed: {pwd}')
```

**注意：**
- 必須使用 `python3` + `pypdf` 模組
- 先用 `decrypt()` 嘗試解密，再讀取 `pages`
- 如果解密失敗（result = 0），嘗試下一個密碼

### 📊 Sheet 格式

**PASSWD 密碼頁籤（2欄）：**
| 欄位 | 說明 |
|------|------|
| 機構 | 例如：台灣自來水公司、台電、中華電信 |
| 密碼 | PDF 開啟密碼，若無密碼寫「無(PDF無密碼)」 |

**OVERVIEW 帳單頁籤（5欄）：**
| 欄位 | 說明 | 範例 |
|------|------|------|
| 日期 | 收到帳單的日期（YYYY-MM-DD）| 2026-03-02 |
| 機構 | 帳單機構名稱 | 台灣自來水公司 |
| 金額 | 帳單金額，若加密無法讀取寫「-1」 | 583 或 -1 |
| 幣別 | 貨幣類型（TWD/USD） | TWD, USD |
| 備註 | 重要資訊（帳號、扣款日等），加密無法讀取要加「（加密無法讀取）」 | 115/03/13 玉山銀行扣款 |

### 🔐 加密 PDF 處理
- 金額欄位：填入 `-1`
- 備註欄位：加上 `（加密無法讀取）`

### 🔗 Sheet ID
`1ZYKgmPZa1lVDhI6MH2D1QWMgaFTBdTqmSX3EAiYMF1Y`

**⏰ 頻率：** 每日早上、中午、下午

---

## 2️⃣ 每日晨報

> **規則描述：**
> - 檢查今日起三日內行事曆行程
> - 檢查主要行事曆（primary）
> - 回報給使用者

**⏰ 時間：** 每日早上

---

## 3️⃣ GitHub 連結

> **狀態：** ✅ 已設定 GH_TOKEN（在 Zeabur 環境變數）

| 項目 | 狀態 |
|------|------|
| GitHub 連線 | ✅ 正常 |
| 帳號 | shower220g |
| 專案 | webpage |

---

<div align="center">

*💾 最後更新於 2026-03-02*

</div>
