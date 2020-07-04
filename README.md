# YubiKey 5 Series 指南

網路上有許多 YubiKey 的英文指南，Mandarin 的則只有販賣商家的介紹，因此既然都花時間研究了就順便記錄下來。

錯誤/建議/修正請送 PR，謝謝。

YubiKey 有很多產品線，也有很多功能，本文主要是介紹 YubiKey 5 Series 支援的各種應用協定和介面。

本文絕大部分來自[這裡](https://support.yubico.com/support/solutions/articles/15000014219-yubikey-5-series-technical-manual#FIDO_U2Fkw7ef)，原文很豐富完整，有興趣也可以參閱。

## 目標讀者

YubiKey 支援的功能非常多，本文主要專注在入門的應用，包含：

- 設定二階段認證
- 想要從簡訊二階段認證遷移到 
  - Authenticator app 或者
  - 硬體 security key (YubiKey)
- 設定/啟用/停用 YubiKey 各種功能

另外還會提到一點點備份的事。

本文尚未包含進階應用：(maybe TODO)

- [ ] Smart Card
- [ ] OpenPGP

# 二階段驗證 / 2-Factor Authentication (2FA) / Multi-Factor Authentication (MFA)

指除了帳號密碼的驗證方式外，額外的驗證方式。常見的有（以安全性弱 -> 強排列）

- 簡訊收取一串數字，常見 4-6 位數字。安全性最弱，簡訊已被證實可以輕易攔截。
- Authenticator app 產生一串數字，常見為 6 位數字。例如 Google Authenticator / Lastpass Authenticator / Yubico Authenticator。由於是軟體產生，一般認為比硬體來的不安全。
- 硬體 security key 驗證。例如 YubiKey。有非常多種協定。

這裡有一個重要的概念，就是**安全性是定義在「最弱的那個連結」**：如果你在某服務有設定簡訊二階段，也有設定 YubiKey，那整體安全性是很弱的，因為攻擊的人只需要嘗試攔截你的簡訊，就可以成功登入系統。

# 應用協定 v.s. 介面

YubiKey 其中一個讓人混淆的，是有很多應用協定，也有很多的介面。

- 介面：USB-A，USB-C，Lightning，NFC。
- 應用協定：OTP，OATH-OTP，FIDO U2F，FIDO2，以及其他。

這兩者是正交的（orthogonal），也就是說以下都是合理的使用情境：

- 透過 USB-C 使用 FIDO U2F
- 透過 Lighting 使用 FIDO U2F
- 透過 Lighting 使用 OTP

但並不是所有的排列組合都是存在的，例如「OTP 應用」<sup>*1</sup> 就無法透過 NFC 使用。

*1 「OTP 應用」：詳細定義請見下文。

# 介面 / Interface

| 產品 \ 支援介面 | USB-A | USB-C | Lightning | NFC |
|:-------------:|:-----:|:-----:|:---------:|:---:|
| 5 NFC         | ✓     |       |           | ✓   |
| 5 Nano        | ✓     |       |           |     |
| 5C            |       | ✓     |           |     |
| 5Ci           |       | ✓     | ✓         |     |
| 5C Nano       |       | ✓     |           |     |

# 應用協定 / Application

YubiKey 上的應用協定大致有六種，分別是 OTP, FIDO U2F, FIDO2, OATH, Smart Card, OpenPGP。

## OTP / One-time-password 應用

（「OTP」在 YubiKey 文件中不斷出現，此處指的是非常特定的用法，即「當 YubiKey 以 USB-A/USB-C 連接到裝置，變成一份虛擬鍵盤，按下 YubiKey，它會輸出一串隨機碼」，以下稱「OTP 應用」。請小心區分其他處出現的「OTP」，嘗試以上下文了解其意義。）

### 使用情境

登入時，輸入帳號密碼後，應用程式或瀏覽器會再出現一組輸入框，要求使用者插入並按下 security key 後，產生一次性的密碼。

### 設定

OTP 應用有 slot 1 / slot 2 兩組設定，短按 YubiKey 會輸出 slot 1，長按則輸出 slot 2。出廠時，slot 1 是被設定好的（詳細如下），slot 2 則是空的。

每一個 slot 都可以設定怎麼產生隨機碼，有以下的幾種規則

- Yubico OTP：slot 1 出廠預設，常用在二階段認證。長度為 44 個字母，前 12 個字母代表該 YubiKey，所以是固定的；後 32 個字母則是每次都不同。
- OATH-HOTP：常用在二階段認證。以 RFC 4226 定義的的演算法，產生的 6 位數字。OATH-HOTP 晚點會再出現，這裡看不懂請再往下看。
- Static Password：就是一組密碼，基本上就是把密碼存在硬體 security key 裡面。因為是固定的，所以不會用在二階段驗證。
  - 此設定就是一般的密碼輸入，非二階段認證。
  
設定 OTP 應用的各個 slot 需透過 [YubiKey Manager](https://support.yubico.com/support/solutions/articles/15000014219-yubikey-5-series-technical-manual#YubiKey_Manager38ncjm)，各大桌面平台都有 GUI app，有些也有 CLI。

### 限制

只有兩個 slots。如果是工作使用可能還行，例如就只用來登入 AWS；個人使用的話，OATH（詳見下文）比較夠用。

## FIDO U2F

### 使用情境

登入時，輸入帳號密碼後，應用程式或瀏覽器會提示使用者插入並按下 security key。與 OTP 應用的差別在 YubiKey 此時並非一個虛擬鍵盤，因此不會有輸入框。

TODO: 圖

### 設定

在要使用的網站要先註冊 YubiKey，通常在 Security > 2FA > security key 設定，通常可以設定多組。常見網路服務都有支援，例如 Google，Facebook，Twitter，Dropbox，GitHub。

## FIDO2

柏拉圖式的登入，會儲存登入所需的所有資訊在 YubiKey 上，所以可以實現真正的無密碼驗證。目前有支援的服務極少，Microsoft Azure 似乎有支援。支援的服務少到連 [YubiKey 的 catalog](https://www.yubico.com/works-with-yubikey/catalog/) 上似乎都是錯的，例如 Google Account 有出現在名單上，但我遍尋不著怎麼啟用。

## OATH HOTP/TOTP

簡單理解的話，
- Time-based OTP / TOTP：每三十秒，就會產生一組不同的 6 位數字。
- HMAC-based OTP / HOTP：每次產生，都會產生一組不同的 6 位數字。

這裡的 HOTP 跟「OTP 應用」的 OATH-HOTP 原理是一樣的，每次產生都會不同。

特別重要的是，**OATH 只能透過 CCID channel 讀取，目前就是用 Yubico Authenticator 來讀。這個軟體在主要平台和手機上都有。**

### 使用情境

登入時，輸入帳號密碼後，應用程式或瀏覽器會再出現一個輸入框，要求使用者輸入一組 6 位數字。使用者使用 Yubico Authenticator 讀取 YubiKey 後，在欲登入網站填入 Yubico Authenticator 顯示的 5 位數字以登入。

有趣的是，在這過程中，Yubico Authenticator 只負責提供時間給 YubiKey (TOTP)，接著顯示 YubiKey 回傳的值，Yubico Authenticator 本身不負責運算/儲存。所以如果手機掉了，別的手機還是可以讀 YubiKey。

### 設定

在要使用的網站，Security > 2FA > Authenticator app，通常會出現一個 QR code。接著打開 Yubico Authenticator，選擇新增，連結 YubiKey 後，用相機掃 QR code。如果不是使用手機的話，也可以手動輸入一組 code 到 Yubico Authenticator 中。

這個過程是透過 Yubico Authenticator 寫入 YubiKey 的 OATH slot，Yubico Authenticator 本身並不儲存資訊。

### 限制

OATH 有 25 個 slots，至多可以存 25 個二階段驗證，在越來越多網站支援二階段驗證的時代，25 組通常不是很都用。

# YubiKey Manager

[下載連結](https://support.yubico.com/support/solutions/articles/15000014219-yubikey-5-series-technical-manual#YubiKey_Manager38ncjm)

這個軟體可以設定 YubiKey

- 停用特定介面的特定應用，例如我個人把 OTP 應用在所有介面都停用，避免誤觸而產生一堆無用字串。
- 啟用/停用 OTP 應用各個 slot。
- 設定 OTP 應用內的 OATH-MOTP 或者 static password 的內容。

有些進階功能在 GUI 上面無法設定，可以使用 `ykman` 這個 CLI。

# 備份

一個常見問題就是要怎麽備份 YubiKey：**YubiKey 沒辦法備份。** 請把它當作車鑰匙/家門鑰匙來對待，所以你怎麼備份車鑰匙，就怎麼備份 YubiKey。

我目前的方式就是買 2 把 YubiKey，所有二階段驗證的網站都要註冊 2 把，無論是 FIDO U2F 或者是 OATH。

## FIDO U2F

取決於該網站是否支援多組 security keys。Google, Facebook, Dropbox, GitHub 都有支援；目前唯一發現只支援一把 security key 的網站是 Twitter。

## OATH

OATH（也就是掃 QR code 來設定）則可以輕鬆設定多組：

1. 當網站提示掃描 QR code 時
2. 打開 Yubico Authenticator
3. 刷 YubiKey
4. 掃 QR code
5. 換一個 YubiKey 重複步驟 2-4
6. 回到網站輸入一次 6 位數字完成設定。


# 參考資料

- [YubiKey 5 Series Technical Manual](https://support.yubico.com/support/solutions/articles/15000014219-yubikey-5-series-technical-manual)
- [Reddit r/YubiKey 置頂 FAQ](https://www.reddit.com/r/yubikey/comments/9kodiy/meta_ryubikey_frequently_asked_questions_please/)
