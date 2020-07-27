---
title: Windows程序设计
date: 2020-06-10 13:07:05
tags: Windows
categories: Windows
---
《Windows程序设计》学习，漫长的过程，加油冲冲冲！~
<!-- more -->
# 第一章 第一个Windows程序
## Windows简单程序
```
# include <windows.h>
int WINAPI WinMain(HINSTANCE hInstance, HINSTANCE hPrevInstance, PSTR szCmdLine, int iCmdShow)
{
    MessageBox(NULL, TEXT ("Hello, Windows 98!"), TEXT ("helloMsg"), 0);

    return 0;
}
```
## 头文件
头文件`# include <windows.h>`
1. 囊括了其他若干Windows头文件
2. 头文件定义了Windows中所有的数据类型、函数调用、数据结构及常量标识符
3. 至关重要

## 程序入口
`WinMain`
总是以下面的方式出现：
`int WINAPI WinMain(HINSTANCE hInstance, HINSTANCE hPrevInstance, PSTR szCmdLine, int iCmdShow)`
在WINBASE.H中声明如下：
```
int
WINAPI
WinMain{
    HINSTANCE hInstance,
    HINSTANCE hPrevInstance,
    LPSTR lpCmdLine,
    int nShowCmd
};
```
`LPSTR`和`PSTR`这两种数据类型在`WINNT.H`中都声明为字符串的指针。前缀`LP`代表长指针(Long Pointer)，16位Windows的产物。
前缀`i`表示`int`，前缀`sz`表示“以零结尾的字符串”

**WinMain参数**
第一个参数：实例句柄（用来标识某些东西）
第二个参数：NULL（通常）
第三个参数：用来运行程序的命令行
第四个参数：指明程序最初如何显示
## MessageBox函数
显示短信息
第一个参数：窗口句柄
第二个参数：将要在信息框里出现的文本字符串
第三个参数：标题栏上显示的文本字符串
第四个参数：以前缀MB_打头的常量组合
## 编译、链接及运行
编译阶段，编译器从C源代码文件产生一个.obj(目标)文件
链接阶段，连接器把.obj文件和。LIB(库程序)文件放到一起产生.exe(可执行)文件

# 第二章 Unicode简介
## 字符集简史
## 宽字符和C语言
### char数据类型
`char c='A';`变量c需要一个字节的存储空间，用十六进制0x41来被初始化；
`char * p;`指针p需要4个字节的存储空间
`char * p="Hello!";`p任然需要四个字节的存储空间；字符串存储在**静态内存**中并使用7个字节的存储空间(6+1)
`char a[10];`编译器保留了十个字节的存储空间给这个数组，sizeof(a)==10；
定义在静态内存中的数组。全局变量`char a[]="Hello!";`，局部变量`static char a[]="Hello!";`

### 更宽的字符
宽字符是基于wchar_t数据类型的，其被定义在多个头文件中，包括WCHAR.H，如`typedef unsigned short wchar_t`
因此wchar_t数据类型和一个无符号短整型一样，为16位宽；
1. 定义一个包含单个宽字符的变量：`wchar_t c='A';`变量c是一个两个字节的值0x0041(最低有效数字优先)
2. 定义一个已初始化的指向宽字符串的指针：`wchar_t *p=L"Hello!";'(大写的L紧邻左引号)，没个字符占用两个字节；指针变量p依旧占用4个字节，但是字符串占用14个字节(每个字符需要两个字节)
3. 定义一个宽字符数组：`static wchar_t a[]=L"Hello!";`sizeof(a)==14；a[1]的值是宽字符'e'或0x0065；
4. 注意：`L`很重要，它告诉编译器你想要的字符串用每个字符两个字节的方式存储

### 宽字符库函数
宽字符版本的strlen函数被称为wcslen("宽字符字符串长度")，并定义在STRING.H和WCHAR.H中。
strlen函数的声明：`size_t __cdecl strlen(const char*);`
wcslen函数的声明：`size_t __cdecl wcslen(const wchar_t*);`
所以确定宽字符串的长度：`iLength=wcslen(pw);`

### 维护一个源代码文件

## 宽字符和Windows
### Windows头文件的类型
### Windows函数调用
### Windows的字符串函数
ILength=lstrlen(pString);
pString=lstrcpy(pString1,pString2);
pString=lstrcpyn(pString1,pString2);
pString=lstrcat(pString1,pString2);
iComp=lstrcmp(pString1,pString2);
iComp=lstrcmpi(pString1,pString2);
以上与c运行库中函数功能对应，定义了UNICODE标识符后，接受宽字符，否则只接受常规字符串

### 在Windows中使用printf
sprintf函数定义：
int sprintf (char * szBuffer,const char * szFormat, ......);
第一个参数是字符缓冲区，第二个是格式字符串；该函数返回字符串的长度
Microsoft版的c语言运行库和Windows所支持的所有sprintf类函数
{% asset_img sprintf类函数.png%}
### 格式化的消息框
(兼容性问题导致课本代码无法运行)

# 第三章 窗口与消息
## 窗口的创建
CreateWindow函数
### 系统结构概述
窗口过程
### HELLOWIN程序
```
#include <Windows.h>
#pragma comment(lib, "winmm")
LRESULT CALLBACK WndProc (HWND, UINT, WPARAM, LPARAM);

