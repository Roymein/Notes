# VBA 编程指南

## 基础语法

### 条件判断语句

##### 1、IF...Then 选择语句

> 用于解决多次判断问题。

格式：

```
If condition Then
	expression
ElseIf condition Then
	...
Else
	condition
End If
```

类似于“如果... 那么...否则”



##### 2、Select Case 语句

> 当判断条件过多时，If...Then 语句变的更长，可读性很差。

格式：

```
Select Case Condition
	Case condition1
		expression
	Case condition2
		expression
	...
	Case Else
		...
End Select
```



### 循环控制语句

> If ... Then 语句 及 Select Case 语句单条数据有多个判断条件时，需要做出判断时使用，但是当有多条数据时，无法处理多条数据  

##### 1、For ... Next 循环语句

格式

```
For <循环变量> =  <初值>To<终值> [Step 步长值]   //括号内的参数可选，默认步长为1
	<循环体>
	[Exit For]  //可以在循环体中任意处加入一句或多聚Exit For，当遇到这个语句时，退出For循环，执行Nex后的语句
Next [循环变量]  // 循环变量名可省略 
```

example

```
For i = 1 To 20 Step 1
	Select Case Cells(i,"A")
		Case conditiion1
			expression
		Case condition2
			expression
		...
	End Select
Next i
```



##### 2、Do While 语句

> 工作表中的记录随时都在变，不知道有多少条记录，不确定循环变量的终值时使用

开头判断式格式

```
Do [While 逻辑表达式]    //循环条件，当逻辑表达式之为True时，执行Do 及Loop之间的语句，否则执行Loop后的语句
	<循环体>
	<Exit Do>       //将跳出循环，执行Loop后的语句
	[循环体]
Loop				// 标志Do While 语句结束，返回Do语句出，再次判断循环条件
```



结尾判断式格式

```
Do    
	<循环体>
	<Exit Do>       //将跳出循环，执行Loop后的语句
	[循环体]
Loop [While 逻辑表达式]
```

结尾判断的语句是在执行一次循环体后在判断循环条件，因此，当循环条件一开始就是False时，比开头判断式要多执行一次循环体，其他时候执行次数相同



##### 3、Do Until 语句

开头判断式格式

```
Do [Until 逻辑表达式]  //如果逻辑表达式的值为False，则执行循环体部分，否则执行Loop后的语句
	<循环体>
	[Exit Do]
	[循环体]
Loop
```

结尾判断式格式

```
Do 
	<循环体>
	[Exit Do]
	[循环体]
Loop [Until 逻辑表达式]  //如果逻辑表达式的值为False，则返回到Do语句出，否则执行Loop后的语句
```



**与Do While 的区别**

Do While语句式当逻辑表达式的只为False时推出循环，而Do Until语句是当逻辑表达式的值为True时推出循环，两者区别是退出条件不同



##### 4、For Each ... Next 语句

格式

```
For Each 元素变量 In 集合名称或数组名称 //如果是集合，元素变量定义为相同的对象类型，是数据，元素变量定义为Variant类型
	<语句块>
	[Exit For]
		[语句块]
Next [元素变量]		
// 元素变量用来遍历集合或数据中的每个元素，无论集合或数组里有多少个元素，总是从第一个元素开始，知道最后一个，然后推出循环
```



example

```
Sub sheet()

    Dim sheet As Worksheet, i As Integer
    i = 1
    For Each sheet In Worksheets
        Cells(i, "A") = sheet.Name   //sheet.Name 返回工作表的标签名称
        i = i + 1
    Next sheet
End Sub
```

```
    Dim c As Range, i As Integer
    i = 1
    For Each c In Range("A1:A8")
        c.Value = i
        i = i + 1
    Next
```



##### 5、跳转语句 GoTo

> 让程序跳转到另一条语句执行
>
> 在VBA中，指定地点可以在目标代码所在行前加上一个带冒号的字符串或不带冒号的数字作为标签，然后在GoTo的后面写上标签名
>
> 通常 GoTo 语句会影响程序结构，不建议使用



