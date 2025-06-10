#include <iostream>
#include <fstream>
#include <vector>
#include <stack>
#include <windows.h>
#include <conio.h>
#include <string>
#include <thread>

using namespace std;

const int SCREEN_WIDTH = 120;
const int SCREEN_HEIGHT = 30;
const int TOWER_COUNT = 3;
const int GROUND_Y = 22;
const char POLE_CHAR = '|';
const char DISK_CHAR = '*';
const char GROUND_CHAR = '=';

struct Disk {
    int size;
};

vector<stack<Disk>> towers(TOWER_COUNT);
int diskCount = 3;
bool isRunning = true;

void GotoXY(int x, int y) {
    COORD coord = { (SHORT)x, (SHORT)y };
    SetConsoleCursorPosition(GetStdHandle(STD_OUTPUT_HANDLE), coord);
}

void ClearScreen() {
    system("cls");
}

void DrawGroundAndPoles() {
    const int towerX[TOWER_COUNT] = { 30, 60, 90 };

    for (int i = 0; i < SCREEN_WIDTH; ++i) {
        GotoXY(i, GROUND_Y);
        cout << GROUND_CHAR;
    }

    for (int i = 0; i < TOWER_COUNT; ++i) {
        for (int j = 0; j < diskCount + 1; ++j) {
            GotoXY(towerX[i], GROUND_Y - 1 - j);
            cout << POLE_CHAR;
        }
    }
}

void DrawDisks() {
    const int towerX[TOWER_COUNT] = { 30, 60, 90 };
    for (int i = 0; i < TOWER_COUNT; ++i) {
        stack<Disk> temp;
        int level = 0;
        while (!towers[i].empty()) {
            temp.push(towers[i].top());
            towers[i].pop();
        }
        while (!temp.empty()) {
            towers[i].push(temp.top());
            int size = temp.top().size;
            int width = size * 2;
            int centerX = towerX[i];
            int y = GROUND_Y - 1 - (++level);
            GotoXY(centerX - size, y);
            for (int j = 0; j < width; ++j) cout << DISK_CHAR;
            temp.pop();
        }
    }
}

void AnimateMove(int from, int to, Disk disk) {
   }

void WaitForBackKey() {
    GotoXY((SCREEN_WIDTH - 30) / 2, SCREEN_HEIGHT - 2);
    cout << "Натисніть Enter щоб повернутись...";
    while (_getch() != 13) {}
}

void LoadFileAndPrint(const string& filename) {
    ClearScreen();
    ifstream file(filename);
    if (!file) {
        cerr << "Не вдалося відкрити файл: " << filename << endl;
        WaitForBackKey();
        return;
    }
    string line;
    int y = 5;
    while (getline(file, line)) {
        GotoXY((SCREEN_WIDTH - line.length()) / 2, y++);
        cout << line;
    }
    file.close();
    WaitForBackKey();
}

int NavigateMenu(const vector<string>& items, int startY) {
    int selected = 0;
    while (true) {
        for (int i = 0; i < items.size(); ++i) {
            GotoXY((SCREEN_WIDTH - items[i].length()) / 2, startY + i * 2);
            if (i == selected) {
                SetConsoleTextAttribute(GetStdHandle(STD_OUTPUT_HANDLE), FOREGROUND_GREEN | FOREGROUND_INTENSITY);
                cout << "> " << items[i];
                SetConsoleTextAttribute(GetStdHandle(STD_OUTPUT_HANDLE), FOREGROUND_RED | FOREGROUND_GREEN | FOREGROUND_BLUE);
            } else {
                cout << "  " << items[i];
            }
        }
        int key = _getch();
        if (key == 224) {
            key = _getch();
            if (key == 72 && selected > 0)
                --selected;
            else if (key == 80 && selected < items.size() - 1)
                ++selected;
        } else if (key == 13  key == 10) {
            return selected + 1;
        }
    }
}

void AutoMove(int n, int from, int to, int aux) {
    if (n == 0) return;
    AutoMove(n - 1, from, aux, to);
    towers[to].push(towers[from].top());
    towers[from].pop();
    DrawTowersVisual();
    this_thread::sleep_for(chrono::milliseconds(600));
    AutoMove(n - 1, aux, to, from);
}