int WINAPI WinMain(HINSTANCE hInstance, HINSTANCE hPrevInstance, PSTR szCmdLine, int iCmdShow)
{
	static TCHAR szAppName[] = TEXT("HelloWin");
	HWND hwnd;
	MSG msg;
	WNDCLASS wndclass;
	wndclass.style = CS_HREDRAW | CS_VREDRAW;
	wndclass.lpfnWndProc = WndProc;
	wndclass.cbClsExtra = 0;
	wndclass.cbWndExtra = 0;
	wndclass.hInstance = hInstance;
	wndclass.hIcon = LoadIcon(NULL, IDI_APPLICATION);
	wndclass.hCursor = LoadCursor(NULL, IDC_ARROW);
	wndclass.hbrBackground = (HBRUSH)GetStockObject(WHITE_BRUSH);
	wndclass.lpszMenuName = NULL;
	wndclass.lpszClassName = szAppName;

	if (!RegisterClass(&wndclass)) {
		MessageBox(NULL, TEXT("This program requires windows NT!"), szAppName, MB_ICONERROR);
		return 0;
	}

	hwnd = CreateWindow(szAppName,
		TEXT("The Hello Program"),
		WS_OVERLAPPEDWINDOW,
		CW_USEDEFAULT,
		CW_USEDEFAULT,
		CW_USEDEFAULT,
		CW_USEDEFAULT,
		NULL,
		NULL,
		hInstance,
		NULL);

	ShowWindow(hwnd, iCmdShow);
	UpdateWindow(hwnd);

	while (GetMessage(&msg, NULL, 0, 0))
	{
		TranslateMessage(&msg);
		DispatchMessageW(&msg);
	}
	return msg.wParam;
}

LRESULT CALLBACK WndProc(HWND hwnd, UINT message, WPARAM wParam, LPARAM lParam)
{
	HDC hdc;
	PAINTSTRUCT ps;
	RECT rect;

	switch (message)
	{
	case WM_CREATE:
		PlaySound(TEXT("hellowin.wav"), NULL, SND_FILENAME | SND_ASYNC);
		return 0;

	case WM_PAINT:
		hdc = BeginPaint(hwnd, &ps);
		GetClientRect(hwnd, &rect);
		DrawText(hdc, TEXT("Hello, Windows 98!"), -1, &rect, DT_SINGLELINE | DT_CENTER | DT_VCENTER);
		EndPaint(hwnd, &ps);
		return 0;

	case WM_DESTROY:
		PostQuitMessage(0);
		return 0;

	}
	
	return DefWindowProc(hwnd, message, wParam, lParam);
}
```
### 通盘考虑
#### Windows函数调用
#### 大写标识符
在Windows头文件中定义的
很多以两个或三个字母作为前缀，其后紧跟一个下划线
#### 新数据类型
#### 理解句柄
#### 匈牙利标记法
前缀`sz`表示“以0结束的字符串”
前缀`h`表示“句柄”
前缀`i`表示“整型”
前缀`lpfn`表示“指向函数的长指针”
前缀`cb`表示“字节数”
前缀`hbr`表示“画刷的句柄”
前缀`lpsz`表示“指向以零结束的字符串的长指针”
前缀`IDI`表示“图标的标识符”
前缀`hbr`表示“画刷的句柄”

### 窗口类的注册
在创建应用程序窗口之前，必须调用函数`RegisterClass`来注册窗口类；该函数只需要一个参数，即一个指向WNDCLASS(HELLOWIN.C程序)类型的结构的指针；
```
#include <Windows.h>
#pragma comment(lib, "winmm")
LRESULT CALLBACK WndProc (HWND, UINT, WPARAM, LPARAM);

