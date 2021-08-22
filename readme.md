# qt与matlab混合编程

## 需要用到的资源

* QT-creator安装包（**qt-opensource-windows-x86-5.12.2.exe**）
* vs2015（64位）离线安装包（**vs2015.2.com_chs.iso**）
* matlab2015b（64位）安装包（**matlab2015b**）

* Windows SDK安装包（**winsdksetup.exe**）
* matlab2015b运行环境安装包（**MCR_R2015b_win64_installer_2.exe**）



## 1. vs2015安装

安装vs2015过程中，我们只需要勾选vc++模块即可，笔者的安装路径为

> C:\Program Files (x86)\Microsoft Visual Studio 14.0

![image-20210820225351963](https://gitee.com/LucasXm/img/raw/master/img//image-20210820225351963.png)

## 2. QT-creator的安装

* 安装过程中会选择需要安装的插件，主要是要勾选**MSVC 2015 64bit插件**，笔者这里勾选的插件为

  > MSVC 2015 64 bit
  >
  > MinGW 32bit
  >
  > MinGW 64bit
  >
  > UWP ARMv7 (MSVC 2015)
  >
  > UWP x86 (MSVC 2015)
  >
  > UWP x64 (MSVC 2015)
  >
  > 以及其他全部的小组件

## 3. 安装Windows SDK

默认安装即可

![image-20210820233328787](https://gitee.com/LucasXm/img/raw/master/img//image-20210820233328787.png)

## 4. 对QT进行配置

* 打开QT，在工具->选项->Kits->Debuggers选项卡下，将SDK Debugger路径配置好，如下图所示

  ![image-20210822214333633](https://gitee.com/LucasXm/img/raw/master/img//image-20210822214333633.png)

* 在工具->选项->Kits->构建套件选项卡下，配置调试器如下图

  ![image-20210822214516720](https://gitee.com/LucasXm/img/raw/master/img//image-20210822214516720.png)

* 完成上诉两个步骤之后，进行编译，可能报错 **rc.exe找不到**，解决方法如下

  * 在Windows SDK安装路径下（C:\Program Files (x86)\Windows Kits\10\bin\10.0.16299.0）找到**rc.exe、rcdll.dll**到QT MSVC 安装目录下（C:\Qt\Qt5.12.2\5.12.2\msvc2015_64\bin）如下图所示

    ![image-20210822214949880](https://gitee.com/LucasXm/img/raw/master/img//image-20210822214949880.png)

    ![image-20210822215307004](https://gitee.com/LucasXm/img/raw/master/img//image-20210822215307004.png)

* 完成上述步骤后再编译，可能会报错 **运行 rc.exe 期间出错**，解决方法如下

  * 在工具->选项->构建和运行选项卡下，去掉勾选”**使用jom代替nmake**“

    ![image-20210822215507370](https://gitee.com/LucasXm/img/raw/master/img//image-20210822215507370.png)

  * 再次编译会出现如下报错

    ![img](https://gitee.com/LucasXm/img/raw/master/img//20200320102125179.png)

  * 解决方法就是在Windows SDK安装路径下（C:\Program Files (x86)\Windows Kits\10\bin\10.0.16299.0）找到**rc.exe、rcdll.dll** 拷贝到上诉路径中

* 再次编译后，就可以正常运行了。

## 5. 安装matlab2015b

默认安装即可

详细安装方法见 https://github.com/lucas-zgp/QT-matlab- 



## 6 演示demo

### 6.1 基于matlab创建c++库

* matlab创建两个文件

  * 一个名为matAdd.m，内容如下

    ```matlab
    function [c] = matAdd(a,b)
    c = a+b;
    end
    ```

  * 一个名为matdel.m，内容如下

    ```matlab
    function [c] = matdel(a,b)
    c = a-b;
    end
    ```

* matlab 使用`mbuild -setup`查看当前以何种语言进行编译，输出内容如下

  ![image-20210822222010924](https://gitee.com/LucasXm/img/raw/master/img//image-20210822222010924.png)

* matlab 使用`mbuild -setup c++ `选择当前以C++进行编译，输出内容如下

  ![image-20210822222125347](https://gitee.com/LucasXm/img/raw/master/img//image-20210822222125347.png)

* matlab 使用`deploytool`命令选择编译器要执行什么功能，输入内容如下

  ![image-20210822222256439](https://gitee.com/LucasXm/img/raw/master/img//image-20210822222256439.png)

  这里我们选择第三项，执行 Library Compiler

* 对打开后的界面执行如下图操作

  ![image-20210822222628477](https://gitee.com/LucasXm/img/raw/master/img//image-20210822222628477.png)

* 等待生成完成后，打开生成后的文件夹，如下图所示

  ![image-20210822222728806](https://gitee.com/LucasXm/img/raw/master/img//image-20210822222728806.png)

  这里我们要用到的是“**for_redistribution_files_only**”文件夹里面的内容，即**demo.dll、demo.h、demo.lib**

  ![image-20210822222834289](https://gitee.com/LucasXm/img/raw/master/img//image-20210822222834289.png)

### 6.2 QT引用matlab库必要的配置

* 在QT工程下创建一个include文件夹，将demo.h、demo.lib复制到此目录下

  ![image-20210822223211125](https://gitee.com/LucasXm/img/raw/master/img//image-20210822223211125.png)

* 在QT工程名上右键，点击添加库->外部库，然后将demo.lib添加进去，如下图

  ![image-20210822223520764](https://gitee.com/LucasXm/img/raw/master/img//image-20210822223520764.png)

* 还需要添加几个matlab相关的.lib文件和.h文件，如下

  ```makefile
  # .h文件搜索路径
  INCLUDEPATH += $$quote(C:/Program Files/MATLAB/R2015b/extern/include)
  INCLUDEPATH += $$quote(C:/Program Files/MATLAB/R2015b/extern/include/win64)
  
  # 用到的MATLAB 的.lib库文件 及其搜索路径
  INCLUDEPATH += $$quote(C:/Program Files/MATLAB/R2015b/extern/lib/win64/microsoft)
  DEPENDPATH += $$quote(C:/Program Files/MATLAB/R2015b/extern/lib/win64/microsoft)
  
  win32: LIBS += -L$$quote(C:/Program Files/MATLAB/R2015b/extern/lib/win64/microsoft) -llibmex
  win32: LIBS += -L$$quote(C:/Program Files/MATLAB/R2015b/extern/lib/win64/microsoft) -llibmx
  win32: LIBS += -L$$quote(C:/Program Files/MATLAB/R2015b/extern/lib/win64/microsoft) -llibmat
  win32: LIBS += -L$$quote(C:/Program Files/MATLAB/R2015b/extern/lib/win64/microsoft) -llibeng
  win32: LIBS += -L$$quote(C:/Program Files/MATLAB/R2015b/extern/lib/win64/microsoft) -lmclmcr
  win32: LIBS += -L$$quote(C:/Program Files/MATLAB/R2015b/extern/lib/win64/microsoft) -lmclmcrrt
  ```

  由于笔记的matlab安装目录有空格（Program Files），所以采用了$$quote()命令

* 添加如下路径到系统环境变量，如下图

  ![image-20210822224111233](https://gitee.com/LucasXm/img/raw/master/img//image-20210822224111233.png)

### 6.3 QT引用matlab库

* 在mainwindow.ui中添加如下界面

  ![image-20210822224338970](https://gitee.com/LucasXm/img/raw/master/img//image-20210822224338970.png)

* 在mainwindow.cpp中编写代码

  ```c++
  #include "mainwindow.h"
  #include "ui_mainwindow.h"
  #include "demo.h"
  #include "qdebug.h"
  
  MainWindow::MainWindow(QWidget *parent) :
      QMainWindow(parent),
      ui(new Ui::MainWindow)
  {
      ui->setupUi(this);
  
      if (!demoInitialize()) //DLL 初始化
      {
          return;
      }
  }
  
  MainWindow::~MainWindow()
  {
      delete ui;
  }
  
  void MainWindow::on_pushButton_clicked()
  {
      ui->label->setText("+");
      double   vectA = ui->lineEdit->text().toInt(nullptr,10);
      double   vectB = ui->lineEdit_2->text().toInt(nullptr,10);
      int   vectC = 0;
  
      mwArray matrixA(1,1,mxDOUBLE_CLASS, mxREAL);//定义数组
      mwArray matrixB(1,1,mxDOUBLE_CLASS, mxREAL);//定义数组
      mwArray matrixC(1,1,mxDOUBLE_CLASS, mxREAL);//定义数组
      matrixA.SetData(&vectA,1);
      matrixB.SetData(&vectB,1);
  
      matAdd(1,matrixC,matrixA,matrixB);
  
      matrixC.GetData(&vectC,1);
  
      ui->lineEdit_3->setText(QString::number(vectC,10));
  }
  
  void MainWindow::on_pushButton_2_clicked()
  {
      ui->label->setText("-");
      double   vectA = ui->lineEdit->text().toInt(nullptr,10);
      double   vectB = ui->lineEdit_2->text().toInt(nullptr,10);
      int   vectC = 0;
  
      mwArray matrixA(1,1,mxDOUBLE_CLASS, mxREAL);//定义数组
      mwArray matrixB(1,1,mxDOUBLE_CLASS, mxREAL);//定义数组
      mwArray matrixC(1,1,mxDOUBLE_CLASS, mxREAL);//定义数组
      matrixA.SetData(&vectA,1);
      matrixB.SetData(&vectB,1);
  
      matdel(1,matrixC,matrixA,matrixB);
  
      matrixC.GetData(&vectC,1);
  
      ui->lineEdit_3->setText(QString::number(vectC,10));
  }
  ```

* 代码理解
  * `demoInitialize()`函数是必须要调用的，调用后，才可以引用相关API
  * 使用`mwArray matrixA(1,1,mxDOUBLE_CLASS, mxREAL);`的方法来定义数组，这里定义了一个1X1的double型实数数组
  * 使用`matrixA.SetData(&vectA,1);`进行数组赋值
    * 第一个形参为设置的值
    * 第二个形参为设置参数的个数
  * 对于`matAdd(1,matrixC,matrixA,matrixB);`
    * 函数中的第1个形参为返回值个数，这里为1个返回值，即填入1
    * 函数中的第2个形参为返回值
    * 函数中的第3个、第4个形参为输入值
  * 使用`matrixC.GetData(&vectC,1);`获取参数
    * 第一个形参为获取到的值
    * 第二个形参为获取参数的个数

### 6.4 运行可执行程序

* 我们需要将自己生成的demo.dll添加到执行程序所在目录下

* 将matlab安装目录\bin\win64目录下的SSLEAY32.DLL和LIBEAY32.DLL文件拷贝到可执行程序所在目录下

* 如下图

  ![image-20210822225520737](https://gitee.com/LucasXm/img/raw/master/img//image-20210822225520737.png)

### 6.5 演示效果

![image-20210822225547722](https://gitee.com/LucasXm/img/raw/master/img//image-20210822225547722.png)

![image-20210822225555205](https://gitee.com/LucasXm/img/raw/master/img//image-20210822225555205.png)

#### 6.6 程序打包及其他电脑使用

* 和常规的QT程序一样，我们可以使用`windeployqt`命令进行打包

* 需要将自己生成的demo.dll文件拷贝到exe执行程序目录下

* 完成上诉两步，就可以在自己的电脑上使用打包好的exe程序了，如果要放到别的电脑上去运行，别的电脑还需要安装matlab的运行环境

* 打开 https://ww2.mathworks.cn/products/compiler/matlab-runtime.html 网址，下载matlab版本对应的运行环境，这里使用的是2015b,所以我们下载下图这个安装包，注意位数

  ![image-20210822230213936](https://gitee.com/LucasXm/img/raw/master/img//image-20210822230213936.png)

* 别的电脑上，安装上这个运行环境后，就可以正常运行程序了