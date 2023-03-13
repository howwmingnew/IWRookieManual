# NuGet套件: Humanizer
> Popular high-performance JSON framework for .NET

----

## 目次
- [人性化字符串(Humanize String)](#人性化字符串(Humanize-String))
- [截斷字符串(Truncate String)](#截斷字符串(Truncate-String))
- [格式化字符串(Format String)](#格式化字符串(Format-String))
- [人性化日期時間(Humanize DateTime)](#人性化日期時間(Humanize-DateTime))
- [人性化時間跨度(Humanize TimeSpan)](#人性化時間跨度(Humanize-TimeSpan))
- [單複數轉換(Inflector methods)](#單複數轉換(Inflector-methods))
- [流利的日期(Fluent Date)](#流利的日期(Fluent-Date))
- [字節大小(ByteSize)](#字節大小(ByteSize))

----

## 安裝Humanizer套件

在要加入套件的專案，點擊右鍵選擇`管理NuGet套件` <br />
![](https://raw.githubusercontent.com/howwmingnew/IWRookieManual/main/Photos/Json/00.png)
<br />

在瀏覽頁面上方的搜尋欄輸入`Humanizer`，搜尋結果會有許多的Humanizer版本分別代表不同語言，如果要全語系就直接安裝Humanizer就好，
如果是要英文和繁中就安裝Humanizer.Core + Humanizer.Core.zh-Hant。
![](https://raw.githubusercontent.com/howwmingnew/IWRookieManual/main/Photos/CommonFunctions/06.png)
<br />

----

## 人性化字符串(Humanize String)

僅包含大寫字母且僅由一個單詞組成的字符串始終被視為首字母縮略詞（無論其長度如何）。
為了保證任何任意字符串始終是人性化的，您必須使用轉換（請參閱下面`Transform`方法）：
```C#
"HUMANIZER".Transform(To.LowerCase, To.TitleCase) => "Humanizer"
```

```C#
"Sentence casing".Transform(To.LowerCase) => "sentence casing"
"Sentence casing".Transform(To.SentenceCase) => "Sentence casing"
"Sentence casing".Transform(To.TitleCase) => "Sentence Casing"
"Sentence casing".Transform(To.UpperCase) => "SENTENCE CASING"
```

----

## 截斷字符串(Truncate String)

您可以使用 `Truncate` 方法截斷字符串：
```C#
"Long text to truncate".Truncate(10) => "Long text…"
"Long text to truncate".Truncate(10, "---") => "Long te---"
"Long text to truncate".Truncate(10, Truncator.FixedLength, TruncateFrom.Left) => "… truncate"
````

----

## 格式化字符串(Format String)

您可以使用 `FormatWith()` 方法格式化字符串：
```C#
"To be formatted -> {0}/{1}.".FormatWith(1, "A") => "To be formatted -> 1/A."
```
<br />

您還可以指定要顯式用作 FormatWith() 方法的第一個參數的區域性：
```C#
"{0:N2}".FormatWith(new CultureInfo("ru-RU"), 6666.66) => "6 666,66"
```

----

## 人性化日期時間(Humanize DateTime)

您可以將 `DateTime` 或 `DateTimeOffset` 的實例人性化，並取回一個字符串，說明時間向前或向後多遠：

```C#
DateTime.UtcNow.AddHours(-30).Humanize() => "yesterday"
DateTime.UtcNow.AddHours(-2).Humanize() => "2 hours ago"

DateTime.UtcNow.AddHours(30).Humanize() => "tomorrow"
DateTime.UtcNow.AddHours(2).Humanize() => "2 hours from now"

DateTime.UtcNow.AddHours(-2).Humanize(culture: new CultureInfo("zh-Hant"))	=> "2 小時前"
DateTime.UtcNow.AddHours(-2).Humanize(culture: new CultureInfo("zh-cht"))	=> "2 小時前"
DateTime.UtcNow.AddHours(-2).Humanize(culture: new CultureInfo("en-US")) => "2 hours ago"

DateTimeOffset.UtcNow.AddHours(1).Humanize() => "an hour from now"
```
<br />

Humanizer 支持本地和 UTC 日期以及帶偏移量的日期 (DateTimeOffset)。
您還可以提供要與輸入日期進行比較的日期。如果為空，它將使用當前日期作為比較基準。
此外，可以明確指定要使用的文化。如果不是，則使用當前線程的當前 UI 區域性。這是 API 簽名：

```C#
public static string Humanize(this DateTime input, bool utcDate = true, DateTime? dateToCompareAgainst = null, CultureInfo culture = null)
public static string Humanize(this DateTimeOffset input, DateTimeOffset? dateToCompareAgainst = null, CultureInfo culture = null)
```
<br />

`DateTime.Humanize` 有兩種策略：如上所示的默認策略和基於精度的策略。要使用基於精度的策略，您需要對其進行配置：
```C#
Configurator.DateTimeHumanizeStrategy = new PrecisionDateTimeHumanizeStrategy(precision: .75);
Configurator.DateTimeOffsetHumanizeStrategy = new PrecisionDateTimeOffsetHumanizeStrategy(precision: .75); // configure when humanizing DateTimeOffset
```
<br />

默認精度設置為 .75，但您也可以傳遞所需的精度。精度設置為 0.75：
```C#
44 seconds => 44 seconds ago/from now
45 seconds => one minute ago/from now
104 seconds => one minute ago/from now
105 seconds => two minutes ago/from now

25 days => a month ago/from now
```

----

## 人性化時間跨度(Humanize TimeSpan)

您可以在 `TimeSpan` 上調用 `Humanize` 以獲得人性化的表示：
```C#
TimeSpan.FromMilliseconds(1).Humanize() => "1 millisecond"
TimeSpan.FromMilliseconds(2).Humanize() => "2 milliseconds"
TimeSpan.FromDays(1).Humanize() => "1 day"
TimeSpan.FromDays(16).Humanize() => "2 weeks"
```
<br />

`TimeSpan.Humanize` 有一個可選的`precision`參數，它允許您指定返回值的精度。
`precision`的默認值為 1，這意味著僅返回最大的時間單位，就像您在 `TimeSpan.FromDays(16).Humanize()` 中看到的那樣。
以下是指定精度的幾個示例：

```C#
TimeSpan.FromDays(1).Humanize(precision:2) => "1 day" // no difference when there is only one unit in the provided TimeSpan
TimeSpan.FromDays(16).Humanize(2) => "2 weeks, 2 days"

// the same TimeSpan value with different precision returns different results
TimeSpan.FromMilliseconds(1299630020).Humanize() => "2 weeks"
TimeSpan.FromMilliseconds(1299630020).Humanize(3) => "2 weeks, 1 day, 1 hour"
TimeSpan.FromMilliseconds(1299630020).Humanize(4) => "2 weeks, 1 day, 1 hour, 30 seconds"
TimeSpan.FromMilliseconds(1299630020).Humanize(5) => "2 weeks, 1 day, 1 hour, 30 seconds, 20 milliseconds"
```
<br />

可以指定最小時間單位以避免滾動到更小的單位。例如：
```C#
TimeSpan.FromMilliseconds(122500).Humanize(minUnit: TimeUnit.Second) => "2 minutes, 2 seconds"    // instead of 2 minutes, 2 seconds, 500 milliseconds
TimeSpan.FromHours(25).Humanize(minUnit: TimeUnit.Day) => "1 Day"   //instead of 1 Day, 1 Hour
```
<br />

使用 `collectionSeparator` 參數，您可以指定自己的分隔符字符串：
```C#
TimeSpan.FromMilliseconds(1299630020).Humanize(3, collectionSeparator: " - ") => "2 weeks - 1 day - 1 hour"
```
<br />

如果單詞比數字更受歡迎，可以設置 `toWords: true` 參數以將人性化 TimeSpan 中的數字轉換為單詞：
```C#
TimeSpan.FromMilliseconds(1299630020).Humanize(3, toWords: true) => "two weeks, one day, one hour"
```
<br />

## 單複數轉換(Inflector methods)

`Pluralize` 將提供的輸入複數化，同時考慮不規則和不可數的單詞：
```C#
"Man".Pluralize() => "Men"
"string".Pluralize() => "strings"
```
<br />

`Singularize` 將提供的輸入單數化，同時考慮不規則和不可數的單詞：
```C#
"Men".Singularize() => "Man"
"strings".Singularize() => "string"
```
<br />

有時，您可能需要從單數化/複數化詞彙表中添加規則
（下面的示例已經在 `Inflector` 使用的 `DefaultVocabulary` 中）：
```C#
// Adds a word to the vocabulary which cannot easily be pluralized/singularized by RegEx.
// Will match both "salesperson" and "person".
Vocabularies.Default.AddIrregular("person", "people");

// To only match "person" and not "salesperson" you would pass false for the 'matchEnding' parameter.
Vocabularies.Default.AddIrregular("person", "people", matchEnding: false);

// Adds an uncountable word to the vocabulary.  Will be ignored when plurality is changed:
Vocabularies.Default.AddUncountable("fish");

// Adds a rule to the vocabulary that does not follow trivial rules for pluralization:
Vocabularies.Default.AddPlural("bus", "buses");

// Adds a rule to the vocabulary that does not follow trivial rules for singularization
// (will match both "vertices" -> "vertex" and "indices" -> "index"):
Vocabularies.Default.AddSingular("(vert|ind)ices$", "$1ex");
```
<br />

很多時候你想調用 `Singularize` 和 `Pluralize` 來給一個單詞加上數字前綴；
例如“2 個請求”，“3 個男人”。 `ToQuantity` 在提供的單詞前加上數字前綴，
並相應地對單詞進行複數或單數化：
```C#
"case".ToQuantity(0) => "0 cases"
"case".ToQuantity(1) => "1 case"
"case".ToQuantity(5) => "5 cases"
"man".ToQuantity(0) => "0 men"
"man".ToQuantity(1) => "1 man"
"man".ToQuantity(2) => "2 men"
```
<br />

還有一個重載允許您格式化數字。您可以傳入要使用的格式和語言。
```C#
"dollar".ToQuantity(2, "C0", new CultureInfo("en-US")) => "$2 dollars"
"dollar".ToQuantity(2, "C2", new CultureInfo("en-US")) => "$2.00 dollars"
"cases".ToQuantity(12000, "N0") => "12,000 cases"
```
<br />

## 流利的日期(Fluent Date)
Humanizer 提供了一個流暢的 API 來處理 `DateTime` 和 `TimeSpan`，如下所示：
```C#
2.Milliseconds() => TimeSpan.FromMilliseconds(2)
2.Seconds() => TimeSpan.FromSeconds(2)
2.Minutes() => TimeSpan.FromMinutes(2)
2.Hours() => TimeSpan.FromHours(2)
2.Days() => TimeSpan.FromDays(2)
2.Weeks() => TimeSpan.FromDays(14)
```
<br />

月或年沒有流暢的 API，因為一個月可能有 28 到 31 天，
一年可能有 365 或 366 天。
<br />
您可以使用這些方法來替換:
```C#
DateTime.Now.AddDays(2).AddHours(3).AddMinutes(-5)

DateTime.Now + 2.Days() + 3.Hours() - 5.Minutes()
```
<br />

## 字節大小(ByteSize)
Humanizer 包括出色的 (ByteSize)[https://github.com/omar/ByteSize] 庫的一個端口。
對 `ByteSize` 進行了相當多的更改和添加，
以使與 `ByteSize` 的交互更容易並且與 Humanizer API 更一致。
下面是幾個示例，說明如何將數字轉換為字節大小以及大小之間的轉換：
```C#
var fileSize = (10).Kilobytes();

fileSize.Bits      => 81920
fileSize.Bytes     => 10240
fileSize.Kilobytes => 10
fileSize.Megabytes => 0.009765625
fileSize.Gigabytes => 9.53674316e-6
fileSize.Terabytes => 9.31322575e-9
```
<br />

有一些擴展方法允許您將數字轉換為 ByteSize 實例：
```C#
3.Bits();
5.Bytes();
(10.5).Kilobytes();
(2.5).Megabytes();
(10.2).Gigabytes();
(4.7).Terabytes();
```
<br />

您還可以使用 +/- 運算符和 Add/Subtract 方法添加/減去值：
```C#
var total = (10).Gigabytes() + (512).Megabytes() - (2.5).Gigabytes();
total.Subtract((2500).Kilobytes()).Add((25).Megabytes());
```
<br />

您還可以選擇為預期的字符串表示形式提供一種格式。
格式化程序可以包含要顯示的值的符號：`b`、`B`、`KB`、`MB`、`GB`、`TB`。
格式化程序使用內置的 double.ToString 方法，
將 `#.##` 作為默認格式，將數字四捨五入到小數點後兩位：
```C#
var b = (10.505).Kilobytes();

// Default number format is #.##
b.ToString("KB");         // 10.52 KB
b.Humanize("MB");         // .01 MB
b.Humanize("b");          // 86057 b

// Default symbol is the largest metric prefix value >= 1
b.ToString("#.#");        // 10.5 KB

// All valid values of double.ToString(string format) are acceptable
b.ToString("0.0000");     // 10.5050 KB
b.Humanize("000.00");     // 010.51 KB

// You can include number format and symbols
b.ToString("#.#### MB");  // .0103 MB
b.Humanize("0.00 GB");    // 0 GB
b.Humanize("#.## B");     // 10757.12 B
```
<br />

如果你想要一個帶有完整單詞的字符串表示，
你可以在 `ByteSize` 實例上調用 `ToFullWords` ：
```C#
7.Bits().ToFullWords();           // 7 bits
8.Bits().ToFullWords();           // 1 byte
(.5).Kilobytes().ToFullWords();   // 512 bytes
(1000).Kilobytes().ToFullWords(); // 1000 kilobytes
(1024).Kilobytes().ToFullWords(); // 1 megabyte
(.5).Gigabytes().ToFullWords();   // 512 megabytes
(1024).Gigabytes().ToFullWords(); // 1 terabyte
```
<br />

沒有 `Dehumanize` 方法可以將字符串表示變回 `ByteSize` 實例；
但您可以在 `ByteSize` 上使用 `Parse` 和 `TryParse` 來做到這一點。
與其他 `TryParse` 方法一樣，`ByteSize.TryParse` 返回指示解析是否成功的`boolean` 值。
如果該值被解析，它將輸出到提供的 `out` 參數：
```C#
ByteSize output;
ByteSize.TryParse("1.5mb", out output);

// Invalid
ByteSize.Parse("1.5 b");   // Can't have partial bits

// Valid
ByteSize.Parse("5b");
ByteSize.Parse("1.55B");
ByteSize.Parse("1.55KB");
ByteSize.Parse("1.55 kB "); // Spaces are trimmed
ByteSize.Parse("1.55 kb");
ByteSize.Parse("1.55 MB");
ByteSize.Parse("1.55 mB");
ByteSize.Parse("1.55 mb");
ByteSize.Parse("1.55 GB");
ByteSize.Parse("1.55 gB");
ByteSize.Parse("1.55 gb");
ByteSize.Parse("1.55 TB");
ByteSize.Parse("1.55 tB");
ByteSize.Parse("1.55 tb");
```
<br />

最後，如果需要計算傳輸字節數的速率，可以使用 ByteSize 的 Per 方法。 Per 方法接受一個參數——字節的測量間隔；這是傳輸字節所花費的時間。

`Per` 方法返回一個具有 `Humanize` 方法的 `ByteRate` 類別。
默認情況下，速率以秒為單位（例如，MB/s）。
如果需要，可以將 TimeUnit 傳遞給 `Humanize` 以替代間隔。
有效時間間隔為 `TimeUnit.Second`、`TimeUnit.Minute` 和 `TimeUnit.Hour`。
每個間隔的示例和示例字節速率使用情況如下。
```C#
var size = ByteSize.FromMegabytes(10);
var measurementInterval = TimeSpan.FromSeconds(1);

var text = size.Per(measurementInterval).Humanize();
// 10 MB/s

text = size.Per(measurementInterval).Humanize(TimeUnit.Minute);
// 600 MB/min

text = size.Per(measurementInterval).Humanize(TimeUnit.Hour);
// 35.15625 GB/hour
```

----
[Humanizer 官方Github](https://github.com/Humanizr/Humanizer)