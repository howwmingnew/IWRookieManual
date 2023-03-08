# C# 陣列: Array、List、Queue、Stack

> 除了Array以外，其它三個都是從`System.Collections.Generic`套件引用的，所以請在開頭加上 **`using System.Collections.Generic;`**

## 目次
- [Array](#Array)
- [List](#List)
- [Queue](#Queue)
- [Stack](#Stack)

----

## Array

C#內建自帶Array，無須引用其它套件，但在做資料處理要寫比較多程式碼，且不是動態陣列，意謂著初始化後如果要新增或刪除數值就還得手動調整陣列容量 <br />

初始化: <br />
```C#
int[] numbersArray = { 1, 2, 3, 4 };
```
<br />

取得index = 1的數值: <br />
```C#
int IndexOneValue = numbersArray[1];
```
<br />

加入數值: <br />
```C#
int newNumber = 5;
Array.Resize(ref numbersArray, numbersArray.Length + 1);
numbersArray[numbersArray.Length - 1] = newNumber;
```
<br />

移除最後一個數值: <br />
```C#
int indexToRemove = numbersArray.Length - 1;
int[] newNumbers = new int[numbersArray.Length - 1];
Array.Copy(numbersArray, 0, newNumbers, 0, indexToRemove);
Array.Copy(numbersArray, indexToRemove + 1, newNumbers, indexToRemove, numbersArray.Length - indexToRemove - 1);
numbersArray = newNumbers;
```
<br />

## List

List應該是最多人使用的陣列物件，應該不用再多加敘述 <br />

初始化: <br />
```C#
List<int> numberList = new List<int> { 1, 2, 3, 4 };
```
<br />

取得index = 1的數值: <br />
```C#
int IndexOneValue = numberList[1];
```
<br />

加入數值: <br />
```C#
int newNumber = 5;
numberList.Add(newNumber);
```
<br />

移除最後一個數值: <br />
```C#
int indexToRemove = numberList.Count - 1;
numberList.RemoveAt(indexToRemove);
```
<br />

## Queue

先進先出(FIFO), 排隊的概念，先來的就可以先處理先離開 <br />
自帶功能: Dequeue()取第一個值並將整個陣列數量-1並向前遞補index內容<br />

初始化: <br />
```C#
Queue<int> numbersQueue = new Queue<int>();
numbersQueue.Enqueue(1);
numbersQueue.Enqueue(2);
numbersQueue.Enqueue(3);
numbersQueue.Enqueue(4);
```
<br />

取得第一個數值但不刪除(偷瞄一眼): <br />
```C#
int peekValue = numbersQueue.Peek();
```
<br />

取得第一個數值且刪除: <br />
```C#
int dequeueValue = numbersQueue.Dequeue();
```
<br />

加入數值: <br />
```C#
int newNumber = 5;
numbersQueue.Enqueue(newNumber);
```
<br />

## Stack

後進先出(LIFO), 河內塔的概念，最後放的會最先被拿出來 <br />
自帶功能: 陣列內容顛倒，後加的數值會塞到index 0的位置，Pop()取最後一個值並將整個陣列數量-1<br />

初始化: <br />
```C#
Stack<int> numbersQueue = new Stack<int>();
numbersQueue.Push(1);
numbersQueue.Push(2);
numbersQueue.Push(3);
numbersQueue.Push(4);
```
<br />

取得最後一個數值但不刪除(偷瞄一眼): <br />
```C#
int peekValue = numbersQueue.Peek();
```
<br />

取得最後一個數值且刪除: <br />
```C#
int dequeueValue = numbersQueue.Pop();
```
<br />

加入數值: <br />
```C#
int newNumber = 5;
numbersQueue.Push(newNumber);
```
<br />

----
[C# 系統集合泛型(System.Collections.Generic) Queue, Stack](https://hoohoo.top/blog/c-generic-systems-collection-system-collections-generic-queue-stack-teaching-notes-using-visual-studio/)
<br />
[Mircrosoft官方文件: List類別](https://learn.microsoft.com/zh-tw/dotnet/api/system.collections.generic.list-1?view=net-7.0)
<br />
[Mircrosoft官方文件: Queue類別](https://learn.microsoft.com/zh-tw/dotnet/api/system.collections.generic.queue-1?view=net-7.0)
<br />
[Mircrosoft官方文件: Stack類別](https://learn.microsoft.com/zh-tw/dotnet/api/system.collections.generic.stack-1?view=net-7.0)

