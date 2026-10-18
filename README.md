# WapForm for Windows — samples

English · [中文](README.zh-TW.md)

WML sample applications for **WapForm for Windows**: open a `.wml` file with
**WapForm Runtime** or **WapForm Toolkit** (Microsoft Store) and it runs as a Windows desktop
application — forms, data grids, master-detail, reports — connected to your own database.

More samples will be added over time; each sample lives in its own folder.

> All accounts and passwords mentioned here are **simulated** (for example `xyz` / `123`) and
> meant for local testing only. Replace them before going live.

## Contents

- [Architecture](#architecture)
- [Samples](#samples)
- [Requirements](#requirements)
- [Installation](#installation)
- [wapform.ini](#wapformini)
- [Running a sample](#running-a-sample)
- [Image upload uses WapForm for Web](#image-upload-uses-wapform-for-web)
- [Deploying programs with an HTTP server (WapForm Runtime)](#deploying-programs-with-an-http-server-wapform-runtime)
- [License and trial](#license-and-trial)
- [Before going live](#before-going-live)

## Architecture

```
  ┌────────────────────┐   develop    ┌──────────────────────┐
  │ WapForm Toolkit    │─────────────▶│  *.wml  (this repo)  │
  │ (development tool) │              │  plain-text XML:     │
  └────────────────────┘              │  data, forms,        │
                                      │  reports, logic      │
  ┌────────────────────┐   run        └──────────┬───────────┘
  │ WapForm Runtime    │◀────────────────────────┘
  │ (end users' PCs)   │
  └─────────┬──────────┘
            │ wapform.ini (next to the .wml): database, font, language
            ▼
     ┌──────────────┐
     │ MySQL /      │   (Professional: also SQL Server)
     │ MariaDB      │
     └──────────────┘
```

- **One `.wml` file is the application.** It declares the datasets (`<dbquery>`, `<dbtable>`),
  the screens (`<datasource>`, `<dbgrid>`, `<navigator/>`), the reports (`<report>`, `<group>`)
  and the logic (`<function>`, `<onevent>`, `<if>`). There is nothing to compile: edit the file and
  open it again.
- **Toolkit builds, Runtime runs.** Developers write and test WML with WapForm Toolkit; end users
  run the finished system with WapForm Runtime, which shows only the business screens.
- **`wapform.ini` connects it to your data.** The program reads the `wapform.ini` next to the
  `.wml` files for the database connection, fonts and interface language.
- **The same WML goes further.** WapForm Toolkit (Professional) exports the same `.wml` pages as a
  Flutter app, and WapForm for Web serves WML as web pages — see
  [wapform_web](https://github.com/wapform/wapform_web), where these sales pages are also the
  Flutter back office.

## Samples

### `sales/` — sales management back office

A small sales system on the sample database `sales` (the one in
[wapform_web](https://github.com/wapform/wapform_web/tree/main/database)).

| File | Screen |
|---|---|
| `wapform.wml` | Main menu (MDI window). Checks the sign-in against `users` and builds the menu from `mnu` / `login` (permissions per user). |
| `app001.wml` | System parameters (company profile, tax rate, period) |
| `app002.wml` | Product master |
| `app003.wml` | Brand master |
| `app004.wml` | Customer master (with a printable list) |
| `app005.wml` | Employee master |
| `app006.wml` | Shipment orders (header + lines, master-detail, totals) |
| `app007.wml` | Customer list (report) |
| `app012.wml` | Accounts receivable statement (report) |
| `app023.wml` | Payment receipts |
| `app037.wml` | Shippers |
| `app901.wml` | User accounts (password, department, e-mail) |
| `app902.wml` | Menu items and read / write permissions per user (`mnu`, `login`) |
| `function.wml` | Shared functions (print report) |

## Requirements

| Item | Used for |
|---|---|
| Windows 10 / 11 | WapForm Toolkit / Runtime |
| [WapForm Toolkit](https://apps.microsoft.com/detail/9PMRZVM58RM4) or [WapForm Runtime](https://apps.microsoft.com/detail/9N1DKMZR2PF4) (Microsoft Store) | Running the samples; Toolkit also for editing them |
| MySQL or MariaDB (for example XAMPP) | The sample database |
| [WapForm for Web](https://wapform.com/web) (`wapsetup.exe`, includes the free WapForm Web Server) | Image upload and product pictures in `app002.wml` — recommended |
| A plain HTTP server (IIS static site, nginx, `python -m http.server` …) | Deploying the programs to WapForm Runtime over the network — optional |

## Installation

1. Install **WapForm Runtime** (to run) or **WapForm Toolkit** (to run and develop) from the
   Microsoft Store.
2. Install MySQL or MariaDB and create the sample database with
   [`sales.sql`](https://github.com/wapform/wapform_web/blob/main/database/sales.sql):
   ```
   mysql -u root -p < sales.sql
   ```
   It creates the database `sales` with fictitious data and the MySQL account `xyz` / `123`
   (simulated values). If a database named `sales` already exists, back it up first.
3. Clone or download this repository, for example to `C:\wapform_windows`.
4. Create `wapform.ini` in the sample folder (`sales\wapform.ini`) — see below.

## wapform.ini

WapForm Toolkit / Runtime reads the `wapform.ini` in the same folder as the `.wml` being opened.
Format: `name=value`, one per line; names are case-insensitive; lines starting with `;` are
comments; save as **UTF-8**. Only the database lines are required.

Minimal `sales\wapform.ini` for the sample database:

```ini
Driver=mysql
Server=localhost
Database=sales
Username=xyz
Password=123
unicode=yes
language=en
```

| Key | Sample | Meaning |
|---|---|---|
| `driver` | `mysql` | **Required.** Database type: `mysql` (MySQL / MariaDB); the Professional edition also supports `mssql` (SQL Server). |
| `server` | `localhost` | **Required.** Database host name or IP; use `host:port` for a non-default port. |
| `database` | `sales` | **Required.** Database name. |
| `username` | `xyz` | **Required.** Database account — use one limited to this database, not `root`. |
| `password` | `123` | **Required.** Database password (simulated — always change it for a real installation). |
| `dateformat` | `yyyy/mm/dd` | Optional (default: Windows regional settings). Date format on screens and reports. |
| `fontname` | `MingLiU` | Optional (default: system font). Default font of forms and reports. Use a monospaced font, because layouts are measured in character cells. MingLiU is built into Windows and covers English, Traditional Chinese and Japanese; for English only, Consolas also works. |
| `fontsize` | `10` | Optional (default 10). Default font size in points. |
| `charwidth` | `7` | Optional (default 7). Width of one character cell in pixels, used to convert WML widths given in characters. |
| `charheight` | `13` | Optional (default 13). Height of one character cell in pixels, used for row heights and layout. |
| `unicode` | `yes` | Keep it for utf8mb4 databases such as the sample: the presence of the line switches Unicode (UTF-8 / utf8mb4) on — the value is not checked. |
| `language` | `en` | Optional (default `en`). Interface language: `en`, `tw` (Traditional Chinese) or `jp` (Japanese). |

> **Security.** A real `wapform.ini` holds the database password: keep it out of public
> repositories and shared folders. This repository does not contain one — create your own.

## Running a sample

1. Start WapForm Toolkit or Runtime.
2. Open `sales\wapform.wml` (the main menu) and sign in with `admin` / `123` (all functions) or
   `demo` / `123` (sales menu only); or open a single screen such as `sales\app002.wml` (products)
   or `sales\app006.wml` (shipment orders).

## Image upload uses WapForm for Web

The product master `app002.wml` uploads product pictures with
`<webcopy protocol="httpupload">` to the page `shop/upload.wml` of the WapForm for Web sample
store, and shows them from `shop/upload/`:

```
upload   http://localhost:8080/shop/upload.wml
show     http://localhost:8080/shop/upload/<file>   (default picture: /shop/300x300.jpg)
```

So for this function, download and install **WapForm for Web**
([wapform.com/web](https://wapform.com/web)): it includes the free WapForm Web Server
(`localhost:8080`) and the sample store with `upload.wml`. Under IIS change the address to
`http://localhost/shop/…` in `app002.wml`. Everything else in the sample works without it.

## Deploying programs with an HTTP server (WapForm Runtime)

WapForm Runtime can run the programs from a server instead of local files. At sign-in, enter the
**Server** address (a folder URL ending with `/`, for example `http://192.168.1.10/sales/`).
Runtime then downloads from that folder:

| File | Purpose |
|---|---|
| `wapform.ini` | Database connection and settings |
| `wapform.wml`, `app*.wml`, … | The programs |
| `wapform.inf` | Optional: automatic update of the Runtime itself |

The address is remembered for the next sign-in. Update a program once on the server and every PC
gets the new version the next time it opens it.

**Use a plain static HTTP server — not WapForm for Web.** WapForm for Web *executes* `*.wml`
pages and returns the resulting HTML, but Runtime needs the `.wml` source itself. Serve the
folder with any server that returns files as they are, for example:

- an IIS site or virtual directory **without** the `*.wml` → WapServ.dll mapping, with MIME types
  added for `.wml` and `.ini` (`text/plain`);
- nginx or Apache;
- for a quick test: `python -m http.server 8000` in the folder (address
  `http://<pc>:8000/`).

> **Security.** In this mode `wapform.ini` — including the database password — is downloaded by
> every client. Use it only on a trusted internal network, give the database account only the
> rights the programs need, and never expose this folder to the internet. IIS blocks `.ini` files
> by default precisely for this reason; allow it only on this internal site.

## License and trial

WapForm Toolkit and Runtime start a 30-day trial with all features on first use, then continue
as the Free edition (forms only). Plans: [wapform.com/pricing](https://wapform.com/pricing). The
license key is emailed to you; enter it in the program's License window.

## Before going live

- Replace the simulated MySQL account and the sample sign-ins (`admin` / `123`, `demo` / `123`).
- Delete or edit the sample data.

---

Documentation and support: [wapform.com](https://wapform.com) ·
[github.com/wapform](https://github.com/wapform) · Technical manual:
[wapform_manual_en.md](https://wapform.com/wapform_manual_en.md)
