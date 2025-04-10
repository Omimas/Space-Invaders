# 👾 Space Invaders - C++ (Console Version)

A simple console-based implementation of the classic **Space Invaders** game written in C++. This version is text-based and does not use any graphics libraries, making it ideal for beginners who want to understand basic game logic and mechanics.

## 🎮 Features

- Move the player left and right using `A` / `D`
- Aliens move downward over time
- Shoot bullets with the `Spacebar`
- Destroy aliens by hitting them with bullets
- Simple game loop and collision detection implemented

## 🧰 Requirements

- C++11 or later compatible compiler (e.g., g++, MSVC)
- Works on Windows using `conio.h` and `windows.h`

## 🛠️ How to Compile and Run

### Windows (Using g++)

```bash
g++ space_invaders.cpp -o space_invaders
./space_invaders

#include <iostream>
#include <conio.h>
#include <windows.h>
#include <vector>

using namespace std;

const int width = 40;
const int height = 20;
char screen[height][width];

int playerX = width / 2;
vector<pair<int, int>> aliens;
vector<pair<int, int>> bullets;

bool gameOver = false;

void Setup() {
    // Add aliens
    for (int i = 0; i < 5; i++) {
        for (int j = 10; j <= 30; j += 5) {
            aliens.push_back({i, j});
        }
    }
}

void Draw() {
    system("cls");
    
    // Clear screen buffer
    for (int i = 0; i < height; i++)
        for (int j = 0; j < width; j++)
            screen[i][j] = ' ';

    // Draw player
    screen[height - 1][playerX] = 'A';

    // Draw aliens
    for (auto &alien : aliens)
        screen[alien.first][alien.second] = 'M';

    // Draw bullets
    for (auto &b : bullets)
        screen[b.first][b.second] = '|';

    // Print screen
    for (int i = 0; i < height; i++) {
        for (int j = 0; j < width; j++)
            cout << screen[i][j];
        cout << endl;
    }
}

void Input() {
    if (_kbhit()) {
        char key = _getch();
        if (key == 'a' || key == 'A')
            if (playerX > 0) playerX--;
        if (key == 'd' || key == 'D')
            if (playerX < width - 1) playerX++;
        if (key == ' ')
            bullets.push_back({height - 2, playerX});
    }
}

void Logic() {
    // Move bullets
    for (int i = 0; i < bullets.size(); i++) {
        bullets[i].first--;
        if (bullets[i].first < 0) {
            bullets.erase(bullets.begin() + i);
            i--;
        }
    }

    // Move aliens down every 20 frames
    static int alienMoveTimer = 0;
    alienMoveTimer++;
    if (alienMoveTimer >= 20) {
        for (auto &a : aliens)
            a.first++;
        alienMoveTimer = 0;
    }

    // Bullet-Alien collision
    for (int i = 0; i < bullets.size(); i++) {
        for (int j = 0; j < aliens.size(); j++) {
            if (bullets[i] == aliens[j]) {
                bullets.erase(bullets.begin() + i);
                aliens.erase(aliens.begin() + j);
                i--;
                break;
            }
        }
    }

    // Check if any alien reached player line
    for (auto &a : aliens) {
        if (a.first >= height - 1)
            gameOver = true;
    }

    // Win condition
    if (aliens.empty())
        gameOver = true;
}

int main() {
    Setup();
    while (!gameOver) {
        Draw();
        Input();
        Logic();
        Sleep(100); // Slow down game loop
    }

    cout << "\nGame Over!\n";
    system("pause");
    return 0;
}
