---
title: C++ & WPF
date: 2017-4-9
update: 2017-4-9
tag: [C++,Windows Coding]
category: [Coder]
---

# C++ & WPF

> 用C++开发WPF程序，主要就是在MFC窗体中Host WPF的Page。

## 创建工程，最好使用空工程

1. 打开VS，创建`Win32控制台程序`

2. 选择使用 `控制台程序`和`MFC`

3. 将其修改为`MFC Windows程序`

   - 删除cpp,h文件
   - 添加`CWinApp`派生类
   - 添加`CWnd`派生类
   - 将工程属性的`SubSystem`改为`Windows`。具体位置在`链接器`-`系统`

   **现在已经可以编译了**，当然还不能运行

4. 在`CWnd`派生类中创建窗口函数

   ```c++
   BOOL CWnd_Class_Name::CreateMainWnd(const CRect &rect,DWORD dwStyle,DWORD dwStyleEx)
   {
     WNDCLASS wndClass;
     memset(&wndClass,0,sizeof(WNDCLASS));
     wndClass.style = CS_HREDRAW | CS_VREDRAW | CS_DBLCLKS;
     wndClass.lpfnWndProc = ::DefWindowProc;
     wndClass.hInstance = AfxGetInstanceHandle();
     wndClass.hIcon = NULL;
     wndClass.hCursor = ::LoadCursor(NULL, IDC_ARROW);
     wndClass.hbrBackground = (HBRUSH)(COLOR_WINDOW);
     wndClass.lpszMenuName = NULL;
     wndClass.lpszClassName = _T("__CWnd_Class_Name__");
     if(!AfxRegisterClass(&wndClass))
     {
     	return FALSE;
     }
     return CWnd::CreateEx(dwStyleEx, wndClass.lpszClassName, _T("C++ && WPF"), dwStyle, 0, 0, rect.Width(), rect.Height(), NULL, NULL); 
   }
   ```

5. 创建`PostNcDestroy`善后

   ```c++
   BOOL CWnd_Class_Name::PostNcDestory()
   {
     delete this;
   }
   ```

6. 修改`CWinAPP`派生类

   - 构造函数改为`public`

   - 定义`theApp`

   - 重载`InitInstance()`

     ```c++
     BOOL CCPlusPlus_WPFApp::InitInstance()
     {
         CWinApp::InitInstance();
         CCPlusPlus_WPFMainWnd *pMainWnd = new CCPlusPlus_WPFMainWnd();
         if (!pMainWnd->CreateMainWnd(CRect(0, 0, 800, 600), WS_OVERLAPPEDWINDOW, 0))
             return FALSE;
         m_pMainWnd = pMainWnd;
         pMainWnd->CenterWindow();
         pMainWnd->ShowWindow(SW_SHOW);
         pMainWnd->UpdateWindow();
         return TRUE;
     }
     ```

## 修改工程，使支持WPF

1. 修改工程属性：`常规`-`CLR` 选择`\clr`

2. 添加References：`通用选项`-`References`

   ​