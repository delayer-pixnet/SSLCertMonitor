# SSL 憑證監控工具 (SSLCertMonitor)

> 一個以 C# .NET 8 / Windows Forms 開發的 SSL 憑證監控與自動警報工具。

---

## 功能特色

| 功能 | 說明 |
|------|------|
| 🖥️ GUI 介面 | 深色主題 WinForms，支援 DataGridView 管理多筆監控目標 |
| 🔍 憑證解析 | 透過 SslStream 取得 CN、SAN、O、L、S、C、I、有效期限、剩餘天數 |
| ⚡ 批次檢測 | 非同步並行檢測所有啟用目標，並以顏色區分狀態 |
| 📮 雙郵件引擎 | 支援 System.Net.Mail (內建) 與 MailKit (現代高效能) 彈性切換 |
| 📧 HTML 警報郵件 | 自動發送精美 HTML 格式警報，含色彩標籤與摘要表格 |
| ⚙️ 設定管理 | SMTP 完整參數 + 警報門檻天數 + 引擎選擇，以 JSON 持久化 |
| 📄 CSV 匯出 | 一鍵匯出檢測結果為 UTF-8 BOM CSV |
| 🤫 安靜模式 | `--silent` / `--check` 參數不開視窗，適合排程器呼叫 |

---

## 環境需求

- Windows 10 / 11
- [.NET 8 Runtime (Windows)](https://dotnet.microsoft.com/download/dotnet/8.0)

---

## 建置與執行

```powershell
# 建置（Release）
dotnet build --configuration Release

# GUI 模式
.\bin\Release\net8.0-windows\SSLCertMonitor.exe

# 靜默模式（排程器用）
.\bin\Release\net8.0-windows\SSLCertMonitor.exe --check
# 或
.\bin\Release\net8.0-windows\SSLCertMonitor.exe --silent
```

---

## 設定說明（config.json）

`config.json` 位於執行檔同一目錄，首次執行由程式自動建立。

```json
{
  "alertThresholdDays": 30,
  "emailProvider": "SystemNetMail",
  "smtp": {
    "host": "smtp.example.com",
    "port": 587,
    "enableSsl": true,
    "account": "your@email.com",
    "password": "your-password",
    "sender": "SSL Monitor <your@email.com>",
    "receiver": "admin@company.com"
  },
  "targets": [
    {
      "enabled": true,
      "host": "www.google.com",
      "port": 443,
      "remark": "Google 主站"
    }
  ]
}
```

> ⚠️ **安全提示**：`config.json` 包含 SMTP 密碼，請勿加入版本庫（已加入 .gitignore）。

---

## Windows 工作排程器設定

1. 開啟「工作排程器」→「建立基本工作」
2. 觸發程序：每天 08:00
3. 動作：啟動程式 → 執行 `run_silent_check.bat`
4. 一般 → 勾選「不管使用者是否登入都要執行」

---

## 靜默模式日誌

每次靜默執行會在執行檔同一目錄產生 `silent_run.log`，格式如下：

```
[2026-01-15 08:00:01] === SSL 憑證靜默檢測開始 ===
[2026-01-15 08:00:01] 已載入設定，共 3 筆目標，警報門檻：30 天
[2026-01-15 08:00:03]   [⚠ 警報] example.com:443 | CN=example.com | 到期：2026-02-01 | 剩餘：17 天
[2026-01-15 08:00:03] 共 1 筆需要警報，嘗試發送郵件...
[2026-01-15 08:00:04] 警報郵件已成功發送至：admin@company.com
[2026-01-15 08:00:04] === 靜默檢測結束 ===
```