void GameMenu() {
    ClearScreen();
    GotoXY((SCREEN_WIDTH - 30) / 2, 7);
    cout << "Введіть кількість дисків (1-9): ";
    cin >> diskCount;
    if (diskCount < 1  diskCount > 9) diskCount = 3;

    for (auto& tower : towers) while (!tower.empty()) tower.pop();
    for (int i = diskCount; i >= 1; --i) towers[0].push({ i });

    vector<string> items = {
        "Ручний режим",
        "Автоматичний режим",
        "Назад"
    };

    int choice = NavigateMenu(items, 11);
    if (choice == 1) {
       } else if (choice == 2) {
            }
}

void MainMenu() {
    while (isRunning) {
        ClearScreen();
        GotoXY((SCREEN_WIDTH - 20) / 2, 5);
        cout << "=== ГОЛОВНЕ МЕНЮ ===";

        vector<string> items = {
            "Почати гру",
            "Правила гри",
            "Інформація про автора",
            "Вихід"
        };

        int choice = NavigateMenu(items, 8);

        switch (choice) {
            case 1:
                GameMenu();
                break;
            case 2:
                LoadFileAndPrint("rules.txt");
                break;
            case 3:
                LoadFileAndPrint("author.txt");
                break;
            case 4:
                isRunning = false;
                break;
        }
    }
}

int main() {
    SetConsoleOutputCP(1251);
    SetConsoleCP(1251);

    HANDLE hOut = GetStdHandle(STD_OUTPUT_HANDLE);
    SMALL_RECT windowSize = {0, 0, SCREEN_WIDTH - 1, SCREEN_HEIGHT - 1};
    SetConsoleWindowInfo(hOut, TRUE, &windowSize);

    CONSOLE_CURSOR_INFO cursorInfo;
    GetConsoleCursorInfo(hOut, &cursorInfo);
    cursorInfo.bVisible = FALSE;
    SetConsoleCursorInfo(hOut, &cursorInfo);

    MainMenu();
    return 0;
}#include <iostream>
#include <fstream>
#include <vector>
#include <stack>
#include <windows.h>
#include <conio.h>
#include <string>
#include <thread>

using namespace std;

const int SCREEN_WIDTH = 120;
const int SCREEN_HEIGHT = 30;
const int TOWER_COUNT = 3;
const int GROUND_Y = 22;
const char POLE_CHAR = '|';
const char DISK_CHAR = '*';
const char GROUND_CHAR = '=';

struct Disk {
    int size;
};

vector<stack<Disk>> towers(TOWER_COUNT);
int diskCount = 3;
bool isRunning = true;

void GotoXY(int x, int y) {
    COORD coord = { (SHORT)x, (SHORT)y };
    SetConsoleCursorPosition(GetStdHandle(STD_OUTPUT_HANDLE), coord);
}

void ClearScreen() {
    system("cls");
}

void DrawGroundAndPoles() {
    const int towerX[TOWER_COUNT] = { 30, 60, 90 };

    for (int i = 0; i < SCREEN_WIDTH; ++i) {
        GotoXY(i, GROUND_Y);
        cout << GROUND_CHAR;
    }

    for (int i = 0; i < TOWER_COUNT; ++i) {
        for (int j = 0; j < diskCount + 1; ++j) {
            GotoXY(towerX[i], GROUND_Y - 1 - j);
            cout << POLE_CHAR;
        }
    }
}

void DrawDisks() {
    const int towerX[TOWER_COUNT] = { 30, 60, 90 };
    for (int i = 0; i < TOWER_COUNT; ++i) {
        stack<Disk> temp;
        int level = 0;
        while (!towers[i].empty()) {
            temp.push(towers[i].top());
            towers[i].pop();
        }
        while (!temp.empty()) {
            towers[i].push(temp.top());
            int size = temp.top().size;
            int width = size * 2;
            int centerX = towerX[i];
            int y = GROUND_Y - 1 - (++level);
            GotoXY(centerX - size, y);
            for (int j = 0; j < width; ++j) cout << DISK_CHAR;
            temp.pop();
        }
    }
}

