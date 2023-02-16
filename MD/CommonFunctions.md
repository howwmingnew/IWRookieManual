# C# & WPF 常用技巧


----

## 目次
- [委派](#委派)

----

### 委派

> **情境:** 假設我有一個名叫`MainWindow`的主Window以及一個名叫`BtnFuncBar`的UserControl，我想要在點擊`BtnFuncBar`的其中一顆按鈕後回傳訊息給上一層的`MainWindow`

在BtnFuncBar的Class內新增2行定義委派的程式碼，`ClickHandler`和`ClickFromBtnFuncBar`可以自己改名，括號內的`Enum_BtnFuncBar button`則是要傳遞的物件(選填)，要注意的是第一行和第二行`ClickHandler`名稱要一樣<br />
```C#
public delegate void ClickHandler(Enum_BtnFuncBar button);
public event ClickHandler ClickFromBtnFuncBar;
```
<br />

接著在Button Click事件內加上一行使用委派的程式碼<br />
```C#
ClickFromBtnFuncBar?.Invoke(Enum_BtnFuncBar.Button1);
```
<br />

範例:<br />
![](https://raw.githubusercontent.com/howwmingnew/IWRookieManual/main/Photos/CommonFunctions/01.png)
<br />

`MainWindow`若要使用`BtnFuncBar`的委派則可以寫在xaml或是xaml.cs<br /> <br />
XAML寫法:<br />
```XAML
<Grid>
	<BtnFuncBar ClickFromBtnFuncBar="BtnFuncBar_ClickFromBtnFuncBar"/>
</Grid>
```
<br />

XAML.CS寫法:
```C#
BtnFuncBar btnFuncBar = new BtnFuncBar();
btnFuncBar.ClickFromBtnFuncBar += BtnFuncBar_ClickFromBtnFuncBar;
```
<br />

兩種寫法都會把委派指向`MainWindow`的`BtnFuncBar_ClickFromBtnFuncBar`的函式，接著就是補上接收到資料後想做的事情，這樣就大功告成了
```C#
private void BtnFuncBar_ClickFromBtnFuncBar(Enum_BtnFuncBar srcButton)
{
    switch (srcButton)
    {
        case Enum_BtnFuncBar.Button1:
            {
                MessageBox.Show("Click button 1");
                break;
            }
        case Enum_BtnFuncBar.Button2:
            {
                MessageBox.Show("Click button 2");
                break;
            }

        ... 以下省略 ...
    }
}
```

----
[使用委派 (C# 程式設計手冊)](https://learn.microsoft.com/zh-tw/dotnet/csharp/programming-guide/delegates/using-delegates)

