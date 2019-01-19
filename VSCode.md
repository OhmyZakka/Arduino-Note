### 功能:

1. Arduino草图的智能感知和语法高亮显示

2. 在Visual Studio代码中验证并上传草图

3. 内置板和库管理器

4. 内置示例列表

5. 内置串行监视器

6. 草图片断

7. 自动脚手架

8. 命令选项板( F1 )集成常用命令(例如验证、上传...) )
9. 集成Arduino调试新功能



### 右下角

1. Select Programmer

   > 选择外部程序上传

1. Show Borad Config

   > 开发板的全部配置

1. Open Serial Monitor

   > 切换串行监视器显示状态

1. Selec Serial Port

   > 更改当前串行端口。



### 命令

- **Arduino: Board Manager**: 

  > 管理主板的软件包。

  您可以通过在“板管理器”中配置“额外的板管理器网址”来添加第三方Arduino板。

- **Arduino: Change Baud Rate**: 

  > 更改选定串行端口的波特率。115200

- **Arduino: Change Board Type**:

  >  更改板类型或平台。 ESPDuino

- **Arduino: Close Serial Monitor**: 

  > 停止串行监视器并释放串行端口。

- **Arduino: Examples**: 

  > 显示示例列表。

- **Arduino: Initialize**: 

  > 在.vscode 文件夹中，生成 Arduino.json 文件

- **Arduino: Library Manager**: 

  > 查找管理 库文件

- **Arduino: Open Serial Monitor**: 

  > 在集成输出窗口中打开串行监视器。

- **Arduino: Select Serial Port**: 

  > 更改当前串行端口。

- **Arduino: Send Text to Serial Port**: 

  > 通过当前串行端口发送一行文本。

- **Arduino: Upload**: 

  > 建立草图并上传至Arduino板。

- **Arduino: Upload Using Programmer**: 

  > 上传使用外部程序

- **Arduino: Verify**: 

  > 制作草图。



### 技巧

1. 使用 `#include <Arduino.h>` 头文件，输入时会有自动提示
1. 不要把两个 `Arduino` 文件放到同一目录！会出现 `'void loop()' previously defined here` 错误



### 设置

```json
{
    "arduino.path": "C:/Program Files (x86)/Arduino",
    "arduino.commandPath": "run-arduino.bat",
    "arduino.additionalUrls": "",
    "arduino.logLevel": "info",
    "arduino.enableUSBDetection": true,
    "arduino.disableTestingOpen": false,
    "arduino.skipHeaderProvider": false,
}
```

- arduino.path 

  > Arduino的路径，您可以通过修改此设置以包括完整路径来使用Arduino的自定义版本。 

- arduino.commandPath

  > 相对于Arduino . path的可执行文件(或脚本)的路径。可以使用自定义启动脚本运行Arduino。 

- arduino.additionalUrls

  > 第三方软件包的其他网址。

  您可以在一个字符串中包含多个URL，以逗号(，)作为分隔符，也可以包含一个字符串数组。

- arduino.logLevel 

  > CLI输出日志级别。可以是信息或详细信息。默认值为“信息”。

- arduino.enableUSBDetection

  > 启用USB检测

- arduino.disableTestingOpen

  > 禁用/启用自动向串行端口发送测试消息以检查打开状态。默认值为false (将发送测试消息)。

- arduino.skipHeaderProvider  

  > 启用/禁用为标题提供完成项的扩展。该功能包含在c++扩展的较新版本中。默认值为false。



### 选项

以下设置是Arduino扩展的每个草图设置。你可以在工作区下的 `.vscode/arduino.json` 找到它们

```json
{
    "sketch": "example.ino", Arduino的主草图文件名。
    "port": "COM5", 连接到设备的串行端口的名称。
    "board": "adafruit:samd:adafruit_feather_m0", 当前选择的Arduino板别名。
    "output": "../build", Arduino生成输出路径。
    "debugger": "jlink",当板本身没有任何调试器并且有多个调试器可用时，将使用的调试器的简称。
    "prebuild": "bash prebuild.sh" 构建草图文件之前的外部命令。
}
```



### 调试

Before you start debug your Arduino code, read [this doc](https://code.visualstudio.com/docs/editor/debugging) and get to know the basic mechanism about debugging in Visual Studio Code. Also see [debugging for C++ in VSCode](https://code.visualstudio.com/docs/languages/cpp#_debugging) for your reference.

Make sure your Arduino board can work with [STLink](http://www.st.com/en/development-tools/st-link-v2.html), [Jlink](https://www.segger.com/jlink-debug-probes.html) or [EDBG](http://www.atmel.com/webdoc/protocoldocs/ch01s01.html). The debugging support currently is fully tested with the following boards.

- [MXChip IoT Developer Kit - AZ3166](https://microsoft.github.io/azure-iot-developer-kit/)
- [Arduino M0 PRO](https://www.arduino.cc/en/Main/ArduinoBoardM0PRO)
- [Adafruit WICED WiFi Feather](https://www.adafruit.com/product/3056)
- [Adafruit Feather M0](https://www.adafruit.com/product/3010)
- Arduino Zero Pro

Steps to start debugging:

1. Plugin your board to your development machine properly. For those boards don't have on-board debugging chip, you need use STLink or JLink connector.
1. Go to **Debug View** (`Ctrl + Shift + D`). Set breakpoints in your source files.
1. Press `F5` to select debugging environment.
1. When your breakpoint is hit, you can see variables and add expression to watch on the Debug Side Bar.

> To learn more about how to debug Arduino code, visit our [team blog](https://blogs.msdn.microsoft.com/iotdev/2017/05/27/debug-your-arduino-code-with-visual-studio-code/).





