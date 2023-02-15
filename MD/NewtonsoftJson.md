# NuGet套件: Newtonsoft Json.NET
> Popular high-performance JSON framework for .NET

----

## 安裝Newtonsoft Json套件

在要加入套件的專案，點擊右鍵選擇`管理NuGet套件` <br />
![](https://raw.githubusercontent.com/howwmingnew/IWRookieManual/main/Photos/Json/00.png)
<br />

在瀏覽頁面上方的搜尋欄輸入`json`，選取`Newtonsoft.Json`再點擊右邊`安裝`按鈕進行安裝 <br />
![](https://raw.githubusercontent.com/howwmingnew/IWRookieManual/main/Photos/Json/01.png)
<br />

## 實作練習

接下來就開始進入實作練習，我將以原本的`Anker.ini`資料做為參考，並會在後面轉化成`Json`格式文件。<br />
![](https://raw.githubusercontent.com/howwmingnew/IWRookieManual/main/Photos/Json/02.png)
<br />

### 資料整理

在瀏覽`Anker.ini`檔案後我們得知幾個重要資訊:

1. 資料的層級是:
    - **`Fixture`** (ex: Anker)
        - **`Implant System`** (ex: SBI、SBII、SBIII)
            - **`Implant`** (ex: SBN3409、SBN3410...)
2. 資料的內容分別是:
    - **`Fixture`**
        - Fixture的名稱 (ex: Anker)
        - 列著所有Implant System的清單
    - **`Implant System`**
        - Implant System的名稱 (ex: SBI)
        - 列著所有Implant的清單
    - **`Implant`**
        - Implant的名稱 (ex: SBN3409)
        - Platform (ex: 3.40)
        - Diameter (ex: 2.50)
        - Length (ex: 8.50)
<br />

### 定義Class
資料整理完後就可以開始寫相對應的Class了<br />
![](https://raw.githubusercontent.com/howwmingnew/IWRookieManual/main/Photos/Json/03.png)
<br />

### 新增及初始化一個剛定義好的Class
新增一個Fixture的Class並填上初始化資料(本次練習所新增的Class名稱取名為`fixture`)<br />
![](https://raw.githubusercontent.com/howwmingnew/IWRookieManual/main/Photos/Json/04.png)
<br />

### 匯出Json
使用Newtonsoft.Json套件內的`JsonConvert.SerializeObject`函式，先將`fixture`這個Class直接轉換成string字串，之後再寫入到`D:\\Fixture.json`檔案內(如果沒有此檔則會自動生成)，這樣就完成匯出了<br />
```C#
//Export json
string jsonString = JsonConvert.SerializeObject(fixture, Formatting.Indented);
File.WriteAllText(@"D:\Fixture.json", jsonString);
```
<br />

打開`D:\\Fixture.json`檔案，內容會長這樣:<br />
![](https://raw.githubusercontent.com/howwmingnew/IWRookieManual/main/Photos/Json/07.png)
<br />

### 匯入Json

匯入方式就是倒著流程走，讀檔-->取得string-->string轉成 Fixture Class

```C#
//Import json
StreamReader streamReader = new StreamReader(@"D:\Fixture.json");
string jsonString = streamReader.ReadToEnd();
streamReader.Close();
Fixture importFixture = JsonConvert.DeserializeObject<Fixture>(jsonString);
```



----
[Newtonsoft Json.NET官方網站](https://www.newtonsoft.com/json)