void AnimateMove(int from, int to, Disk disk) {
   }

void WaitForBackKey() {
    GotoXY((SCREEN_WIDTH - 30) / 2, SCREEN_HEIGHT - 2);
    cout << "Натисніть Enter щоб повернутись...";
    while (_getch() != 13) {}
}

void LoadFileAndPrint(const string& filename) {
    ClearScreen();
    ifstream file(filename);
    if (!file) {
        cerr << "Не вдалося відкрити файл: " << filename << endl;
        WaitForBackKey();
        return;
    }
    string line;
    int y = 5;
    while (getline(file, line)) {
        GotoXY((SCREEN_WIDTH - line.length()) / 2, y++);
        cout << line;
    }
    file.close();
    WaitForBackKey();
}

int NavigateMenu(const vector<string>& items, int startY) {
    int selected = 0;
    while (true) {
        for (int i = 0; i < items.size(); ++i) {
            GotoXY((SCREEN_WIDTH - items[i].length()) / 2, startY + i * 2);
            if (i == selected) {
                SetConsoleTextAttribute(GetStdHandle(STD_OUTPUT_HANDLE), FOREGROUND_GREEN | FOREGROUND_INTENSITY);
                cout << "> " << items[i];
                SetConsoleTextAttribute(GetStdHandle(STD_OUTPUT_HANDLE), FOREGROUND_RED | FOREGROUND_GREEN | FOREGROUND_BLUE);
            } else {
                cout << "  " << items[i];
            }
        }
        int key = _getch();
        if (key == 224) {
            key = _getch();
            if (key == 72 && selected > 0)
                --selected;
            else if (key == 80 && selected < items.size() - 1)
                ++selected;
        } else if (key == 13  key == 10) {
            return selected + 1;
        }
    }
}

void AutoMove(int n, int from, int to, int aux) {
    if (n == 0) return;
    AutoMove(n - 1, from, aux, to);
    towers[to].push(towers[from].top());
    towers[from].pop();
    DrawTowersVisual();
    this_thread::sleep_for(chrono::milliseconds(600));
    AutoMove(n - 1, aux, to, from);
}

void GameMenu() {
    ClearScreen();
    GotoXY((SCREEN_WIDTH - 30) / 2, 7);
    cout << "Введіть кількість дисків (1-9): ";
    cin >> diskCount;
    if (diskCount < 1  diskCount > 9) diskCount = 3;

    for (auto& tower : towers) while (!tower.empty()) tower.pop();
    for (int i = diskCount; i >= 1; --i) towers[0].push({ i });

    vector<string> items = {
        "Ручний режим",
        "Автоматичний режим",
        "Назад"
    };

    int choice = NavigateMenu(items, 11);
    if (choice == 1) {
       } else if (choice == 2) {
            }
}

void MainMenu() {
    while (isRunning) {
        ClearScreen();
        GotoXY((SCREEN_WIDTH - 20) / 2, 5);
        cout << "=== ГОЛОВНЕ МЕНЮ ===";

        vector<string> items = {
            "Почати гру",
            "Правила гри",
            "Інформація про автора",
            "Вихід"
        };

        int choice = NavigateMenu(items, 8);

        switch (choice) {
            case 1:
                GameMenu();
                break;
            case 2:
                LoadFileAndPrint("rules.txt");
                break;
            case 3:
                LoadFileAndPrint("author.txt");
                break;
            case 4:
                isRunning = false;
                break;
        }
    }
}

int main() {
    SetConsoleOutputCP(1251);
    SetConsoleCP(1251);

    HANDLE hOut = GetStdHandle(STD_OUTPUT_HANDLE);
    SMALL_RECT windowSize = {0, 0, SCREEN_WIDTH - 1, SCREEN_HEIGHT - 1};
    SetConsoleWindowInfo(hOut, TRUE, &windowSize);

    CONSOLE_CURSOR_INFO cursorInfo;
    GetConsoleCursorInfo(hOut, &cursorInfo);
    cursorInfo.bVisible = FALSE;
    SetConsoleCursorInfo(hOut, &cursorInfo);

    MainMenu();
    return 0;
}
