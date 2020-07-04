# YubiKey 5 Series 指南

網路上有很多英文的指南，Mandarin 的則只有商家的介紹，既然都花時間研究了就順便記錄下來。

## 目標讀者

YubiKey 支援的功能非常多，本文主要專注在入門的應用，包含：

- 想要設定二階段認證
- 或者想要從簡訊二階段認證遷移到 
  - Authenticator app
  - 或者 security key 的人

本文尚未包含進階應用：

- Smart Card
- OpenPGP

# 二階段認證 / 2-Factor Authentication (2FA) / Multi-Factor Authentication (MFA)

指除了帳號密碼的驗證方式外，額外的認證方式。常見的有

- 簡訊收取一串數字，常見 4-6 碼。
- Authenticator app 產生一串數字，常見為 6 碼。例如 Google Authenticator / Lastpass Authenticator / Yubico Authenticator。
- 硬體 security key 驗證。例如 YubiKey。有非常多種協定。

# 應用協定 / Application

Yubikey 上的應用協定大致有六種，分別是 OTP, FIDO/U2F, FIDO2, OATH, Smart Card, OpenPGP，但其實有些功能性有重疊。

## OTP / One-time-password 應用

「OTP」在 YubiKey 文件中不斷出現，這邊指的是非常特定的用法，即「當 YubiKey 以 USB-A/USB-C 連接到裝置，變成一份虛擬鍵盤，按下 YubiKey，它會輸出一串隨機碼」，以下稱「OTP 應用」。請小心區分其他處出現的「OTP」，嘗試以上下文了解其意義。

OTP 應用有 slot 1 / slot 2 兩組設定，短按 YubiKey 會輸出 slot 1，長按則啊輸出 slot 2。出廠時，slot 1 是被設定好的（詳細如下），slot 2 則是空的。

每一個 slot 都可以設定怎麼產生隨機碼，有以下的幾種規則

- Yubico OTP：slot 1 出廠預設，常用在二階段認證。長度為 44 個字母，前 12 個字母代表該 YubiKey，所以是固定的；後 32 個字母則是每次都不同。
- Static Password：就是一組密碼，基本上就是把密碼存在硬體 security key 裡面。
- OATH-HOTP：TODO





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
