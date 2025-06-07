


# 键盘鼠标宏自动操作工具（Windows版）

![项目状态](https://img.shields.io/badge/系统-Windows-blue)
![C++版本](https://img.shields.io/badge/C++-98兼容-yellow)
![开源协议](https://img.shields.io/badge/License-MIT-green)


## 项目简介
这是由 **ATxht** 开发的Windows自动化工具，用于定时模拟键盘粘贴（Ctrl+V）和发送操作，支持自定义键盘组合键或鼠标点击两种发送模式。适用于需要重复粘贴并发送消息的场景（如聊天软件、表单填写等），具备急停功能和剪贴板内容管理能力。


## 核心功能
1. **定时粘贴发送**  
   - 可设置间隔时间 `t`（秒），循环执行 **Ctrl+V粘贴 → 发送** 流程。  
   - 发送操作支持两种模式：  
     - **键盘宏**：自定义组合键（如 `Enter`、`Shift+Enter`、`Ctrl+Enter` 等）。  
     - **鼠标宏**：记录鼠标坐标并模拟点击（适配非键盘发送的按钮）。  

2. **剪贴板灵活控制**  
   - 可设置固定剪贴板内容，或保留系统原有内容。  
   - 支持中文及特殊字符（通过Unicode处理）。  

3. **安全急停机制**  
   - 按下 `ESC` 键立即终止程序，确保紧急情况下可中断操作。  

4. **交互友好性**  
   - 分步引导式输入（间隔时间、发送模式、剪贴板内容）。  
   - 鼠标模式支持实时坐标记录，可视化操作流程。  


## 环境要求
- **操作系统**：Windows 7/8/10/11（仅支持32位或64位桌面版）  
- **编译器**：支持C++98的编译器（如GCC 4.8+、Visual C++ 6.0、MinGW等）,推荐使用DEV-C++，因为兼容万能头文件（当然这个不重要）
- **依赖库**：Windows系统库 `user32.lib`（无需额外安装，编译时自动链接）  


## 快速开始

### 步骤1：获取代码
```bash
git clone https://github.com/ATxht/Repeater_by_keyboard_and_mouse.git
cd Repeater_by_keyboard_and_mouse
```

### 步骤2：编译程序
#### **GCC/MinGW编译（推荐）**
```bash
g++ src/main.cpp -o Repeater_by_keyboard_and_mouse -luser32
```

#### **Visual Studio编译**  
1. 创建空项目，将 `main.cpp` 加入源文件。  
2. 配置项目属性：链接器 → 输入 → 附加依赖项添加 `user32.lib`。  
3. 编译生成可执行文件。  

### 步骤3：运行程序
```bash
# Windows命令行运行
Repeater_by_keyboard_and_mouse.exe
```


## 使用指南

### 1. 输入间隔时间
```plaintext
请输入间隔时间t（秒）：5  # 示例：每5秒执行一次粘贴发送
```

### 2. 选择发送模式
#### **键盘宏模式（输入 k）**  
```plaintext
选择发送模式（键盘宏/鼠标宏，输入k/m）：k  
输入发送键组合（1=Ctrl, 2=Shift, 3=Enter）：13  # 示例：Ctrl+Enter  
```  
- **组合键规则**：  
  `1` = Ctrl，`2` = Shift，`3` = Enter，可任意组合（如 `23` 表示 Shift+Enter）。  
  **注意**：键盘模式必须包含 `3`（Enter键）。  

#### **鼠标宏模式（输入 m）**  
```plaintext
选择发送模式（键盘宏/鼠标宏，输入k/m）：m  
请将鼠标移动到发送按钮位置，然后按空格键记录...  # 移动鼠标后按空格  
记录的坐标：(1024, 768)  # 示例坐标  
```

### 3. 设置剪贴板内容  
```plaintext
请输入要设置的剪贴板内容（直接回车不修改）：你好，世界！  # 示例内容  
```

### 4. 启动与终止  
- 程序将在 **5秒倒计时** 后开始运行，期间请切换到目标窗口。  
- 按下 `ESC` 键可随时终止程序，控制台显示 `程序已安全终止`。  


## 代码结构说明  
```cpp
// 核心功能模块  
void simulateCtrlV();          // 模拟Ctrl+V粘贴  
void simulateKeyCombination(); // 键盘组合键发送  
void simulateMouseClick();     // 鼠标坐标点击  
bool setClipboardText();       // 剪贴板内容设置  
unsigned __stdcall checkEscKey(); // ESC急停检测线程  
```


## 注意事项  
1. **安全软件拦截**  
   - 部分杀毒软件可能将键盘/鼠标模拟视为风险操作，建议将程序添加到信任列表。  
   - 若出现 `SendInput` 失败，可能是权限不足，尝试以管理员身份运行程序。  

2. **鼠标模式限制**  
   - 窗口位置变化后需重新记录坐标，确保点击位置准确。  
   - 不支持动态加载的按钮（如弹窗中的按钮）。  

3. **中文剪贴板问题**  
   - 若出现乱码，可尝试修改编译器字符集为 UTF-8，或直接使用系统默认编码（GBK）。  


## 贡献与反馈  
- **提交Issue**：欢迎在 [ATxht/Repeater_by_keyboard_and_mouse Issues](https://github.com/ATxht/Repeater_by_keyboard_and_mouse/issues) 反馈问题或建议。  
- **代码贡献**：提交Pull Request前请先创建Issue说明需求，确保代码风格一致（使用C++98语法，注释清晰）。  


## 开源协议  
本项目采用 **MIT License**，允许自由修改、分发和商业使用，但需保留原作者声明和协议文件。  
© 2023 **ATxht**。保留部分权利。  


## 联系方式  
- GitHub：[ATxht](https://github.com/ATxht)  

如果项目对你有帮助，欢迎点亮 ⭐ 支持！
