#include <windows.h>
#include <bits/stdc++.h> 
#include <process.h>
using namespace std;  

// 全局标志
volatile bool running = true;
bool useMouse = false;          // 是否使用鼠标点击
POINT clickPos = {0, 0};        // 鼠标点击坐标
vector<int> sendKeys;           // 发送键组合（1=Ctrl, 2=Shift, 3=Enter）

// 反转vector辅助函数
template<typename T>
vector<T> reverse(const vector<T>& vec) {
    vector<T> result(vec);
    reverse(result.begin(), result.end());
    return result;
}

// 模拟键盘组合键
void simulateKeyCombination(const vector<int>& keys) {
    // 按下修饰键
    for (vector<int>::const_iterator it = keys.begin(); it != keys.end(); ++it) {
        if (*it == 1 || *it == 2) {  // Ctrl或Shift
            INPUT input = {0};
            input.type = INPUT_KEYBOARD;
            input.ki.wVk = (*it == 1) ? VK_CONTROL : VK_SHIFT;
            SendInput(1, &input, sizeof(INPUT));
        }
    }
    
    // 按下Enter
    for (vector<int>::const_iterator it = keys.begin(); it != keys.end(); ++it) {
        if (*it == 3) {
            INPUT enterDown = {0};
            enterDown.type = INPUT_KEYBOARD;
            enterDown.ki.wVk = VK_RETURN;
            SendInput(1, &enterDown, sizeof(INPUT));
            break;
        }
    }
    
    // 释放所有键
    vector<int> reversedKeys = reverse(keys);
    for (vector<int>::const_iterator it = reversedKeys.begin(); it != reversedKeys.end(); ++it) {
        if (*it == 1 || *it == 2) {
            INPUT input = {0};
            input.type = INPUT_KEYBOARD;
            input.ki.wVk = (*it == 1) ? VK_CONTROL : VK_SHIFT;
            input.ki.dwFlags = KEYEVENTF_KEYUP;
            SendInput(1, &input, sizeof(INPUT));
        }
    }
    
    for (vector<int>::const_iterator it = reversedKeys.begin(); it != reversedKeys.end(); ++it) {
        if (*it == 3) {
            INPUT enterUp = {0};
            enterUp.type = INPUT_KEYBOARD;
            enterUp.ki.wVk = VK_RETURN;
            enterUp.ki.dwFlags = KEYEVENTF_KEYUP;
            SendInput(1, &enterUp, sizeof(INPUT));
            break;
        }
    }
}

// 模拟鼠标点击
void simulateMouseClick() {
    SetCursorPos(clickPos.x, clickPos.y);
    mouse_event(MOUSEEVENTF_LEFTDOWN, 0, 0, 0, 0);
    Sleep(50);
    mouse_event(MOUSEEVENTF_LEFTUP, 0, 0, 0, 0);
}

// 设置剪贴板内容
bool setClipboardText(const string& text) {
    if (!OpenClipboard(NULL)) {
        cerr << "无法打开剪贴板!" << endl;
        return false;
    }

    if (!EmptyClipboard()) {
        cerr << "无法清空剪贴板!" << endl;
        CloseClipboard();
        return false;
    }

    size_t len = (text.length() + 1) * sizeof(wchar_t);
    HGLOBAL hMem = GlobalAlloc(GMEM_MOVEABLE, len);
    
    if (!hMem) {
        cerr << "内存分配失败!" << endl;
        CloseClipboard();
        return false;
    }

    wchar_t* wcText = new wchar_t[text.length() + 1];
    mbstowcs(wcText, text.c_str(), text.length() + 1);
    
    memcpy(GlobalLock(hMem), wcText, len);
    GlobalUnlock(hMem);
    delete[] wcText;

    if (!SetClipboardData(CF_UNICODETEXT, hMem)) {
        cerr << "无法设置剪贴板数据!" << endl;
        GlobalFree(hMem);
        CloseClipboard();
        return false;
    }

    CloseClipboard();
    return true;
}

// 解析发送键组合
bool parseSendKeys(const string& str) {
    sendKeys.clear();
    for (string::const_iterator it = str.begin(); it != str.end(); ++it) {
        if (*it == '1') sendKeys.push_back(1);
        else if (*it == '2') sendKeys.push_back(2);
        else if (*it == '3') sendKeys.push_back(3);
    }
    
    // 必须包含Enter或选择鼠标模式
    bool hasEnter = false;
    for (vector<int>::const_iterator it = sendKeys.begin(); it != sendKeys.end(); ++it) {
        if (*it == 3) {
            hasEnter = true;
            break;
        }
    }
    
    if (!useMouse && !hasEnter) {
        cerr << "错误：键盘模式必须包含Enter键(3)" << endl;
        return false;
    }
    return true;
}

