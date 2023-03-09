# C# & WPF 常用技巧


----

## 目次
- [委派](#委派)
- [WPF物件新增屬性](#WPF物件新增屬性)
- [陣列內取Index](#陣列內取Index)
- [C# SolidColorBrush](#SolidColorBrush)
- [CommandLine呼叫](#CommandLine呼叫)
- [Console 輸出](#Console輸出)
- [Lambda 運算式和匿名函式](#Lambda)

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

### WPF物件新增屬性

> **情境:** 假設我有一個名叫`ImplantSystemItem`的UserControl我想要增加一個叫`IsFavorite`的bool屬性，如果`IsFavorite`為`True`則在UserControl上顯示星星圖示，反之`IsFavorite`為`False`則在UserControl上隱藏星星圖示

首先在`ImplantSystemItem`的Class內新增`IsFavorite`屬性<br />
```C#
public static readonly DependencyProperty IsFavoriteProperty = DependencyProperty.Register(
 "IsFavorite",
 typeof(bool),
 typeof(ImplantSystemItem),
 new FrameworkPropertyMetadata(null));
public bool IsFavorite
{
    get => (bool)GetValue(IsFavoriteProperty);
    set => SetValue(IsFavoriteProperty, value);
}
```
<br />

在`namespace`下新增`IsFavorite`轉換成`Visibility`的程式<br />
```C#
[ValueConversion(typeof(bool), typeof(Visibility))]
    public class IsFavoriteConverter : IValueConverter
    {
        public object Convert(object value, Type targetType, object parameter, System.Globalization.CultureInfo culture)
        { return (bool)value ? Visibility.Visible : Visibility.Collapsed; }

        public object ConvertBack(object value, Type targetType, object parameter, System.Globalization.CultureInfo culture)
        { throw new NotSupportedException(); }
    }
```
<br />

`Shift + F7`轉跳到ImplantSystemItem.xaml，在開頭Resource加入剛建立的`IsFavoriteConverter`，記得專案要先建置一次，不然Visual Studio會不認得`IsFavoriteConverter`這個東西
```XAML
<UserControl xmlns:implanttemplate="clr-namespace:ImplantTemplate"
    ...以下省略... >
    <UserControl.Resources>
        <implanttemplate:IsFavoriteConverter x:Key="StarVisibleConverter" />
    </UserControl.Resources>
/UserControl>
```
<br />

再來修改Image的Visibility的內容
```XAML
<Image Visibility="{Binding IsFavorite, UpdateSourceTrigger=PropertyChanged, Converter={StaticResource StarVisibleConverter}}"
 ...以下省略... />
```
<br />

最後很常被忽略的一件事，Image內的Visibility屬性出現了`Binding IsFavorite`，代表`IsFavorite`是需要指定Binding來源的，所以我們要在初始化時就告知編譯器說我要指定自己Class的`IsFavorite`為Binding對象<br />
```C#
public ImplantSystemItem()
{
    DataContext = this;
    ...以下省略...
}
```
<br />

全部做完後重建專案，到`MainWindow`加上ImplantSystemItem，在輸入屬性時就會發現多了剛新增的`IsFavorite`可以使用<br />
![](https://raw.githubusercontent.com/howwmingnew/IWRookieManual/main/Photos/CommonFunctions/02.png)
<br /> <br />
把`IsFavorite`設定為True在預覽畫面也能即時顯示結果<br />
![](https://raw.githubusercontent.com/howwmingnew/IWRookieManual/main/Photos/CommonFunctions/03.png)
<br /> <br />

ImplantSystemItem.xaml.cs內容<br />
![](https://raw.githubusercontent.com/howwmingnew/IWRookieManual/main/Photos/CommonFunctions/04.png)
<br />

ImplantSystemItem.xaml內容<br />
![](https://raw.githubusercontent.com/howwmingnew/IWRookieManual/main/Photos/CommonFunctions/05.png)
<br />

----

### 陣列內取Index
For arrays you can use: Array.FindIndex<T>:<br />
```C#
int keyIndex = Array.FindIndex(words, w => w.IsKey);
```
<br />

For lists you can use List<T>.FindIndex:<br />
```C#
int keyIndex = words.FindIndex(w => w.IsKey);
```
<br />

----

### SolidColorBrush

SolidColorBrush Hex:
```C#
(SolidColorBrush)new BrushConverter().ConvertFrom("#ffaacc");
```
 <br />

 SolidColorBrush Argb:
```C#
SolidColorBrush(Color.FromArgb(255, 255, 0, 0))
```
----
### CommandLine呼叫

簡易版CommanLine呼叫:<br />

```C#
string strCmdText;
strCmdText= "/C copy /b Image1.jpg + Archive.rar Image2.jpg";
System.Diagnostics.Process.Start("CMD.exe",strCmdText);
```
<br />

完整版CommandLine呼叫:<br />
```C#
/// <summary>
/// CommandLine(命令提示字元)
/// </summary>
/// <param name="fileName">要開啟的檔案</param>
/// <param name="arguments">要傳進去的參數</param>
/// <param name="byAdmin">Admin管理員執行</param>
public static void RunCommandLine(string fileName, string arguments = "", bool admin = true, bool waitingResult = false)
{
    if (fileName == ConstCommandLine.Browser) { arguments = ConstCommandLine.BrowserArguments + arguments; }

    try
    {
        Process process = new Process();
        if (admin)
        {
            ProcessStartInfo processStartInfo = new ProcessStartInfo(fileName)
            { UseShellExecute = true, Verb = "runas" };
            process.StartInfo = processStartInfo;
        }
        else
        { process.StartInfo.UseShellExecute = false; process.StartInfo.FileName = fileName; }
        process.StartInfo.Arguments = arguments;
        process.Start();
        if (waitingResult) { process.WaitForExit(); }
    }
    catch (Exception ex)
    {
        //WpfLib4Ray.Main.WPFShowMessage4OMP(owner: (OrderManagerPro.MainWindow)App.Current.MainWindow, ex.Message, icon: MessageBoxImage.Error);
        MessageBox.Show(ex.Message);
        return;
    }
}
```

----

### Console輸出

原本:
```C#
Name:Omelchenko          Score[0]:97
Name:Garcia      Score[0]:97
Name:Fakhouri    Score[0]:99
Name:Feng        Score[0]:93
Name:Garcia      Score[0]:92
Name:Adams       Score[0]:99
Name:Zabokritski         Score[0]:96
Name:Tucker      Score[0]:94
```
 <br />

排版後:
 ```C#
Omelchenko                        97
Garcia                            97
Fakhouri                          99
Feng                              93
Garcia                            92
Adams                             99
Zabokritski                       96
Tucker                            94
```
 <br />

 方法:
```C#
public static void prn(string fname, string fvalue)
{
    string outstring = fname.PadRight(20)  +"\t\t  " + fvalue;
    Console.WriteLine(outstring);
}
```

```C#
prn(student.Name, student.Scores.ToString());
```
----

### Lambda 運算式和匿名函式

> 您可以使用 Lambda 運算式 來建立匿名函式。 請使用 `Lambda 宣告運算子 =>` 來分隔 Lambda 的參數清單及其主體。 Lambda 運算式可以是下列兩種形式之一：

在下列範例中，Lambda 運算式 `x => x * x` 會指定名為 `x` 的參數，並傳回平方的值 `x` ，會指派給委派類型的變數：
```C#
Func<int, int> square = x => x * x;
Console.WriteLine(square(5));
// Output:
// 25
```
<br />

您可以在任何需要委派型別或運算式樹狀架構執行個體的程式碼中使用 Lambda 運算式，例如作為 [Task.Run(Action)](https://learn.microsoft.com/zh-tw/dotnet/api/system.threading.tasks.task.run#system-threading-tasks-task-run(system-action)) 方法的引數，以傳遞應該在背景中執行的程式碼。 您也可以在 [C# 中撰寫 LINQ](https://learn.microsoft.com/zh-tw/dotnet/csharp/linq/)時使用 Lambda 運算式，如下列範例所示：
```C#
int[] numbers = { 2, 3, 4, 5 };
var squaredNumbers = numbers.Select(x => x * x);
Console.WriteLine(string.Join(" ", squaredNumbers));
// Output:
// 4 9 16 25
```
<br />

您會以括弧括住 Lambda 運算式的輸入參數。 以空括號指定零個輸入參數：
```C#
Action line = () => Console.WriteLine();
```
<br />

篩選:
```C#
Implant implant = ImplantList.Find(x => x.Name == "SBII");
```

搭配LINQ的篩選:
```C#
Implant focusItem = ImplantList.Where( c => c.Name != string.Empty ).Where( c => c.Name == colValue).FirstOrDefault();
```

搭配LINQ的排序:
```C#
List<Implant> platformSpec = platformSpec.OrderBy(x => x.Platform).ToList();              //遞增排序
List<Implant> platformSpec = platformSpec.OrderByDescending(x => x.Platform).ToList();    //遞減排序
List<Implant> implantList = implantList.OrderBy(x => x.Platform).ThenBy(x => x.Diameter).ToList();  //先以Platform遞增排序、若有相同數值則再以Diameter遞增排序
```

----
[使用委派 (C# 程式設計手冊)](https://learn.microsoft.com/zh-tw/dotnet/csharp/programming-guide/delegates/using-delegates)
[Lambda 運算式和匿名函式](https://learn.microsoft.com/zh-tw/dotnet/csharp/language-reference/operators/lambda-expressions)