int WINAPI WinMain(HINSTANCE hInstance, HINSTANCE hPrevInstance, PSTR szCmdLine, int iCmdShow)
{
	static TCHAR szAppName[] = TEXT("HelloWin");
	HWND hwnd;
	MSG msg;
	WNDCLASS wndclass;   //定义WNDCLASS类型的结构，10个字段
	wndclass.style = CS_HREDRAW | CS_VREDRAW;  //32位“类风格”的标识符
	wndclass.lpfnWndProc = WndProc;  //用于基于该窗口的窗口过程的地址；处理传递给所有基于该窗口类创建的窗口的所有消息。引用的实际上是指向函数的指针
	wndclass.cbClsExtra = 0;  //应用程序根据需要来使用这些额外空间
	wndclass.cbWndExtra = 0;
	wndclass.hInstance = hInstance;  //实例句柄
	wndclass.hIcon = LoadIcon(NULL, IDI_APPLICATION);  //为所有基于该窗口类的窗口设定一个图标
	//为了获取预定义图标的句柄，需要调用函数LoadIcon，并将函数的第一个参数设置为NULL；而从保存在磁盘中的应用程序的可执行文件中加载自定义图标时，该参数必须设定为hInstance
	//第二个参数用于标识该图标
	wndclass.hCursor = LoadCursor(NULL, IDC_ARROW);//载入一个预定义的鼠标指针(IDC_ARROW)，并返回指针的句柄
	wndclass.hbrBackground = (HBRUSH)GetStockObject(WHITE_BRUSH);//这类窗口的客户区指定背景色
	wndclass.lpszMenuName = NULL;//窗口类的菜单
	wndclass.lpszClassName = szAppName;  //窗口类的名称，允许用户任意命名。只创建一个窗口时，窗口类的名称通常与程序名相同

	if (!RegisterClass(&wndclass)) {  //检测错误，如果定义了UNICODE标识符，程序调用RegisterClassW；Windows98下运行，RegisterClassW没有真正实现；
		MessageBox(NULL, TEXT("This program requires windows NT!"), szAppName, MB_ICONERROR);
		return 0;
	}

	hwnd = CreateWindow(szAppName,
		TEXT("The Hello Program"),
		WS_OVERLAPPEDWINDOW,
		CW_USEDEFAULT,
		CW_USEDEFAULT,
		CW_USEDEFAULT,
		CW_USEDEFAULT,
		NULL,
		NULL,
		hInstance,
		NULL);

	ShowWindow(hwnd, iCmdShow);
	UpdateWindow(hwnd);

	while (GetMessage(&msg, NULL, 0, 0))
	{
		TranslateMessage(&msg);
		DispatchMessageW(&msg);
	}
	return msg.wParam;
}