// 记录鼠标坐标
bool recordMousePosition() {
    cout << "请将鼠标移动到发送按钮位置，然后按空格键记录..." << endl;
    while (true) {
        if (GetAsyncKeyState(VK_SPACE) & 0x8000) {
            GetCursorPos(&clickPos);
            cout << "记录的坐标：(" << clickPos.x << ", " << clickPos.y << ")" << endl;
            return true;
        }
        Sleep(100);
        if (!running) return false;
    }
}

// 检测ESC键线程
unsigned __stdcall checkEscKey(void* param) {
    cout << "按ESC键终止程序..." << endl;
    while (running) {
        if (GetAsyncKeyState(VK_ESCAPE) & 0x8000) {
            cout << "\n检测到ESC键，程序正在终止..." << endl;
            running = false;
            break;
        }
        Sleep(100);
    }
    return 0;
}

int main() {
    double t;
    string clipboardText;
    
    // 输入间隔时间
    cout << "请输入间隔时间t（秒）：";
    cin >> t;
    cin.ignore();

    // 选择发送模式
    string mode;
    cout << "选择发送模式（键盘宏/鼠标宏，输入k/m）：";
    getline(cin, mode);
    useMouse = (mode == "m" || mode == "M");

    if (useMouse) {
        if (!recordMousePosition()) return 1;
    } else {
        string keyStr;
        cout << "输入发送键组合（1=Ctrl, 2=Shift, 3=Enter，例如123表示Ctrl+Shift+Enter）：";
        getline(cin, keyStr);
        if (!parseSendKeys(keyStr)) return 1;
    }

    // 输入剪贴板内容
    cout << "请输入要设置的剪贴板内容（直接回车不修改）：";
    getline(cin, clipboardText);
    if (!clipboardText.empty()) {
        if (!setClipboardText(clipboardText)) {
            cout << "警告：剪贴板设置失败，使用原有内容" << endl;
        }
    } else {
        cout << "提示：保留剪贴板原有内容" << endl;
    }

    // 启动前倒计时
    cout << "程序将在5秒后开始运行，请切换到目标窗口..." << endl;
    for (int i = 5; i > 0; i--) {
        cout << i << "..." << endl;
        Sleep(1000);
    }

    // 创建ESC检测线程
    HANDLE hThread = (HANDLE)_beginthreadex(NULL, 0, &checkEscKey, NULL, 0, NULL);
    if (!hThread) {
        cerr << "错误：无法创建ESC检测线程" << endl;
        return 1;
    }

    cout << "程序已启动，当前模式：" << (useMouse ? "鼠标点击" : "键盘组合") << endl;
    while (running) {
        // 模拟Ctrl+V
        INPUT ctrlDown = {0};
        ctrlDown.type = INPUT_KEYBOARD;
        ctrlDown.ki.wVk = VK_CONTROL;
        SendInput(1, &ctrlDown, sizeof(INPUT));

        INPUT vDown = {0};
        vDown.type = INPUT_KEYBOARD;
        vDown.ki.wVk = 'V';
        SendInput(1, &vDown, sizeof(INPUT));

        INPUT vUp = {0};
        vUp.type = INPUT_KEYBOARD;
        vUp.ki.wVk = 'V';
        vUp.ki.dwFlags = KEYEVENTF_KEYUP;
        SendInput(1, &vUp, sizeof(INPUT));

        INPUT ctrlUp = {0};
        ctrlUp.type = INPUT_KEYBOARD;
        ctrlUp.ki.wVk = VK_CONTROL;
        ctrlUp.ki.dwFlags = KEYEVENTF_KEYUP;
        SendInput(1, &ctrlUp, sizeof(INPUT));
        
        cout << "已执行粘贴操作" << endl;
        Sleep(500);

        // 执行发送操作
        if (useMouse) {
            simulateMouseClick();
            cout << "已执行鼠标点击" << endl;
        } else {
            simulateKeyCombination(sendKeys);
            cout << "已执行发送组合键" << endl;
        }

        // 等待间隔时间
        Sleep(static_cast<unsigned int>(t * 1000));
    }

    // 清理资源
    WaitForSingleObject(hThread, INFINITE);
    CloseHandle(hThread);
    cout << "程序已安全终止" << endl;
    return 0;
}
