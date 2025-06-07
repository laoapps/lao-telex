```
// LaoTelexDLL.h
#pragma once
#include <windows.h>

#ifdef LAOTEXDLL_EXPORTS
#define LAOTEXDLL_API __declspec(dllexport)
#else
#define LAOTEXDLL_API __declspec(dllimport)
#endif

extern "C" LAOTEXDLL_API void InstallHook(HWND hwnd);
extern "C" LAOTEXDLL_API void RemoveHook();

// LaoTelexDLL.cpp
#include "LaoTelexDLL.h"
#include <string>
#include <map>

HHOOK hKeyboardHook = NULL;
HWND hMainWnd = NULL;
std::wstring buffer;
std::map<std::wstring, std::wstring> keyMap = {
    {L"k", L"\u0E81"}, // ກ
    {L"a", L"\u0EB2"}, // າ
    {L"s", L"\u0ECB"}, // ໋ (high tone)
    {L"kas", L"\u0E81\u0EB2\u0ECB"}, // ກ້າ
    {L"kaf", L"\u0E81\u0EB2\u0EC8"}  // ກ່າ
};

void SendUnicodeString(const std::wstring& str) {
    std::vector<INPUT> inputs;
    for (wchar_t c : str) {
        INPUT input = {0};
        input.type = INPUT_KEYBOARD;
        input.ki.wVk = 0;
        input.ki.wScan = c;
        input.ki.dwFlags = KEYEVENTF_UNICODE;
        inputs.push_back(input);
        input.ki.dwFlags |= KEYEVENTF_KEYUP;
        inputs.push_back(input);
    }
    SendInput((UINT)inputs.size(), inputs.data(), sizeof(INPUT));
}

LRESULT CALLBACK KeyboardProc(int nCode, WPARAM wParam, LPARAM lParam) {
    if (nCode >= 0 && wParam == WM_KEYDOWN) {
        KBDLLHOOKSTRUCT* kbStruct = (KBDLLHOOKSTRUCT*)lParam;
        if (kbStruct->vkCode == VK_BACK) {
            if (!buffer.empty()) buffer.pop_back();
            return 1;
        }
        if (kbStruct->vkCode >= 'A' && kbStruct->vkCode <= 'Z') {
            wchar_t c = tolower(kbStruct->vkCode);
            buffer += c;
            auto it = keyMap.find(buffer);
            if (it != keyMap.end()) {
                SendUnicodeString(it->second);
                buffer.clear();
                return 1;
            }
            for (const auto& pair : keyMap) {
                if (pair.first.find(buffer) == 0) return 1; // Partial match, wait for more keys
            }
            buffer.clear();
        }
    }
    return CallNextHookEx(hKeyboardHook, nCode, wParam, lParam);
}

LAOTEXDLL_API void InstallHook(HWND hwnd) {
    hMainWnd = hwnd;
    hKeyboardHook = SetWindowsHookEx(WH_KEYBOARD_LL, KeyboardProc, GetModuleHandle(NULL), 0);
}

LAOTEXDLL_API void RemoveHook() {
    if (hKeyboardHook) UnhookWindowsHookEx(hKeyboardHook);
    hKeyboardHook = NULL;
}

// LaoTelexApp.cpp (System Tray App)
#include <windows.h>
#include "LaoTelexDLL.h"

#define ID_TRAY_APP_ICON 1001
#define ID_TRAY_EXIT 1002
#define WM_TRAYICON (WM_USER + 1)

NOTIFYICONDATA nid = {0};
HMENU hMenu;
bool isHookActive = false;

LRESULT CALLBACK WndProc(HWND hwnd, UINT msg, WPARAM wParam, LPARAM lParam) {
    switch (msg) {
    case WM_CREATE:
        nid.cbSize = sizeof(NOTIFYICONDATA);
        nid.hWnd = hwnd;
        nid.uID = ID_TRAY_APP_ICON;
        nid.uFlags = NIF_ICON | NIF_MESSAGE | NIF_TIP;
        nid.uCallbackMessage = WM_TRAYICON;
        nid.hIcon = LoadIcon(NULL, IDI_APPLICATION);
        lstrcpy(nid.szTip, L"Lao Telex Keyboard");
        Shell_NotifyIcon(NIM_ADD, &nid);
        hMenu = CreatePopupMenu();
        AppendMenu(hMenu, MF_STRING, ID_TRAY_EXIT, L"Exit");
        break;
    case WM_TRAYICON:
        if (LOWORD(lParam) == WM_RBUTTONDOWN) {
            POINT pt;
            GetCursorPos(&pt);
            SetForegroundWindow(hwnd);
            TrackPopupMenu(hMenu, TPM_RIGHTALIGN, pt.x, pt.y, 0, hwnd, NULL);
        } else if (LOWORD(lParam) == WM_LBUTTONDOWN) {
            isHookActive = !isHookActive;
            if (isHookActive) InstallHook(hwnd);
            else RemoveHook();
            Shell_NotifyIcon(NIM_MODIFY, &nid);
        }
        break;
    case WM_COMMAND:
        if (LOWORD(wParam) == ID_TRAY_EXIT) DestroyWindow(hwnd);
        break;
    case WM_DESTROY:
        Shell_NotifyIcon(NIM_DELETE, &nid);
        RemoveHook();
        PostQuitMessage(0);
        break;
    default:
        return DefWindowProc(hwnd, msg, wParam, lParam);
    }
    return 0;
}

int WINAPI WinMain(HINSTANCE hInstance, HINSTANCE, LPSTR, int nCmdShow) {
    WNDCLASSEX wc = {0};
    wc.cbSize = sizeof(WNDCLASSEX);
    wc.lpfnWndProc = WndProc;
    wc.hInstance = hInstance;
    wc.lpszClassName = L"LaoTelexClass";
    RegisterClassEx(&wc);

    HWND hwnd = CreateWindow(L"LaoTelexClass", L"Lao Telex", WS_OVERLAPPEDWINDOW,
                            CW_USEDEFAULT, CW_USEDEFAULT, 0, 0, NULL, NULL, hInstance, NULL);

    MSG msg;
    while (GetMessage(&msg, NULL, 0, 0)) {
        TranslateMessage(&msg);
        DispatchMessage(&msg);
    }
    return (int)msg.wParam;
}

// README.md
# Lao Telex Keyboard for Windows
A custom keyboard input method for typing Lao with a Telex-like style.

## Features
- Type Lao Unicode characters using key sequences (e.g., `kas` → `ກ້າ`).
- System tray app to toggle the keyboard.
- Open-source under MIT License.

## Installation
1. Build the solution in Visual Studio (requires VC++).
2. Copy `LaoTelexDLL.dll` and `LaoTelexApp.exe` to a directory.
3. Run `LaoTelexApp.exe`.
4. Click the tray icon to enable/disable the keyboard.

## Typing Rule
- `k` → `ກ` (consonant)
- `a` → `າ` (vowel)
- `s` → `໋` (high tone)
- `kas` → `ກ້າ` (syllable with high tone)
- Backspace to undo.

## Building
- Open `LaoTelex.sln` in Visual Studio.
- Build for x64 or x86.
- Ensure `LaoTelexDLL.lib` is linked.

## License
MIT License. See `LICENSE` file.

## Contributing
Fork the repository, make changes, and submit a pull request.
```