##### 6、With 语句

当需要对相同的对象进行多次操作时，往往会编写一些重复的代码，With语句可对其进行优化

如：

```
    Worksheets("sheet1").Range("A1").Font.Nmae = "仿宋"
    Worksheets("sheet1").Range("A1").Font.Size = 12
    Worksheets("sheet1").Range("A1").Font.Bold = True
    Worksheets("sheet1").Range("A1").Font.ColorIndex = 3
```

使用With 优化之后

```
    With Worksheets("sheet1").Range("A1").Font
        .Name = ""
        .Size = 12
        .Bold = True
        .ColorIndex = 3
    End With
```



## Sub 过程，基本的程序单元

### VBA过程

> 过程：做一件事情的经过，由不同的操作按照先后顺序排列，组合起来
>
> VBA过程： 就是完成某个给定任务的代码的有序组合。如录入数据的过程，需要打开工作簿，输入数据，保存工作簿，保存工作簿，退出Excel程序，将这些操作写成VBA代码，按先后顺序组合起来就是一个VBA过程。

#### 分类

- Function 过程 （自定义函数） 

- Sub过程



#### Sub过程

录制的宏就是 Sub 过程，录制宏业只能生成Sub过程。

首先，先了解录制宏生成Sub过程，复制A1：A8 并将复制的内容粘贴至C列，将录制的代码打开，

```
Sub copyAndPaste()              // copyAndPaste 是过程的名称，过程以 Sub 过程名() 开始
'
' copyAndPaste 宏
'
    Range("A1:A8").Select
    Selection.copy			  // 中间部分为处理数据的过程，一个过程可以有任意多的代码
    Range("C1").Select
    ActiveSheet.Paste
End Sub						//所有的Sub 过程都是以End Sub结束
```

##### 过程写在何处？

宏保存在模块，过程也是保存在模块里。和录制宏一样，过程保存在模块里，所以编写过程，应先插入一个模块来保存。在录制多个宏的时候可以发现，所有的代码都写在一个模块对象中，实际上，这样是十分不利于管理，正确的实践应该是建立不同的模块来保存功能不同的过程。

![image-20210327234438730](C:\Users\Roymein\AppData\Roaming\Typora\typora-user-images\image-20210327234438730.png)



模块化的好处是，可以在其他模块中引用需要使用的模块即可，做到代码多次使用，以以上编写Sub过程copyAndPaste ，若想在另一个过程模块，即在其他的Sub过程中使用，常用的使用方法有三种

- 方法一：输入过程名称以及参数，参数用逗号隔开

过程名[参数1，参数2...]

```
Sub test()
   copyAndPaste
End Sub
```

- 方法二：在过程名称以及参数前使用Call关键字，参数用括号括起来，并用括号隔开。

Call 过程名[（参数1，参数2...）]

```
Sub test()
	Call copyAndPaste
End Sub
```

- 方法三：利用Application对象的Run方法，语句形式如下。

Application.Run 过程名的字符串(或字符串变量) [，参数1，参数2...]

```
Sub test()
	Application.Run "copyAndPaste"	//表示过程名的字符串，必须加上引号
End Sub
```



##### 过程的作用域

###### 公共过程

意味这个过程所有过程都可以使用

```
Public Sub gc()
	MsgBox "公共过程"
End Sub
```

其中Public 可省略



###### 私有过程

声明私有过程，必须使用Private语句，其他模块里的过程不能调用私有过程。

```
Private Sub gc()
End Sub
```

如果想把所有的过程全部声明为私有过程（包括已经声明为公共过程的过程），只需要在模块中的第一个过程之前写上

```
Option Private Module
```

声明了该语句，不管使用什么语句声明过程，他们都是私有过程。



谁有资格调私有过程？

如果一个过程被声明为私有过程，只有这个模块里的过程才能调用它，如果想让其他模块也能调用，应该把它声明为公共过程，私有过程是不会在宏对话框中显示



