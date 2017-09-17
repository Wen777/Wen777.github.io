---
title: 譯 十個logging的戒律
date: 2017-07-20 00:41:39
desc:
tags: [logging, engineering]
---

![commandments](https://c1.staticflickr.com/7/6165/6232563713_e60521fce9_b.jpg)

---

譯者注:

Logging是一個現代開發者在開發的時候要注意到的事, 好的log有助於debgug和troubleshooting。
產品在線上出問題的時候, 你或者是Ops同事會感激有很好的log可以看。因為這議題是如此基本又重要,
在這邊介紹一篇Masterzen's Blog關於logging的[文章](http://www.masterzen.fr/2013/01/13/the-10-commandments-of-logging/)

---

###### 正文

// 再次看一下如何翻得更精確
歡迎來到我的blog。在回答過Paris devops的email list上的一個討論串, 我就有把這個主題寫下的想法。

當我在寫這篇文章時, 我是從Ops的角度出發寫給開發者看的。

我認為對開發者來說, 其中一件很困難的事情就是知道什麼是日誌(log)和如何寫好程式日誌(logging)。
這件事跟預測很類似。你很難知道當你在故障排除時, 你需要哪些訊息。這也是我希望這十個原則可以讓你設計的程式日誌更好, 也幫到你的ops同事。

<!--more-->

1. ### 不應該自己寫log

    永遠不要用`printf`或是自己把日誌寫入檔案。也不要靠自己處理[`Log rotation`](https://en.wikipedia.org/wiki/Log_rotation)。
    幫幫你的Ops同事個忙, 使用標準函式庫或是system API call來處理logging。

    這樣一來, 你可以確保程式能很好的跟其他系統元件整合, 程式日誌寫到正確的地方或是收集日誌的網路服務而不需要特別的設定。

    如果你正在使用系統API, 試著學會用`syslog(3)`寫程式日誌。

    如果偏好使用logging library, 在Java的生態系裡面有許多選擇, 像是Log4j, JCL, slf4j 和 logback。我個人偏好slf和logback的組合, 因為效果好而且很容易設定(而且能夠用JMX設定或是重新載入設定檔)。

    slf4j最大的好處是你找到適合的logging 的後端服務, 你可以替換掉原本的。如果你正在寫一個library, 這件事非常重要, 因為你允許任何人使用你的library可以搭配他自己的logging後台而不需要改動你的library。

    不同的程式語言有許多logging library可以選擇。例如Ruby有 Log4r, stdlib logger, 或是 Jordan Sissel 的 Ruby-cabin。

    你如果認為使用logging library會造成CPU的消耗, 你可以忽略這篇文章。不要把log語句寫在密集的迴圈內, 除此之外, 你不會感覺到很明顯的影響。 

2. ### 應該分程度寫log

    如果你遵守第一誡, 那你有辦法在你的程式裡面使用不同程度的log語句。其中一個比較困難的事是決定哪個log語句要標示為什麼樣程度的訊息。

    在這邊給一些建議:

        * TRACE level: 這個程度的訊息是幫助開發時debug用的, 千萬不要把這種訊息放到版本控制裡面(VCS)。

        * DEBUG level: 把在程式裡面會發生的事情都用這個程度紀錄。
        大部分的時候都是debugging會用到。我個人會在產品要進入上線階段之前減少這個程度的訊息的數量。
        只留下最有意義的訊息。在需要故障排除時可以再打開。

        * INFO level: 這個程度的訊息是紀錄所有使用者導向(user-driven)的行為或是系統特有的
        (ex: 定期排好的操作)

        * NOTICE level: 這個程度的日誌用在程式上線的時候。把不是錯誤但是執得注意的資訊用這個程度紀錄。

        * WARN level: 把有機會變成error的事件用這個程度記錄下來。舉例來說,
        像是呼叫資料庫這個動作花了超過預期的時間或者一個記憶體快取(memory cache)快達到它的容量上限。
        這有助於適當的自動化警告, 還有在故障排除時, 有助於了解系統在當機之前發生哪些事情。

        * ERROR level: 把所有的error用這個程度紀錄在日誌內。
        可以是呼叫一個API失敗的結果或是程式內部錯誤。

        * FATAL level: 非常謹慎的用把訊息用這個程度紀錄。
        這個程度的訊息不該很頻繁在程式內發生。一般來說, 在程式結束前才會把訊息用這個FATAL記錄到日誌內。
        舉例來說, 當一個網路進程(network daemon)無法連接網卡，
        合理的做法是把這個訊息記錄到日誌內然後結束進程。

    預設的日誌程度在不同的環境可能很不一樣。舉例來說, 在server上使用INFO這個等級紀錄日誌, 但是在我的個人電腦上使用DEBUG程度紀錄程式日誌。 很難在一台你沒有權限的電腦上排除故障, 而且當你正在做支援工作的時候, 請使用者把程式日誌寄給你比讓使用者改變日誌的程度設定再寄程式日誌給你容易。當然,YMMV

3. Honor thy log category

    我在第一誡提到的logging library, 大部分都允許指定logging category. 這些分類讓程式能夠把日誌歸類, 最終會根據日誌設定檔, 把日誌存到不同的地方或是不儲存。

    Most of the time java developers use the fully qualified class name where the log statement appears as the category. This is a scheme that works relatively fine if your program respects the simple responsibility principle.

    大部分的JAVA開發者會使用合格的變數名稱

    JAVA 的日誌包裡面日誌類別有層級之分。舉例來說, 寫日誌用`com.daysofwonder.ranking.ELORankingComputation`這個分類, 可以放到最高層級的`com.daysofwonder.ranking`。這樣一來, 維運工程師可以設定這個最高層級的日誌類別, 進而影響到所有的ranking子系統的日誌設定。如果有需要, 也可以同時針對子類別做程式日誌的設定。

    我們可以把這個規則延伸一下以幫助在具體的情境下做故障排除。想像一下你正在處理一個伺服器程式會回應使用者的請求(像是REST API)。如果你的伺服器是用這個類別記錄程式日誌`my.service.api.<apitoken>` (apitoken是對應一個具體的使用者)。你可以透過`my.service.api`記錄所有的API日誌，或是透過`my.service.api.<bad-user-api-token>`更詳細的紀錄一個不預期的API使用者。當然，這要求你的系統能夠動態的改變程式日誌的設定。

4. ### 應該寫有意義的log

    這應該是所有誡條裡面最重要的一個。如果模糊的程式日誌要求人對程式有很深程度的瞭解才能看懂, 沒有什麼比這種事情更糟。

    When writing your log entries messages, always anticipate that there are emergency situations where the only thing you have is the log file, from which you have to understand what happened. Doing it right might be the subtle difference between getting fired and promoted :)

    當你正在設計哪些資訊要寫入程式執行日誌，要記住會有一種緊急情況，你手上唯一有的東西是那個程式執行日誌, 而且你必須靠著它了解發生什麼事情。被fire或是得到升遷微妙的區別可能是程式日誌有沒有寫好。

    When a developer writes a log message, it is in the context of the code in which the log directive is to be inserted. In those conditions we tend to write messages the infer on the current context. Unfortunately when reading the log itself this context is absent, and those messages might not be understandable.

    當開發者在寫程式日誌的訊息, 那些訊息是在那個程式碼情境內。因此我們會傾向把訊息寫的符合當下的情境。不幸的是當我們在看程式日誌的時候是缺乏上下文的，因此那些程式日誌的訊息可能變得無法理解。

    One way to overcome this situation (and that’s particularly important when writing at the warn or error level), is to add remediation information to the log message, or if not possible, what was the purpose of the operation, and it’s outcome.

    一種處理這個問題的方法(尤其對在寫WARN 或是 ERROR 程度的訊息特別重要)是寫入什麼造成這個狀況，後續的結果會是什麼，可能的話加入建議處理的方法。

    Also, do not log message that depends on previous messages content. The reason is that those previous messages might not appear if they are logged in a different category or level, or worst can appear in a different place (or way before) in a multi-threaded or asynchronous context.

     此外，也不要讓當下的程式日誌訊息依賴著前一條訊息。原因是前一條程式訊息可能因為用不同的程度記錄或是被洩入不同的分類而沒有出現。或者更糟的是多執行緒或是非同步的程式可能讓的訊息出現在不同順序。


5. ### 應該用英文寫log

    This might seem a strange commandment, especially coming from a French guy. First, I still think English is much more concise than French and better suits technical language. Why would you want to log in French if the message contains more than 50% of English words in it?
    你可能會覺得這條戒律有點怪，尤其是當這件事是一個法國人提出來的。我仍然認為英文是比法文還嚴謹且更適合描述技術的語言。如果用法文寫程式日誌的訊息會比用英文寫還多50%的字，難道你還會想用法文寫嗎？

    This being put aside, here are the essential reason behind this commandment:

    先把前面提的放旁邊，這邊是比較重要的原因：

    使用英文代表你的程式日誌是用ASCII編碼記錄。這非常重要尤其是你不知道程式日誌如何被歸檔。如果程式日誌的訊息用特殊編碼就算是`utf-8`記錄，這也有可能無法被正確的打開查看。更糟的是在儲存的過程中可能被影響到變成亂碼。也要記錄來自使用者的輸入訊息用的編碼。

English means your messages will be in logged with ASCII characters. This is particularly important because you can’t really know what will happen to the log message, nor what software layer or media it will cross before being archived somewhere. If your message uses a special charset or even UTF-8, it might not render correctly at the end, but worst it could be corrupted in transit and become unreadable. Still remains the question of logging user-input which might be in diverse charset and/or encoding.

If your program is to be used by the most and you don’t have the resources for a full localization, then English is probably your best alternative. Now, if you have to localize one thing, localize the interface that is closer to the end-user (it’s usually not the log entries).

    如果你的程式是被廣泛使用而且沒有足夠的資源做在地化(localization)，那英文會是最好的選擇。如果你必須要做在地化(localization)，只針對使用者會接觸的介面做在地化(通常不會是程式日誌)。

if you localize your log entries (like for instance all the warning and error level), make sure you prefix those by a specific meaningful error-code. This way people can do language independent Internet search and find information. Such great scheme has been used a while ago in the VMS operating system, and I must admit it is very effective. If you were to design such scheme, you can adopt this scheme: APP-S-CODE or APP-S-SUB-CODE, with respectively:

    

APP: your application name on 3 letters
S: severity on 1 letter (ie D: debug, I: info…)
SUB: the sub part of the application this code pertains to
CODE: a numeric code specific to the error in question



6. ### 寫log應該要包含情境

So, there’s nothing worst than this kind of log message:

```
 Transaction failed
```

Or

```
User operation succeeds
```

or in case of API exceptions:

```
java.lang.IndexOutOfBoundsException
```

Without proper context, those messages are only noise, they don’t add value and consume space that could have been useful during troubleshooting.

Messages are much more valuable with added context, like:

```
Transaction 2346432 failed: cc number checksum incorrect
```

or

```
User 54543 successfully registered e-mail user@domain.com
```

or

```
IndexOutOfBoundsException: index 12 is greater than collection size 10
```

Since we’re talking about exceptions in this last context example, if you happen to propagate up exceptions, make sure to enhance them with context appropriate to the current level, to ease troubleshooting, as in this java example:

```
 public void storeUserRank(int userId, int rank, String game) {
    try {
      ... deal database ...
    } catch(DatabaseException de) {
      throw new RankingException("Can't store ranking for user "+userId+" in game "+ game + " because " + de.getMessage() );
    }
  }

```

So the upper-layer client of the rank API will be able to log the error with enough context information. It’s even better if the context becomes parameters of the exception itself instead of the message, this way the upper layer can use remediation if needed.

An easy way to keep a context is to use the MDC some of the java logging library implements. The MDC is a per thread associative array. The logger configuration can be modified to always print the MDC content for every log line. If your program uses a per-thread paradigm, this can help solve the issue of keeping the context. For instance this java example is using the MDC to log per user information for a given request:

```
  class UserRequest {
    ...
    public void execute(int userid) {
      MDC.put("user", userid);

      // ... all logged message now will display the user=<userid> for this thread context ...
      log.info("Successful execution of request");

      // user request processing is now finished, no need to log our current user anymore
      MDC.remote("user");
    }
  }
```

Note that the MDC system doesn’t play nice with asynchronous logging scheme, like Akka’s logging system. Because the MDC is kept in a per-thread storage area and in asynchronous systems you don’t have the guarantee that the thread doing the log write is the one that has the MDC. In such situation, you need to log the context manually with every log statement.



7. ### log的格式要能夠被解析
Thou shalt log in machine parseable format

Log entries are really good for human, but very poor for machines. Sometimes it is not enough to manually read log files, you need to perform some automated processing (for instance for alerting or auditing). Or you want to store centrally your logs and be able to perform search requests.

So what happens when you embed the log context in the string like in this hypothetical logging statement:

```
log.info("User {} plays {} in game {}", userId, card, gameId);
```

This will produce this kind of text:

```
2013-01-12 17:49:37,656 [T1] INFO  c.d.g.UserRequest  User 1334563 plays 4 of spades in game 23425656
```

Now, if you want to parse this, you’d need the following (untested) regex:

```
  /User (\d+) plays (.+?) in game (\d+)$/
```

Well, this is not easy and very error-prone, just to get access to string parameters your code already knows natively.

So what about this idea, I believe Jordan Sissel first introduced in his ruby-cabin library: Let add the context in a machine parseable format in your log entry. Our aforementioned example could be using JSON like this:

```
2013-01-12 17:49:37,656 [T1] INFO  c.d.g.UserRequest  User plays {'user':1334563, 'card':'4 of spade', 'game':23425656}
```

Now your log parsers can be much easier to write, indexing now becomes straightforward and you can enable all logstash power.

8. ### log的內容不該太長或太短

That might sound stupid, but there is a right balance for the amount of log.

Too much log and it will really become hard to get any value from it. When manually browsing such logs, there is too much clutter which when trying to troubleshoot a production issue at 3AM is not a good thing.

Too little log and you risk to not be able to troubleshoot problems: troubleshooting is like solving a difficult puzzle, you need to get enough material for this.

Unfortunately there is no magic rule when coding to know what to log. It is thus very important to strictly respect the 1st and 2nd commandments so that when the application will be live it will be easier to increase or decrease the log verbosity.

One way to overcome this issue is during development to log as much as possible (do not confuse this with logging added to debug the program). Then when the application enters production, perform an analysis of the produced logs and reduce or increase the logging statement accordingly to the problems found. Especially during troubleshooting, note the part of the application you wished you could have more context or logging, and make sure to add those log statements to the next version (if possible at the same time you fix the issue to keep the problem fresh in memory). Of course that requires an amount of communication between ops and devs.

This can be a complex task, but I would recommend to refactor logging statements as much as you refactor the code. The idea would be to have a tight feedback loop between the production logs and the modification of such logging statement. It’s even more efficient if your organization has a continuous delivery process in place, as the refactoring can be constant.

Logging statements are some kind of code metadata, at the same level of code comments. It’s really important to keep the logging statements in sync with the code. There’s nothing worst when troubleshooting issues to get irrelevant messages that have no relation to the code processed.



9. ### 寫log的時候應該考慮到要讀log的人


Why adding logging to an application?

The only answer is that someone will have to read it one day or later (or what is the point?). More important it is interesting to think about who will read those lines. Depending on the person you think will read the log messages you’re about to write, the log message content, context, category and level will be quite different.

Those persons can be:

an end-user trying to troubleshoot herself a problem (imagine a client or desktop program)
a system-administrator or operation engineer troubleshooting a production issue
a developer either for debugging during development or solving a production issue
Of course the developer knows the internals of the program, thus her log messages can be much more complex than if the log message is to be addressed to an end-user. So adapt your language to the intended target audience, you can even dedicate separate categories for this.

10. ### 不要只為了故障排除而寫log

As the log messages are for a different audience, log messages will be used for different reasons. Even though troubleshooting is certainly the most evident target of log messages, you can also use log messages very efficiently for:

Auditing: this is sometimes a business requirement. The idea is to capture significant events that matter to the management or legal people. These are statements that describe usually what users of the system are doing (like who signed-in, who edited that, etc…).

Profiling: as logs are timestamped (sometimes to the millisecond level), it can become a good tool to profile sections of a program, for instance by logging the start and end of an operation, you can either automatically (by parsing the log) or during troubleshooting infer some performance metrics without adding those metrics to the program itself.

Statistics: if you log each time a certain event happens (like a certain kind of error or event) you can compute interesting statistics about the running program (or the user behaviors). It’s also possible to hook this to an alert system that can detect too many errors in a row.

結論

I hope this will help you produce more useful logs, and bear with me if I forgot an essential (to you) commandments. Oh and I can’t be held responsible if your log doesn’t get better after reading this blog :)

It’s possible that those 10 commandments are not enough, so feel free to use the comment section (or twitter or your own blog) to add more useful tips.

Thanks for reading.



Credit
[image](https://pixabay.com/zh/%E6%88%92%E5%BE%8B-%E5%AE%97%E6%95%99-%E5%8D%81-%E4%BA%9A%E4%BC%AF%E6%8B%89%E7%BD%95-%E7%9F%B3-%E5%9C%A3-%E7%8A%B9%E5%A4%AA%E4%BA%BA-%E5%9F%BA%E7%9D%A3%E6%95%99-311202/)
