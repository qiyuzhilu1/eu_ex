[English](README.md) | [简体中文](README_sc.md) | 正體中文

# eu_ex

eu_ex 是 EUserv_extend 的簡寫。一個 Python 指令碼，可以幫你續期免費 EUserv IPv6 VPS。

這個指令碼可以自動檢查你賬戶中的 VPS 數量，如果可以續期，就為 VPS 續期。

## 如何使用

1. 安裝 Python3 和必要的依賴，以下命令在 debian/ubuntu 為例，

   ```bash
   #Install Python3
   apt install python3 python3-pip -y
   #Intstall dependences
   pip install requests beautifulsoup4
   ```

2. 不建議在 `main.py` 檔案第 37-38 行直接用你的真實值去替換`USERNAME`和`PASSWORD`。可以使用環境變數傳入。

   你可以新增多個賬戶，並以一個空格分隔。

3. 你可以新增多個 mailparser.io 解析資料的下載 URL ID，並以一個空格分隔。下載的 URL ID 在`https://files.mailparser.io/d/<download_url_id>`。

4. 將 **Actions secrets** 傳入你的 GitHub Action 執行環境的環境變數。例如，以下環境變數是必需的。

   ```
   env:
       USERNAME: ${{ secrets.USERNAME }}
       PASSWORD: ${{ secrets.PASSWORD }}
       # https://mailparser.io   
       MAILPARSER_DOWNLOAD_URL_ID: ${{ secrets.MAILPARSER_DOWNLOAD_URL_ID }}
   ```

## 郵件轉發和 mailparser 設定
### 郵件轉發

以 gmail 為例, 轉發郵件至 [mailparser](https://mailparser.io)。可以是非 gmail 郵箱，前提是可以收到 euserv 的郵件。目前 outlook/hotmail 是收不到的。

- ![gmail_filter_keys](./images/gmail_filter_keys.png)

- ![gmail_filter_setting](./images/gmail_filter_setting.png)

- ![gmail_forward_setting](./images/gmail_forward_setting.png)

### Mailparser 設定

- 首先建立新的收件箱。
- 建立資料解析規則。
  - 資料解析規則，pin 為必需，其他可選。
   ![mailparser_data_parsing_rules](./images/mailparser_data_parsing_rules.png)
  - pin 的解析規則
  ![mailparser_data_parsing_rules_pin](./images/mailparser_data_parsing_rules_pin.png)
  - subject 的解析規則
  ![mailparser_data_parsing_rules_subject](./images/mailparser_data_parsing_rules_subject.png)
  - sender 的解析規則
  ![mailparser_data_parsing_rules_sender](./images/mailparser_data_parsing_rules_sender.png)
  - receiver 的解析規則
  ![mailparser_data_parsing_rules_receiver](./images/mailparser_data_parsing_rules_receiver.png)
- 建立解析資料下載 URL
  - 解析資料下載 URL
  ![mailparser_parsed_data_downloads](./images/mailparser_parsed_data_downloads.png)
- 解析資料下載設定
  ![mailparser_parsed_data_downloads_setting](./images/mailparser_parsed_data_downloads_setting.png)
- mailparser 收件箱設定（可選，為減少收到 spam 郵件的風險，最好設定）
  - mailparser 收件箱設定 1
  ![mailparser_inbox_setting_1](./images/mailparser_inbox_setting_1.png)
  - mailparser 收件箱設定 2
  ![mailparser_inbox_setting_2](./images/mailparser_inbox_setting_2.png)

## 最終效果
效果如圖,

![mailparser_inbox_setting_2](./images/the_final_effect.png)

## 待辦事項

- [x] ~~驗證 mailparser 解析的`receiver'欄位，以減少惡意郵件的干擾。~~ 由於 mailparser *Inbox Settings - Email Reception*，所以不做了。
- [x] 日誌國際化和本地化。
- [ ] 當驗證碼識別 API 失效時開放預訓練的模型，在本地，不呼叫第三方介面就解決驗證碼識別的問題。
- [ ] 考慮 euserv 訪問超時重試，較低優先順序。

## 鳴謝

- 感謝 EUserv 提供免費的 IPv6 VPS 供我們學習。
- 感謝 CokeMine 和其倉庫貢獻者為我們提供的最初 *EUserv_extend* 指令碼。網際網路永遠不會忘記，但人們會。

## FAQs

1. **Q**: 可以非 gmail 郵箱嗎?

   **A**: 可以是非 gmail 郵箱，前提是可以收到 euserv 的郵件。目前 outlook/hotmail 是收不到的。

2. **Q**: n 個郵箱能在用同一個 mailparser 還是需要申請 n 個 mailparser 與之一 一對應嗎？

   **A**: mailparser free 賬號最多可以設定 10 個收件箱，這 10 個收件箱就可以對應 10 個 euserv 賬號，也就有了 10 個  mailparser parsed data download URL(id)。所以，這得看你 n>10, 還是 n<10  了。n<10， 一個 mailparser 賬號即可，然後 parsed data download URL id 一 一對應  euserv 的註冊郵箱賬號。

3. **Q**: 續期指令碼是如何工作的？

   **A**: EUserv 從 2021 年 9 月底開始，設定了第一道門檻，那就是登入出現驗證碼(成功驗證狀態維持 24 小時)，所以目前就用 TrueCaptcha 提供的 API(每天都有免費額度) 進行識別。沒過多久，大概是 2021 年11月初，EUserv 又設定了第二道門檻，就是續期時的郵件 PIN 碼驗證，這裡解決辦法就大概兩種：a. 登入郵箱獲取含 EUserv PIN 的郵件。b. 將郵件轉化為 HTTP REST API，自動獲取。這裡採取的是方案 b。方案 b 中可選的存在免費額度的，好像目前僅有 [Mailparser](https://mailparser.io) 和 [Zapier Emails Parser](https://parser.zapier.com/)。方案 b 明顯要優於方案 a。

## 參考資訊

### EUserv "PIN for the Confirmation of a Security Check" 原始郵件

```
From：	     EUserv Support <support@euserv.de>
To：	         xyz@example.com
Subject：	 EUserv - PIN for the Confirmation of a Security Check
Content-Type: text/plain; charset = utf-8
Dear XYZ,

you have just requested a PIN for confirmation of a security check at EUserv. If you have not requested the PIN then ignore this email.

PIN:
123456

PLEASE NOTE: If you already have requested a new PIN for the same process this PIN is invalid. Also this PIN is only valid within the session in which it has been requested. This means the PIN is invalid if you for example change the browser or if you logout and perform a new login.


Sincerely,
Your customer support EUserv

--
Web ................: http://www.euserv.com
Login control panel.: https://support.euserv.com
FAQ ................: http://faq.euserv.com
Help & Guides.......: http://wiki.euserv.com
Community / Forum...: http://forum.euserv.com
Mailing-Liste ......: http://www.euserv.com/en/?show_contact=mailinglist
Twitter ............: http://twitter.com/euservhosting
Facebook ...........: http://www.facebook.com/euservhosting
--

EUserv Internet
is a division of
ISPpro Internet KG

Postal address:
ISPpro Internet KG
Division EUserv Internet
P.O. Box 2224
07622 Hermsdorf
GERMANY

Support-Phone: +49 (0) 3641 3101011 (English speaking)

Administration:
ISPpro Internet KG
Neue Str. 4
D-07639 Bad Klosterlausnitz
GERMANY

Management...............: Dirk Seidel
Register.................: AG Jena, HRA 202638
VAT Number...............: 162/156/36600
Tax office ..............: Jena
International VAT Number.: DE813856317
```