#### Function过程

Funtion过程称为函数过程。编写一个Function过程，就是编写了一个函数，类似于数学中的函数y=f(x)，在VBA中，Function过程本质上就是编写一个f(x) 函数，这个函数用来处理特定的问题，函数可以完成很多复杂的计算，如excel中的求和 可以用SUM函数；excel中的求和函数只能求一列或一行数据的和，但是想统计一个表中所有黄色底纹的单元格，这种该如何处理？

此时，可通过编写Function过程来自定义函数。

```
Public Function Fun()
	Fun = Int(Rnd() *10) +1   // Rnd函数生成0~1之间的一个随机数，过程结束前们必须将计算结果赋值给过程名称
End Function
```

自定义的函数可以在excel 中的[插入函数]对话框中的用户定义中找到，且自定义的函数可以和其他函数嵌套使用。

##### 创建Function

<img src="C:\Users\Roymein\AppData\Roaming\Typora\typora-user-images\image-20210328003012155.png" alt="image-20210328003012155" style="zoom:67%;" />

<img src="C:\Users\Roymein\AppData\Roaming\Typora\typora-user-images\image-20210328003044537.png" alt="image-20210328003044537" style="zoom:67%;" />

##### 声明函数过程

```
[Public][Private][Static] Function 函数名 ([参数列表]) [As 数据类型]   //【As 数据类型】函数返回值的数据类型，可选参数
	[语句块]
	[函数名=过程结果]
	[Exit Function]
	[语句块]
	[函数名=过程结果]
End Function
```

当一条语句过长时，可以在句子的后面输入一个空格和下划线，然后换行，把一行代码分成两行



## 常用对象

### VBA常用对象

| 对象        | 说明                                                         |
| ----------- | ------------------------------------------------------------ |
| Application | 代表Excel应用程序                                            |
| Workbook    | 代表Excel中的工作簿，一个Workbook对象代表一个工作簿文件      |
| Worksheet   | 代表Excel中的工作簿，一个Worksheet对象代表工作簿里的一张普通工作表 |
| Range       | 代表Excel中的单元格，可以时单个单元格，也可以时单元格区域    |

如：

```
Application.Worksheets("Sheet1").Range("A1").Value =100
```

#### ScreenUpdating属性

```
Application.ScreenUpdating = False  //关闭屏幕更新，不显示计算结果到屏幕上
```

#### DisplayAlerts属性

> DisplayAlerts属性决定在程序中是否显示警告信息，默认为True，如果不想在程序中运行时被提示和警告信息打扰，可将其设置为False

```
Dim sht As Worksheet
Application.DisplayAlerts = False   //不显示警告信息
For Each sht in Worksheeets
	If sht.Name <> ActiveSheet.Name Then 
		sht.Delete
	End If
Next
Application.DisplayAlerts = True
```

#### EnableEvents属性

启用或禁用事件，事件是能被Excel认识的一个操作动作，Excel中许多操作都会触发事件，如打开工作簿，关闭工作簿等。用户可以编写不同的代码来想要这些事件，当触发某个事件时，自动执行指定的代码。

#### WorksheetFunction属性

通过调用WorksheetFunction属性调用部分工作表函数。



### 工作簿相关操作

#### 创建工作簿

```
Workbooks.Add				//创建包含一定数目空白工作表的新工作簿
Workbooks.Add "D:\test.xls"
```

![image-20210329220144678](C:\Users\Roymein\AppData\Roaming\Typora\typora-user-images\image-20210329220144678.png)

- 工作表 xWBATWorksheet
- 图表     xlWBATChart
- 宏表     xlWBATExcel4MacroSheet
- 对话框xlWBATExcel4IntMacroSheet



#### 打开工作簿

```
Workbooks.Open FileName:="D:\test.xls"
FileName为参数名称，与参数值之间用 ":=" 连接，也可以用以下表示
Workbooks.Open "D:\test.xls"
```



