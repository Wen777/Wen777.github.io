---
title: backend-questions
date: 2017-08-12 20:52:45
desc:
tags:
---

## Questions

Quote from Triton Ho

（也許會很罪很多人）

最近遇上很多人，隨隨便便的自稱自己為fullstack(=frontend + backend) developer。

這是一份自我評分的題目表，讓大家知道真正的backend開發者需要知道什麼的。

（個人看法：如果你回答是＜５分的，還是別自稱fullstack好一點點）

資料正確篇：

1.　你知道怎寫optimistic lock？

2.　你懂得怎去使用Redis / Zookeeper，去寫一個正確（先不談效能和starvation）的鎖嗎？

3.　假設你在寫遊戲，現在用戶Ａ可以把自己的分數轉帳給用戶Ｂ。你知道怎樣做，才不會發生因為當機而數據出錯嗎？（數據出錯＝Ａ扣了分數沒加給Ｂ，或是Ｂ加了分數，沒從Ａ扣掉）

4.　（續上題）你知道有什麼情況下會發生deadlock嗎？你知道如何回避嗎？

架構篇：

5.　你懂得寫一個簡單（同時在線＜１０００人好了）的對話軟體的後端，而這個後端是由２台Application Server組成的？

6.　如果你的客戶端需要持續更新資料（比如說股票價格，籃球比賽分數），你知道不應該每數秒做一次Get request，而是應該打開websocket / Firebase push notification（或是相似的技術）

7.　你知道跟為何跟客戶建立websocket的伺服器，最好跟處理普通HTTP Request的伺服器最好別放在同一台機器上嗎？

8.　你知道什麼時候才應該使用HTTP 202，並且明白使用這個的痛苦？

9.　你知道為何會消耗高CPU / RAM / 時間的工作（例子：一份涉及大量資料會跑很久的報表），不應該放在Application Server上面跑？

10.　你知道怎去寫一個好的cron job，即使執行機器趴掉也不會發生data corruption，也不需要人力介入。

11.　你知道如何善用MessageQueue的blocking機制，寫出一個background worker，在沒有工作時那個worker接近不使用CPU嗎？

12.　（續上題）如果沒有blocking-wait，你知道如何寫一個background worker，在沒有工作時那個worker接近不使用CPU嗎？

效能篇：

13.　你知道為何不應該使用for-loop，把資料一筆一筆地寫入資料庫嗎？

14.　你知道auto-increment在系統效能的隱憂嗎？

安全篇：

15.　你知道使用auto-increment，但又不讓競爭對手猜到你的業務量的方法嗎？

16.　你知道HTTP OPTIONS這個method嗎？

17.　你知道不能把客戶的信用卡號碼（不管是否有加密）存到自己資料庫嗎？（除非你就是在paypal這些金流公司／銀行工作）

18.　你知道把sha256(<password>)存到資料庫是不安全的？

19.　你知道最理想的系統，應該在client side做對密碼做bcrypt處理，再做asymmetric encryption才丟到server？

20.　你知道為何改動密碼時，應該要求用戶輸入本來的密碼？
