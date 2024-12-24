
## qss简介


qss（Qt Style Sheets）是一种基于CSS的样式语言，用于描述用户界面元素的外观和感觉。qss可以让用户在不修改代码的情况下，轻松地自定义应用程序的外观。


其语法基本如下：



```


|  | objectName{ |
| --- | --- |
|  | property: value; |
|  | } |


```

其中，objectName是要设置样式的对象名，property是要设置的属性，value是属性的值。比如，要设置按钮的背景色为红色，可以这样写：



```


|  | QPushButton{ |
| --- | --- |
|  | background-color: red; |
|  | } |


```

若想对同一控件设置多套样式，可以指定姓名



```


|  | QLabel#label1{ |
| --- | --- |
|  | background-color: blue; |
|  | } |
|  | QLabel#label2{ |
|  | background-color: green; |
|  | } |


```

在程序中使用`setObjectName();`方法设置控件名称对应到qss中的名称即可使用对应的样式。



```


|  | QLabel *label = new QLabel(this); |
| --- | --- |
|  | label->setObjectName("label1"); // 此label背景色为蓝色 |
|  | QLabel *label2 = new QLabel(this); |
|  | label2->setObjectName("label2"); // 此label背景色为绿色 |


```

对于一些拥有不同状态的控件，如按钮在普通状态以下按下状态，以及鼠标悬停状态等，可以用不同的qss样式表实现不同状态的样式。



```


|  | QPushButton:pressed{ |
| --- | --- |
|  | background-color: green; |
|  | } |
|  | // 按下按钮时改变背景颜色为绿色 |
|  |  |
|  | QPushButton:hover{ |
|  | background-color: yellow; |
|  | } |
|  | // 鼠标悬停在按钮上时改变背景颜色为黄色 |
|  |  |


```

可以混合使用，比如：



```


|  | QPushButton#btn1:pressed{ |
| --- | --- |
|  | background-color: green; |
|  | } |
|  | // 按下btn1按钮时改变背景颜色为绿色 |


```

## qrc文件


对于样式表，可以使用一个后缀为`.qrc`的文件来管理`.qss`以及编写时所用的图片资源。其实质上就是一个xml文件，里面可以包含多个标签，每个标签对应一个资源文件。



```


|  | <RCC> |
| --- | --- |
|  | <qresource prefix="/"> |
|  | <file>ui/img/img.pngfile> |
|  | <file>ui/img/close.pngfile> |
|  | <file>ui/img/min.pngfile> |
|  | <file>ui/img/max.pngfile> |
|  | <file>ui/img/R.svgfile> |
|  | <file>ui/style/style.qssfile> |
|  | qresource> |
|  | RCC> |


```

其中表示该文件为资源文件集合，表示资源文件，`prefix`属性表示资源文件的前缀，这里设置为`/`表示资源文件位于程序根目录下。标签表示资源文件名，这里可以指定多个资源文件。



> 注意，这里使用`prefix`设置的路径是虚拟路径。在下面的中设置的资源文件的路径应当是相对于`.qrc`文件的路径。这样设置后，在程序中可以通过如下方式获取资源文件：



```


|  | QFile file(":/ui/style/style.qss"); |
| --- | --- |
|  | /*通过":"表示使用虚拟路径访问文件*/ |


```

倘若更改`prefix`如下



```


|  | <qresource prefix="/source"> |
| --- | --- |


```

则在程序中获取资源文件时，应当使用如下方式：



```


|  | QFile file(":/source/ui/style/style.qss"); |
| --- | --- |
|  | /*通过":"表示使用虚拟路径访问文件*/ |


```

也就是说这里定义的虚拟路径不需要它在物理地址中存在，即可使用Qt这种资源管理机制来管理资源文件。方便的实现资源的分组、共享以及版本控制。


设置好`.qrc`文件后，在程序中可以用如下方式获取资源文件中整合的文件：



```


|  | QFile file(":/ui/style/style.qss"); |
| --- | --- |
|  | /*通过":"表示资源文件*/ |


```

在`qss`文件中，也可以使用这种方法来引用资源文件中的资源：



```


|  | QLabel{ |
| --- | --- |
|  | background-image: url(":/ui/img/img.png"); |
|  | } |


```

## Vscode应用qss样式表


在扩展安装界面安装`QSSEditor`插件，这样可以提供qss相关的代码补全以及高亮显示功能。


在写qss时，可能输出以下，大概是由于这个插件可以联动QtDesigner来实时显示样式。这方面我也不太会。不影响对qss代码的补全高亮。
![image](https://img2024.cnblogs.com/blog/3147612/202412/3147612-20241223212031527-1417779064.png)


建立文件树如下的目录结构：



```


|  | ├── ui |
| --- | --- |
|  | │   ├── img |
|  | │   │   ├── close.png |
|  | │   │   ├── img.png |
|  | │   │   ├── max.png |
|  | │   │   ├── min.png |
|  | │   │   └── R.svg |
|  | │   └── style |
|  | │       └── style.qss |
|  | └── ui.qrc |
|  |  |


```

名称任意取。其中`ui.qrc`文件的要求上面已经说明。若要使用该文件，需要修改CMakeLists.txt文件，添加如下内容：



```


|  | find_package(Qt5 REQUIRED COMPONENTS Core Widgets REQUIRED) |
| --- | --- |
|  |  |
|  | set(CMAKE_AUTOMOC ON) |
|  | set(CMAKE_AUTORCC ON) # RCC编译器将qrc文件编译为cpp文件 |
|  | set(CMAKE AUTOUIC ON) |
|  | # 注意 ： 要想使用qt5_add_resources，必须先find_package到Qt的路径或者手动指定QT_DIR |
|  | set(QT_SOURCE_FILE ui.qrc) # 将资源文件放到工程目录下，该部分会由RCC编译器预编译为cpp文件 |
|  |  |
|  | # 添加资源文件 |
|  | qt5_add_resources(QT_RESOURCES ${QT_SOURCE_FILE}) |
|  |  |
|  | # 添加源文件 |
|  | set(SOURCE_FILES main.cpp) |
|  |  |
|  | # 生成可执行文件 |
|  | add_executable(main ${SOURCE_FILES} ${QT_RESOURCES}) |
|  |  |


```

这样在主程序中就可以通过Qt的资源管理机制来访问资源文件了。


要想应用qss样式表，在主程序中调用`setStyleSheet()`方法即可。



```


|  | #include <QApplication> |
| --- | --- |
|  | int main(int argc, char *argv[]) |
|  | { |
|  | QApplication app(argc, argv); |
|  | QFile file(":/ui/style/style.qss"); # 读取样式表文件 |
|  | if (file.open(QFile::ReadOnly)) |
|  | { |
|  | QTextStream stream(&file); |
|  | QString styleSheet = stream.readAll(); |
|  | app.setStyleSheet(styleSheet); |
|  | } |
|  | MainWindow win; |
|  | win.show(); |
|  | return app.exec(); |
|  | } |


```

当然，在Qt中，每个控件都有属于自己的`setStyleSheet()`接口用于单独设置样式，用起来也是很简单粗暴，其参数是一个qss样式表字符串。



```


|  | QLabel *label = new QLabel(this); |
| --- | --- |
|  | /*设置label的背景色为红色*/ |
|  | label->setStyleSheet("background-color: red;"); |
|  | /*当然下面这种写法也ok*/ |
|  | label->setStyleSheet("QLabel{background-color: red;}"); |


```

 本博客参考[veee加速器](https://blog.liuyunzhuge.com)。转载请注明出处！