LRESULT CALLBACK WndProc(HWND hwnd, UINT message, WPARAM wParam, LPARAM lParam)
{
	HDC hdc;
	PAINTSTRUCT ps;
	RECT rect;

	switch (message)
	{
	case WM_CREATE:
		PlaySound(TEXT("hellowin.wav"), NULL, SND_FILENAME | SND_ASYNC);
		return 0;

	case WM_PAINT:
		hdc = BeginPaint(hwnd, &ps);
		GetClientRect(hwnd, &rect);
		DrawText(hdc, TEXT("Hello, Windows 98!"), -1, &rect, DT_SINGLELINE | DT_CENTER | DT_VCENTER);
		EndPaint(hwnd, &ps);
		return 0;

	case WM_DESTROY:
		PostQuitMessage(0);
		return 0;

	}
	
	return DefWindowProc(hwnd, message, wParam, lParam);
}
```

### 窗口的创建
基于同一窗口类可以创建许多不同的窗口；
理解各个参数
{% asset_img CreatWindow.png%}


### 窗口的显示
CreateWindow调用返回，窗口已在Windows内部被创建。Windows已经分配了一块内存来保存CreateWindow已经分配了一块内存来保存CreateWindow调用中指定的窗口信息以及其他一些信息
Windows可通过窗口句柄来获取这些信息。
将窗口显示在屏幕上，需要调用另外两个函数：
 `ShowWindow(hwnd, iCmdShow);`
第一个参数是指向有CreateWindow所创建的窗口句柄；
第二个参数是WindMain函数所接受的iCmdShow值；该参数决定着窗口在屏幕中的初始显示形式(正常、最小化、最大化)
```
WinMain传给ShowWindow的参数值：
正常：SW_SHOWNORMAL（窗口的客户区将被窗口类中所指定的背景画刷擦除，`UpadteWindow(hwnd)`使窗口客户区重绘）
最大化：SW_SHOWMAXIMIZED
显示在任务栏：SW_SHOWMINNOACTIVE
```

### 消息循环
开启消息循环的GetMessage函数用于从消息队列中对消息进行检索
`GetMessage(&msg,NULL,0,0)`
将一个指向名称为msg的MSG结构变量的指针传给Windows
第二三四个参数分别设置为NULL或0，表明该程序希望获取由该程序所创建的窗口消息
```
hwnd：消息所指向的窗口句柄。
message：消息标识符，用于标识消息的数字。对于每条消息，在Windows的头文件中都为其定义了一个已WM为前缀的标识符
wParam：一个32位的“消息参数”，该参数的含义和取值决定于具体的信息
lParam：一个32位的“消息参数”，该参数的含义和取值决定于具体的信息
time：消息进入消息队列的时间
pt：消息进入消息队列中时鼠标指针的位置坐标
```

### 窗口过程
决定了窗口过程决定了窗口客户区的的显示内容以及窗口如何对用户的输入做出响应
窗口过程几乎由Windows自身调用
### 消息的处理
窗口过程所接受的每条消息都由一个数字来标识，即窗口过程的message参数
用DefWindowProc来对所有窗口过程没有处理的消息进行默认处理非常重要，否则其他的正常行为将无法进行


### 声音文件的播放
### WM_PAINT消息
当窗口的客户区的部分或全部“无效”且必须“更新”时，应用程序将得到此通知
对WM_PAINT消息的处理几乎总是从BeginPaint函数开始：`hdc=BeginPaint*hwnd,&ps);`，以调用EndPaint函数结束：`EndPaint(hwnd,&ps);`
第一个参数均为程序的窗口句柄，第二个参数均指向一个类型为PAINTSTRUCT结构的指针
### WM_DESTROY消息
PostQuitMessage(0);

## Windows编程中的若干难点
真正有价值的行为发生在窗口过程中
### 究竟是谁调用谁
### 队列消息和非队列消息
**队列消息**：由Windows放入程序的消息队列中的消息。(投递)。主要用用户的输入产生
**非队列消息**：Windows对窗口过程的直接调用产生的。(发送)。包括队列消息以外的其他所有消息。通常由调用特定的Windows函数引起。





# 第四章 文本输出
## 绘制和重绘
### WM_PAINT消息
大多数Windows程序在WinMain函数初始化过程中会在进入消息循环之前调用UpdateWondow函数。
窗口过程收到一条WM_PAINT消息：
```
用户移动一个窗口，导致原来被遮盖的部分窗口暴露出来；
用户调整窗口的大小；
程序调用ScrollWindow或ScrollDC函数滚动客户区；
程序调用InvalidateRect或InvalidateRgn函数显示生成WM_PAINT消息
```
发送一条WM_PAINT消息：
```
windows关闭了一个覆盖了部分窗口的对话框或消息框；
下拉菜单被拉下然后被收回；
显示提示信息
```
Windows保存被覆盖部分的显示内容，然后再恢复：
```
鼠标在客户区内移动
在客户区内拖动图标
```
程序保存所有用于绘制客户区的信息，但是只有在需要时才进行绘制--当收到WM_PAINT消息时。如果程序需要在其他时候更新客户区，可以强制Windows生成WM_PAINT消息。

### 有效矩形和无效矩形
窗口过程收到WM_PAINT消息时更新整个客户区，但通常只需要更新其中一个部分，最常见更新其中一个矩形区域。
需要重新绘制的部分被称作`无效区域`或`更新区域`。客户区中有一个无效区域将导致Windows在应用程序的消息低劣中放置一条WM_PAINT消息，只有程序客户区的一部分失效时，窗口过程才会接受到。

## GDI简介
绘制一个窗口的客户区需要调用Windows的图形设备接口(GDI)函数。
最常用的字符输出函数TextOut：`TextOut (hdc, x, y, psText, iLength);`，向窗口客户区输出一个字符串。`psText`指向字符串的指针；`iLength`是字符串的长度；`x`、`y`定义了字符串在客户区中的开始位置；`hdc`是设备环境句柄。

### 设备环境
句柄不过是一个数值，Windows内部用这个数值确定一个对象。
设备环境句柄是程序窗口使用GDI函数的“通行证”。
`设备环境DC`是GDI内部维护的一个数据结构。与特定显示设备相关联。
程序在绘制前必须获得一个设备环境句柄。获取句柄之后，Windows会在内部的设备环境结构中填入默认的属性值。绘制完成之后必须释放设备环境句柄，释放之后这个句柄不再有效且不能再被使用，so获取释放应该在处理同一条消息的过程中。

### 获取设备环境句柄：方法一
可以在处理WM_PAINT消息时使用，牵涉到两个函数：`BeginPaint`和`EndPaint`，而这两个函数需要两个参数：窗口句柄和类型为PAINTSTRUCT结构的变量(ps)的地址。
窗口过程在处理WM_PAINT消息时必须成对的调用BeginPaint和EndPaint。如果窗口过程不处理WM_PAINT消息，WM_PAINT消息就会被传送给Windows默认的窗口过程DefWindowProc。

#### 绘制信息结构
**PAINTSTRUCT**
```
typedef struct tagPAINTSTRUCT
{
	HDC hdc;               //设备环境句柄(BeginPaint函数的返回值)
	BOOL fErase;           //大多数情况设置为FALSE
	RECT rcPaint;           //定义无效矩形的边界，还是一个“裁剪”矩形
	BOOL fRestore;
	BYTE rgbReserved[32];
}PAINTSTRUCT;
```
### 获取设备环境句柄：方法二
调用`GetDC`函数来获得窗口客户区的设备环境句柄，使用完以后调用ReleaseDC函数释放；
```
hdc=GetDC(hwnd);
(使用GDI函数)
ReleaseDC(hend,hdc);
```
通常用于处理键盘消息或者鼠标消息
从GetDC返回的设备环境句柄中的裁剪矩形是整个客户区。不会将无效区域有效化；
`ValidateRect (hwnd,NULL);`将整个客户区有效化
GetWindowDC返回的是整个窗口的设备环境句柄；


### TEXTOUT函数详解
语法：`TextOut (hdc, x, y, psText, iLength);`
`hdc`设备环境句柄，既可以是GetDC返回的hdc值，也可以是处理WM_PAINT消息时从BeginPaint返回的hdc值；
`psText`指向字符串的指针；
`iLength`是字符串的长度；如果psText指向一个Unicode字符串，则字符串占用字节数将是iLength的两倍；
`x`、`y`定义了字符串在客户区中的开始位置；

### 系统字体

### 字符大小

### 文本尺寸的度量

### 文本的格式化

....好多不想看了


# 第五章 绘图基础
## GDI的结构
### GDI原理


