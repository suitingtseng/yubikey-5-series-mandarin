# YubiKey 5 Series 指南

網路上有很多英文的指南，Mandarin 的則只有商家的介紹，既然都花時間研究了就順便記錄下來。

YubiKey 有很多產品線，也有很多功能，本文主要是介紹 YubiKey 5 Series 支援的各種應用。

本文絕大部分來自[這裡](https://support.yubico.com/support/solutions/articles/15000014219-yubikey-5-series-technical-manual#FIDO_U2Fkw7ef)，原文寫得很好，有興趣也可以參閱。

## 目標讀者

YubiKey 支援的功能非常多，本文主要專注在入門的應用，包含：

- 想要設定二階段認證
- 或者想要從簡訊二階段認證遷移到 
  - Authenticator app 或者
  - Security key

本文尚未包含進階應用：

- Smart Card
- OpenPGP

# 二階段驗證 / 2-Factor Authentication (2FA) / Multi-Factor Authentication (MFA)

指除了帳號密碼的驗證方式外，額外的認證方式。常見的有

- 簡訊收取一串數字，常見 4-6 碼。
- Authenticator app 產生一串數字，常見為 6 碼。例如 Google Authenticator / Lastpass Authenticator / Yubico Authenticator。
- 硬體 security key 驗證。例如 YubiKey。有非常多種協定。

詳情可以 Google。

# 應用協定 v.s. 介面

YubiKey 其中一個讓人混淆的就是有很多不同的應用協定，也有很多的介面。

- 介面：USB-A，USB-C，Lightning，NFC
- 應用協定：OTP，OATH-OTP，FIDO U2F，FIDO2

這兩者是正交的（orthogonal），也就是說以下都是合理的使用情境

- 透過 USB-C 使用 FIDO U2F
- 透過 Lighting 使用 FIDO U2F
- 透過 Lighting 使用 OTP

但並不是所有的排列組合都是存在的，例如「OTP 應用」<sup>*1</sup>就無法透過 NFC 使用。

*1 「OTP 應用」：詳細定義請見下文。


# 應用協定 / Application

Yubikey 上的應用協定大致有六種，分別是 OTP, FIDO/U2F, FIDO2, OATH, Smart Card, OpenPGP，但其實有些功能性有重疊。

## OTP / One-time-password 應用

（「OTP」在 YubiKey 文件中不斷出現，此處指的是非常特定的用法，即「當 YubiKey 以 USB-A/USB-C 連接到裝置，變成一份虛擬鍵盤，按下 YubiKey，它會輸出一串隨機碼」，以下稱「OTP 應用」。請小心區分其他處出現的「OTP」，嘗試以上下文了解其意義。）

### 使用情境

登入時，輸入完帳號密碼後，會在出入一組輸入框，要求使用者插入並按下 security key 後，產生一次性的密碼。

### 設定

OTP 應用有 slot 1 / slot 2 兩組設定，短按 YubiKey 會輸出 slot 1，長按則啊輸出 slot 2。出廠時，slot 1 是被設定好的（詳細如下），slot 2 則是空的。

每一個 slot 都可以設定怎麼產生隨機碼，有以下的幾種規則

- Yubico OTP：slot 1 出廠預設，常用在二階段認證。長度為 44 個字母，前 12 個字母代表該 YubiKey，所以是固定的；後 32 個字母則是每次都不同。
- OATH-HOTP：常用在二階段認證。以 RFC 4226 定義的的演算法，產生的 6 碼數字。OATH-HOTP 晚點會再出現，這裡看不懂請再往下看。
- Static Password：就是一組密碼，基本上就是把密碼存在硬體 security key 裡面。因為是固定的，所以不會用在二階段驗證。
  - 這個的使用情境就是一般的密碼輸入，非二階段認證。

### 限制

只有兩個 slots。

## FIDO U2F

### 使用情境

登入時，使用完帳號密碼後，應用程式或瀏覽器會提示使用者插入並按下 security key。與 OTP 應用的差別在 YubiKey 此時並非一個虛擬鍵盤，因此不會有輸入框。

TODO: 圖

### 設定

在要使用的網站要先註冊 YubiKey，通常在 Security > 2FA > security key 地下設定，通常可以設定多組。常見網路服務都有支援，例如 Google，Facebook，Twitter，Dropbox，GitHub。

TODO: 圖

## FIDO2

柏拉圖式的登入，會儲存登入所需的所有資訊在 YubiKey 上，所以可以實現真正的無密碼驗證。目前有支援的服務及少，Microsoft Azure 似乎有支援。支援的服務少到連 [YubiKey 的 catalog](https://www.yubico.com/works-with-yubikey/catalog/) 上似乎都是錯的，例如 Google Account 有出現在名單上，但我遍尋不著怎麼啟用。

## OATH-HOTP

HOTP 是指 HMAC-based OTP， 與之相對的是 Time-based OTP。

簡單理解的話，
- Time-based OTP：每三十秒，就會產生一個不同的 6 碼數字。
- HMAC-based OTP：每次產生（例如按下 YubiKey），都會產生一組不同的 6 碼數字。

TODO

### 使用情境

登入時，輸入完帳號密碼後，會在出入一組輸入框，要求使用者插入並按下 security key 後，產生一次性的密碼。這組碼通常為 6 個數字。







| 應用協定 \ 介面 | USB-A | NFC |
|:-------------:|:-----:|:---:|
| OTP           | Y     | N   |
| FIDO/U2F      | Y     | Y   |
| FIDO2         | Y     | Y   |
| OATH          | N     | Y   |
| Smart Card    | ?     | ?   |
| OpenPGP       | ?     | ?   |






# 參考資料

- [YubiKey 5 Series Technical Manual](https://support.yubico.com/support/solutions/articles/15000014219-yubikey-5-series-technical-manual)
- [Reddit r/YubiKey 置頂 FAQ](https://www.reddit.com/r/yubikey/comments/9kodiy/meta_ryubikey_frequently_asked_questions_please/)