#### 激活工作簿

打开了多个工作簿文件，但同一时间只能有一个窗口是活动的，一下调用可激活一个工作簿

```
Workbooks("book1").Activate
```

激活该工作簿后，原活动窗口将自动转为不活动窗口



#### 保存工作簿

```
ThisWorkbook.Save	//保存代码所在的工作簿
```

如果想将文件另存为一个新的文件，通过SaveAs方法。

```
ThisWorkbook.SaveAs "d:\test.xlsx"
```

使用SaveAs方法之后，将自动关闭源文件，打开新文件，若希望继续保留源文件不打开新文件，可以用SaceCopyAs方法。

```
ThisWorkbook.SaceCopyAs "d:\test.xlsx"
```



#### 关闭工作簿

```
Workbooks.Close		//关闭所有打开的工作簿
Workbooks("test").Close		//关闭指定的某个工作簿
```

若工作簿更改过但是未保存，关闭工作簿前会询问用户是否保存更改，若不想显示该对话框，可以给Close设置参数：

```
Workbooks("test").Close	savechanges:=True
```



    Dim wb As Workbook, sht As Worksheet
    Set wb = Workbooks.Add
    Set sht = wb.Worksheets(1)
    With sht
            .Name = "花名册"
            .Range("A1:F1") = Array("序号", "姓名", "性别", "出生年月", "参加工作时间", "备注")
    End With
    wb.SaveAs ThisWorkbook.Path & "\员工花名册.xlsx"
    ActiveWorkbook.Close
#### 判断工作表是否打开

```
    Dim i As Integer
    For i = 1 To Workbooks.Count
        If Workbooks(i).Name = "花名册.xlsx" Then
            Exit Sub
        End If
    Next
```

```
    Dim file As String
    file = ThisWorkbook.Path & "\员工花名册.xlsx"
    If Len(Dir(file)) > 0 Then
        MsgBox "exit"
    Else
        MsgBox "not exit"
    End If
    
    //Dir 返回文件名，否则返回空字符串""
```

向未打开的工作簿中录入数据

```

```



### Worksheet对象

#### Worksheet概述

一个Worksheet对象代表一张普通工作表，Worksheets是多个Worksheet对象的集合，包含指定工作簿中所有的Worksheet对象。

```
Worksheets.Count 返回工作表的数量
Worksheet.Name  工作表的标签名称
```



#### 引用工作表

![image-20210329222315370](C:\Users\Roymein\AppData\Roaming\Typora\typora-user-images\image-20210329222315370.png)

Sheet3是第一个工作表的标签名称，Sheet1是第三张工作表，所有索引号为3。

```
Worksheets.Item(1)			 '引用工作表中的第一张表'
Worksheets(1)				'引用工作表中的第一张表'
Worksheets("Sheet1")		 '引用工作表中标签为'Sheet1'的工作表
```



#### 创建工作表

```
Worksheets.Add 	'不带参数将在活动工作表前创建一个新表'

指定位置：
Worksheets.Add before:=Worksheets(1)
Worksheets.Add after:=Worksheets(1)

插入多张：
Worksheets.Add Count:=3
```

如在第一张表后插入三张表：

```
Worksheets.Add after:=Worksheets(1),Count:=3
```

指定添加工作表的名称

```
Worksheets.Add(before:=Worksheets(1)).Name = "test"
```



#### 删除工作表

```
Worksheets("Sheet1").Delete
```

可以在删除工作表的前后加上 DisplayAlerts属性取消警告弹窗提示

```
Application.DisplayAleerts = False
```



#### 激活工作表

让未处于活动状态的工作表变为活动工作表，激活工作表可以用Activate方法和Select方法

```
Worksheets(1).Select
```

Activate方法与Select方法的区别：

工作表隐藏时，Select会出错，Select可同时选中未隐藏的多张工作表。



#### 复制工作表

