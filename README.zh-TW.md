# WapForm for Windows — 範例

[English](README.md) · 中文

**WapForm for Windows** 的 WML 範例程式：用 **WapForm Runtime** 或 **WapForm Toolkit**（Microsoft Store）
開啟 `.wml` 檔，就會以 Windows 桌面應用程式執行——表單、資料表格、主從表、報表——並連接你自己的資料庫。

之後會陸續加入更多範例，每個範例放在自己的資料夾。

> 文中提到的帳號與密碼都是**模擬值**（例如 `xyz` / `123`），只供本機測試，正式上線前請換掉。

## 目錄

- [架構](#架構)
- [範例](#範例)
- [需求](#需求)
- [安裝](#安裝)
- [wapform.ini 參數說明](#wapformini-參數說明)
- [多國語言介面：language.txt](#多國語言介面languagetxt)
- [執行範例](#執行範例)
- [授權與試用](#授權與試用)
- [上線前檢查](#上線前檢查)

## 架構

```
  ┌────────────────────┐    開發      ┌──────────────────────┐
  │ WapForm Toolkit    │─────────────▶│  *.wml（本 repo）    │
  │ （開發工具）        │              │  純文字 XML：        │
  └────────────────────┘              │  資料、表單、        │
                                      │  報表、邏輯          │
  ┌────────────────────┐    執行      └──────────┬───────────┘
  │ WapForm Runtime    │◀────────────────────────┘
  │ （使用者電腦）      │
  └─────────┬──────────┘
            │ wapform.ini（放在 .wml 旁）：資料庫、字型、語言
            ▼
     ┌──────────────┐
     │ MySQL ／     │   （專業版另支援 SQL Server）
     │ MariaDB      │
     └──────────────┘
```

- **一個 `.wml` 檔就是一支程式。** 它宣告資料集（`<dbquery>`、`<dbtable>`）、畫面（`<datasource>`、
  `<dbgrid>`、`<navigator/>`）、報表（`<report>`、`<group>`）與邏輯（`<function>`、`<onevent>`、`<if>`）。
  不需要編譯：改完檔案重新開啟即可。
- **Toolkit 開發，Runtime 執行。** 開發者用 WapForm Toolkit 撰寫與測試 WML；使用者用 WapForm Runtime
  執行完成的系統，畫面上只有業務功能。
- **`wapform.ini` 連接你的資料。** 程式讀取 `.wml` 旁的 `wapform.ini`，取得資料庫連線、字型與介面語言。
- **同一份 WML 還能走更遠。** WapForm Toolkit（專業版）可以把同一批 `.wml` 匯出成 Flutter App，
  WapForm for Web 則把 WML 當網頁執行——見 [wapform_web](https://github.com/wapform/wapform_web)，
  這批銷售程式在那裡同時也是 Flutter 後台。

## 範例

### `sales/` — 銷售管理後台

以範例資料庫 `sales`（[wapform_web](https://github.com/wapform/wapform_web/tree/main/database) 裡的那一個）
為基礎的小型銷售系統。

| 檔案 | 畫面 |
|---|---|
| `wapform.wml` | 主選單（MDI 視窗）。依 `users` 驗證登入，再依 `mnu`／`login` 產生選單（每位使用者的權限）。 |
| `app001.wml` | 系統參數（公司資料、稅率、會計期間） |
| `app002.wml` | 商品資料 |
| `app003.wml` | 品牌資料 |
| `app004.wml` | 客戶資料（含列印清單） |
| `app005.wml` | 員工資料 |
| `app006.wml` | 出貨單（表頭＋明細，主從表，合計） |
| `app007.wml` | 客戶名冊（報表） |
| `app012.wml` | 應收帳款對帳單（報表） |
| `app023.wml` | 收款單 |
| `app037.wml` | 貨運公司 |
| `app901.wml` | 使用者帳號（密碼、部門、Email） |
| `app902.wml` | 選單項目與每位使用者的讀寫權限（`mnu`、`login`） |
| `function.wml` | 共用函式（列印報表） |

## 需求

| 項目 | 用途 |
|---|---|
| Windows 10／11 | WapForm Toolkit／Runtime |
| [WapForm Toolkit](https://apps.microsoft.com/detail/9PMRZVM58RM4) 或 [WapForm Runtime](https://apps.microsoft.com/detail/9N1DKMZR2PF4)（Microsoft Store） | 執行範例；Toolkit 也能編輯範例 |
| MySQL 或 MariaDB（例如 XAMPP） | 範例資料庫 |

## 安裝

1. 從 Microsoft Store 安裝 **WapForm Runtime**（執行用）或 **WapForm Toolkit**（執行＋開發）。
2. 安裝 MySQL 或 MariaDB，用
   [`sales.sql`](https://github.com/wapform/wapform_web/blob/main/database/sales.sql) 建立範例資料庫：
   ```
   mysql -u root -p < sales.sql
   ```
   會建立含虛構資料的 `sales` 資料庫與 MySQL 帳號 `xyz`／`123`（模擬值）。若已有同名資料庫請先備份。
3. Clone 或下載本 repo，例如放到 `C:\wapform_windows`。
4. 在範例資料夾建立 `wapform.ini`（`sales\wapform.ini`），內容見下方。

## wapform.ini 參數說明

WapForm Toolkit／Runtime 讀取與所開啟 `.wml` 同一資料夾的 `wapform.ini`。
格式：每行一個 `名稱=值`；名稱不分大小寫；`;` 開頭為註解；請存成 **UTF-8**。只有資料庫那幾行是必要的。

範例資料庫用的最小 `sales\wapform.ini`：

```ini
Driver=mysql
Server=localhost
Database=sales
Username=xyz
Password=123
unicode=yes
language=tw
```

| 參數 | 範例 | 說明 |
|---|---|---|
| `driver` | `mysql` | **必要。** 資料庫類型：`mysql`（MySQL／MariaDB）；專業版另支援 `mssql`（SQL Server）。 |
| `server` | `localhost` | **必要。** 資料庫主機名稱或 IP；非預設連接埠用 `主機:埠`。 |
| `database` | `sales` | **必要。** 資料庫名稱。 |
| `username` | `xyz` | **必要。** 資料庫帳號——請用只能存取此資料庫的帳號，不要用 `root`。 |
| `password` | `123` | **必要。** 資料庫密碼（模擬值，正式安裝一定要改）。 |
| `dateformat` | `yyyy/mm/dd` | 選用（預設：Windows 地區設定）。畫面與報表的日期格式。 |
| `fontname` | `MingLiU` | 選用（預設：系統字型）。表單與報表的預設字型。版面以字元格計算，請用等寬字型。細明體 MingLiU 內建於 Windows，涵蓋英、繁中、日文；純英文也可用 Consolas。 |
| `fontsize` | `10` | 選用（預設 10）。預設字型大小（點）。 |
| `charwidth` | `7` | 選用（預設 7）。一個字元格的寬度（像素），用來換算 WML 中以字數表示的寬度。 |
| `charheight` | `13` | 選用（預設 13）。一個字元格的高度（像素），用於列高與版面。 |
| `unicode` | `yes` | 像範例這樣的 utf8mb4 資料庫請保留：**有這一行**就啟用 Unicode（UTF-8／utf8mb4），值不檢查。 |
| `language` | `tw` | 選用（預設 `en`）。介面語言：`en`、`tw`（繁體中文）、`jp`（日文）。 |

> **安全提醒**：正式環境的 `wapform.ini` 含有資料庫密碼，不要放進公開 repo 或共用資料夾。
> 本 repo 不含 `wapform.ini`，請自行建立。

## 多國語言介面：language.txt

`language.txt` 放在 `wapform.ini` 旁（UTF-8）。每行一筆：英文為鍵，後面接繁體中文與日文翻譯，以逗號分隔：

```
; English=Traditional Chinese,Japanese
Item No=料號,品番
Search=查詢,検索
```

WML 中的標籤與標題用英文寫；設定 `language=tw` 或 `jp` 時就顯示翻譯，缺少的翻譯會顯示英文。
在 WapForm Toolkit 的 *Languages › Interface Languages…* 可以設定 `language=` 並編輯 `language.txt`。

## 執行範例

1. 啟動 WapForm Toolkit 或 Runtime。
2. 開啟 `sales\wapform.wml`（主選單），以 `admin`／`123`（全部功能）或 `demo`／`123`（只有銷售選單）登入；
   也可以直接開單一畫面，例如 `sales\app002.wml`（商品）或 `sales\app006.wml`（出貨單）。

## 授權與試用

WapForm Toolkit 與 Runtime 第一次使用時開始 30 天全功能試用，之後轉為免費版（只有表單功能）。
方案：[wapform.com/pricing](https://wapform.com/pricing)。序號會寄到你的 Email，在程式的授權視窗輸入。

## 上線前檢查

- 換掉模擬的 MySQL 帳號與範例登入帳號（`admin`／`123`、`demo`／`123`）。
- 刪除或修改範例資料。

---

文件與支援：[wapform.com](https://wapform.com) ·
[github.com/wapform](https://github.com/wapform) · 技術手冊：
[wapform_manual_tw.md](https://wapform.com/wapform_manual_tw.md)
