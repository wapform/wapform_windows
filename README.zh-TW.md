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
- [執行範例](#執行範例)
- [圖片上傳使用 WapForm for Web](#圖片上傳使用-wapform-for-web)
- [用 HTTP 伺服器部署程式（WapForm Runtime）](#用-http-伺服器部署程式wapform-runtime)
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
| [WapForm for Web](https://wapform.com/web)（`wapsetup.exe`，內含免費的 WapForm Web Server） | `app002.wml` 的圖片上傳與商品圖片——建議安裝 |
| 一般 HTTP 伺服器（IIS 靜態站台、nginx、`python -m http.server`…） | 透過網路把程式部署給 WapForm Runtime——選用 |

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

## 執行範例

1. 啟動 WapForm Toolkit 或 Runtime。
2. 開啟 `sales\wapform.wml`（主選單），以 `admin`／`123`（全部功能）或 `demo`／`123`（只有銷售選單）登入；
   也可以直接開單一畫面，例如 `sales\app002.wml`（商品）或 `sales\app006.wml`（出貨單）。

## 圖片上傳使用 WapForm for Web

商品資料 `app002.wml` 用 `<webcopy protocol="httpupload">` 把商品圖片上傳到 WapForm for Web 範例商店的
`shop/upload.wml`，並從 `shop/upload/` 讀取顯示：

```
上傳   http://localhost:8080/shop/upload.wml
顯示   http://localhost:8080/shop/upload/<檔名>   （預設圖：/shop/300x300.jpg）
```

所以要使用這個功能，建議下載安裝 **WapForm for Web**（[wapform.com/web](https://wapform.com/web)）：
它內含免費的 WapForm Web Server（`localhost:8080`）與附 `upload.wml` 的範例商店。使用 IIS 時，請把
`app002.wml` 裡的網址改成 `http://localhost/shop/…`。範例的其他功能不需要它。

## 用 HTTP 伺服器部署程式（WapForm Runtime）

WapForm Runtime 可以從伺服器執行程式，而不是本機檔案。登入時在 **Server** 欄輸入網址
（以 `/` 結尾的資料夾網址，例如 `http://192.168.1.10/sales/`），Runtime 就會從那個資料夾下載：

| 檔案 | 用途 |
|---|---|
| `wapform.ini` | 資料庫連線與設定 |
| `wapform.wml`、`app*.wml`… | 程式本身 |
| `wapform.inf` | 選用：Runtime 本身的自動更新 |

網址會記住，下次登入自動帶出。程式只要在伺服器上更新一次，每台電腦下次開啟時就是新版本。

**請用一般的靜態 HTTP 伺服器——不能用 WapForm for Web。** WapForm for Web 會*執行* `*.wml`，回傳執行後的
HTML；但 Runtime 需要的是 `.wml` 原始檔。請用任何「原樣回傳檔案」的伺服器提供這個資料夾，例如：

- IIS 站台或虛擬目錄，**不要**設定 `*.wml` → WapServ.dll 的對應，並為 `.wml` 與 `.ini` 加上 MIME 類型
  （`text/plain`）；
- nginx 或 Apache；
- 快速測試：在該資料夾執行 `python -m http.server 8000`（網址 `http://<電腦>:8000/`）。

> **安全提醒**：這種方式下，`wapform.ini`——包括資料庫密碼——會被每一台用戶端下載。只能在可信任的內部網路使用，
> 資料庫帳號只給程式需要的權限，絕不可讓這個資料夾對外公開。IIS 預設封鎖 `.ini` 正是這個原因，只在這個內部站台開放。

## 授權與試用

WapForm Toolkit 與 Runtime 第一次使用時開始 30 天全功能試用，之後轉為免費版（只有表單功能）。
方案：[wapform.com/pricing](https://wapform.com/pricing)。序號會寄到你的 Email，在程式的授權視窗輸入。

## 上線前檢查

- 換掉模擬的 MySQL 帳號與範例登入帳號（`admin`／`123`、`demo`／`123`）。
- 刪除或修改範例資料。

## 授權

本 repo 的範例檔案以 [MIT 授權](LICENSE) 釋出：可以自由複製、修改並用在你自己的專案，包括商業用途。
WapForm 產品本身（WapForm Toolkit、WapForm Runtime、WapForm for Web）是商業軟體，另行授權——
見 [wapform.com/tw/terms](https://wapform.com/tw/terms)。

---

文件與支援：[wapform.com](https://wapform.com) ·
[github.com/wapform](https://github.com/wapform) · 技術手冊：
[wapform_manual_tw.md](https://wapform.com/wapform_manual_tw.md)