```
Worksheets("Sheet1").Copy      //excel自动为新的工作表命名
Worksheets("Sheet1").Copy after:=Worksheets("Sheet2")
```

若不使用参数，则默认将工作表复制到新工作簿中,名称与原来相同。复制工作表后，复制的工作表变为活动的工作表（工作簿同理），因此根据这条特点，可以修改新的工作表的相关属性，

```
Worksheets("Sheet1").Copy after:=Worksheets("Sheet2")
ActiveSheet.Name = "new name"
```



#### 移动工作表

```
Worksheets("Sheet1").Move
Worksheets("Sheet1").Move after:=Worksheets("Sheet2")
```

#### 选中已使用的单元格区域

```
Worksheets("Sheet1").UsedRange.Select  	//返回工作表中已使用的单元格围成的矩形区域
```



### Range对象

#### 引用多个不连续区域

```
Range("A1:A10,A4:E6,C3:D9").Select
```

#### 引用相交区域

```
Range("A1:A10 A4:E6 C3:D9").Select
```

#### 引用两个区域围成的矩形区域

```
Range("A1:A10","A4:E6","C3:D9").Select
```

#### 引用整行

```
ActiveSheet.Rows(3,3).Select   活动工作表中的第三行
ActiveSheet.Rows.Select			选中所有行
```

#### 引用整列

```
ActiveSheet.Columns("F:G").Select     选中活动工作表的F到G列
ActiveSheet.Columns(6).Select
ActiveSheet.Columns.Select
```

```
Union方法剋有将不连续的多个单元格区域粘在一起，可以同时对其进行操作。
```



```
Range("A1").Offset(2,3).Value = 500   //从A1单元格出发，向下移动2行，在向右移动三行

Range("A1").Resize(5,4).Select       //resize将扩大或缩小指定的单元格区域，得到一个新的单元格区域。

Range("A1").CurrentRegion.Select	//CurrentRegion属性返回当前区域，即以空行和空列的组合为边界的区域、

Range("A1").CurrentRegion.Rows.Count //当前选中单元格已使用的行数
```

```
获取单元格地址
Selection.Address    //Selection对当前活动工作表中选中对象的引用
```



### 单元格

#### 清除单元格

```
Range("A1").Clear
Range("A1").ClearComments
Range("A1").ClearContents
Range("A1").ClearFormats
```

#### 复制、粘贴单元格

```
Range("A1").Select
Selection.Copy
Ranage("D1").Select
ActiveSheet.Paste
简化为：
Range("A1").Copy Destination:=Range("C1")       //C1是目标单元格
```

#### 剪切单元格

```
Range("A1").Cut
```

#### 删除单元格

```
Rnage("B5").Delete Shift:=xlToLeft       //xlUp 上移   xlToLeft 左移
Rnage("B5").EntireRow.Delete             //删除整行
Rnage("B5").EntireColumn.Delete			//删除整列
```

删除单元格后下方的单元格默认上移

#### 设置字体

Font对象(字体)，决定表格里的内容以什么样的姿势表现出来。

```
With Range("A1:L1").Font
	.Name="宋体"
	.Size=12
	.Color=RGB(255,0,0)
	.Bold = True
	.Italic =True
	.Underline = xlUnderlineStyleDouble
End With
```

#### 设置边框

```
With Range("A1").CurrentRegion.Borders
	.LineStyle = xlContinuous
	.Color=RGB(0,0,255)
	.Weight=xlHairline
End With
```





### 批注

> 单元格的批注类似于标签，对单元格作注释或说明，并不影响单元格内的数值，也不参与或影响计算。

#### 批注

```
Range("B5").AddComment Text:=" annotation"
```

只能为无批注的单元格添加批注，为含批注的单元格加批注时程序会出错。

```
If Range("B5").Comment Is Nothing Then 
	...
End If
```

```
Range("B5").Comment.Text = ""   
Range("B5").Comment.Visible = False	//隐藏批注
Range("B5").Comment.Delete	//删除批注
```

