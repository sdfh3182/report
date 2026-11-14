### C언어의 배열과 구조체를 활용해 테트리스 게임의 규칙을 직접 구현하면서 프로그래밍의 원리를 깊이 이해할 수 있었습니다. 
### 특히 Raylib 라이브러리의 직관적인 함수들을 통해 복잡한 그래픽 설정 없이도 빠르고 재미있게 2D 게임을 완성할 수 있어 흥미로웠습니다.
### 에러를 해결하고 코드를 다듬는 과정에서 개발의 어려움과 성취감을 동시에 느낄 수 있었던 뜻깊은 과제였습니다.



#include "raylib.h"
#include <stdlib.h>
#include <time.h>

#define BOARD_WIDTH 10
#define BOARD_HEIGHT 20
#define BLOCK_SIZE 30

#define SCREEN_WIDTH (BOARD_WIDTH * BLOCK_SIZE + 200)
#define SCREEN_HEIGHT (BOARD_HEIGHT * BLOCK_SIZE)

// Tetromino shapes definition (7 pieces, 4 rotations)
int tetrominos[7][4][4][4] = {
    // I piece
    {{{0,0,0,0},{1,1,1,1},{0,0,0,0},{0,0,0,0}},
     {{0,0,1,0},{0,0,1,0},{0,0,1,0},{0,0,1,0}},
     {{0,0,0,0},{0,0,0,0},{1,1,1,1},{0,0,0,0}},
     {{0,1,0,0},{0,1,0,0},{0,1,0,0},{0,1,0,0}}},
     // J piece
     {{{1,0,0,0},{1,1,1,0},{0,0,0,0},{0,0,0,0}},
      {{0,1,1,0},{0,1,0,0},{0,1,0,0},{0,0,0,0}},
      {{0,0,0,0},{1,1,1,0},{0,0,1,0},{0,0,0,0}},
      {{0,1,0,0},{0,1,0,0},{1,1,0,0},{0,0,0,0}}},
      // L piece
      {{{0,0,1,0},{1,1,1,0},{0,0,0,0},{0,0,0,0}},
       {{0,1,0,0},{0,1,0,0},{0,1,1,0},{0,0,0,0}},
       {{0,0,0,0},{1,1,1,0},{1,0,0,0},{0,0,0,0}},
       {{1,1,0,0},{0,1,0,0},{0,1,0,0},{0,0,0,0}}},
       // O piece
       {{{0,1,1,0},{0,1,1,0},{0,0,0,0},{0,0,0,0}},
        {{0,1,1,0},{0,1,1,0},{0,0,0,0},{0,0,0,0}},
        {{0,1,1,0},{0,1,1,0},{0,0,0,0},{0,0,0,0}},
        {{0,1,1,0},{0,1,1,0},{0,0,0,0},{0,0,0,0}}},
        // S piece
        {{{0,1,1,0},{1,1,0,0},{0,0,0,0},{0,0,0,0}},
         {{0,1,0,0},{0,1,1,0},{0,0,1,0},{0,0,0,0}},
         {{0,0,0,0},{0,1,1,0},{1,1,0,0},{0,0,0,0}},
         {{1,0,0,0},{1,1,0,0},{0,1,0,0},{0,0,0,0}}},
         // T piece
         {{{0,1,0,0},{1,1,1,0},{0,0,0,0},{0,0,0,0}},
          {{0,1,0,0},{0,1,1,0},{0,1,0,0},{0,0,0,0}},
          {{0,0,0,0},{1,1,1,0},{0,1,0,0},{0,0,0,0}},
          {{0,1,0,0},{1,1,0,0},{0,1,0,0},{0,0,0,0}}},
          // Z piece
          {{{1,1,0,0},{0,1,1,0},{0,0,0,0},{0,0,0,0}},
           {{0,0,1,0},{0,1,1,0},{0,1,0,0},{0,0,0,0}},
           {{0,0,0,0},{1,1,0,0},{0,1,1,0},{0,0,0,0}},
           {{0,1,0,0},{1,1,0,0},{1,0,0,0},{0,0,0,0}}}
};

Color pieceColors[7]; // Declared globally without initialization

int board[BOARD_HEIGHT][BOARD_WIDTH] = { 0 };

typedef struct {
    int type;
    int rotation;
    int x, y;
} Tetromino;

Tetromino currentPiece;
float dropTimer = 0.0f;
float dropInterval = 0.5f;
int score = 0;
bool gameOver = false;

void SpawnNewPiece() {
    currentPiece.type = rand() % 7;
    currentPiece.rotation = 0;
    currentPiece.x = BOARD_WIDTH / 2 - 2;
    currentPiece.y = 0;

    for (int r = 0; r < 4; r++) {
        for (int c = 0; c < 4; c++) {
            if (tetrominos[currentPiece.type][currentPiece.rotation][r][c]) {
                if (board[currentPiece.y + r][currentPiece.x + c]) {
                    gameOver = true;
                }
            }
        }
    }
}

bool CheckCollision(int posX, int posY, int rot) {
    for (int r = 0; r < 4; r++) {
        for (int c = 0; c < 4; c++) {
            if (tetrominos[currentPiece.type][rot][r][c]) {
                int boardX = posX + c;
                int boardY = posY + r;

                if (boardX < 0 || boardX >= BOARD_WIDTH || boardY >= BOARD_HEIGHT)
                    return true;
                if (boardY >= 0 && board[boardY][boardX])
                    return true;
            }
        }
    }
    return false;
}

void MergePiece() {
    for (int r = 0; r < 4; r++) {
        for (int c = 0; c < 4; c++) {
            if (tetrominos[currentPiece.type][currentPiece.rotation][r][c]) {
                int boardX = currentPiece.x + c;
                int boardY = currentPiece.y + r;
                if (boardY >= 0) {
                    board[boardY][boardX] = currentPiece.type + 1;
                }
            }
        }
    }
}

void ClearLines() {
    int linesCleared = 0;
    for (int r = BOARD_HEIGHT - 1; r >= 0; r--) {
        bool full = true;
        for (int c = 0; c < BOARD_WIDTH; c++) {
            if (board[r][c] == 0) {
                full = false;
                break;
            }
        }
        if (full) {
            linesCleared++;
            for (int moveR = r; moveR > 0; moveR--) {
                for (int c = 0; c < BOARD_WIDTH; c++) {
                    board[moveR][c] = board[moveR - 1][c];
                }
            }
            for (int c = 0; c < BOARD_WIDTH; c++) {
                board[0][c] = 0;
            }
            r++;
        }
    }
    if (linesCleared > 0) {
        score += linesCleared * 100;
    }
}

void ResetGame() {
    for (int r = 0; r < BOARD_HEIGHT; r++) {
        for (int c = 0; c < BOARD_WIDTH; c++) {
            board[r][c] = 0;
        }
    }
    score = 0;
    gameOver = false;
    SpawnNewPiece();
}

int main(void) {
    srand((unsigned int)time(NULL));
    InitWindow(SCREEN_WIDTH, SCREEN_HEIGHT, "Raylib Tetris in C");
    SetTargetFPS(60);

    // Initialize piece colors inside main (runtime initialization)
    pieceColors[0] = SKYBLUE;
    pieceColors[1] = BLUE;
    pieceColors[2] = ORANGE;
    pieceColors[3] = YELLOW;
    pieceColors[4] = GREEN;
    pieceColors[5] = PURPLE;
    pieceColors[6] = RED;

    ResetGame();

    while (!WindowShouldClose()) {
        float dt = GetFrameTime();

        if (!gameOver) {
            if (IsKeyPressed(KEY_LEFT)) {
                if (!CheckCollision(currentPiece.x - 1, currentPiece.y, currentPiece.rotation))
                    currentPiece.x--;
            }
            if (IsKeyPressed(KEY_RIGHT)) {
                if (!CheckCollision(currentPiece.x + 1, currentPiece.y, currentPiece.rotation))
                    currentPiece.x++;
            }
            if (IsKeyPressed(KEY_UP)) {
                int nextRot = (currentPiece.rotation + 1) % 4;
                if (!CheckCollision(currentPiece.x, currentPiece.y, nextRot))
                    currentPiece.rotation = nextRot;
            }
            if (IsKeyDown(KEY_DOWN)) {
                dropInterval = 0.05f;
            }
            else {
                dropInterval = 0.5f;
            }

            dropTimer += dt;
            if (dropTimer >= dropInterval) {
                dropTimer = 0.0f;
                if (!CheckCollision(currentPiece.x, currentPiece.y + 1, currentPiece.rotation)) {
                    currentPiece.y++;
                }
                else {
                    MergePiece();
                    ClearLines();
                    SpawnNewPiece();
                }
            }
        }
        else {
            if (IsKeyPressed(KEY_ENTER)) {
                ResetGame();
            }
        }

        BeginDrawing();
        ClearBackground(RAYWHITE);

        for (int r = 0; r < BOARD_HEIGHT; r++) {
            for (int c = 0; c < BOARD_WIDTH; c++) {
                if (board[r][c]) {
                    DrawRectangle(c * BLOCK_SIZE, r * BLOCK_SIZE, BLOCK_SIZE - 1, BLOCK_SIZE - 1, pieceColors[board[r][c] - 1]);
                }
                else {
                    DrawRectangleLines(c * BLOCK_SIZE, r * BLOCK_SIZE, BLOCK_SIZE, BLOCK_SIZE, LIGHTGRAY);
                }
            }
        }

        if (!gameOver) {
            for (int r = 0; r < 4; r++) {
                for (int c = 0; c < 4; c++) {
                    if (tetrominos[currentPiece.type][currentPiece.rotation][r][c]) {
                        int drawX = (currentPiece.x + c) * BLOCK_SIZE;
                        int drawY = (currentPiece.y + r) * BLOCK_SIZE;
                        if (drawY >= 0) {
                            DrawRectangle(drawX, drawY, BLOCK_SIZE - 1, BLOCK_SIZE - 1, pieceColors[currentPiece.type]);
                        }
                    }
                }
            }
        }

        DrawText(TextFormat("SCORE:\n%d", score), BOARD_WIDTH * BLOCK_SIZE + 20, 40, 20, DARKGRAY);

        if (gameOver) {
            DrawRectangle(0, SCREEN_HEIGHT / 2 - 50, SCREEN_WIDTH, 100, Fade(BLACK, 0.8f));
            DrawText("GAME OVER", SCREEN_WIDTH / 2 - MeasureText("GAME OVER", 20) / 2, SCREEN_HEIGHT / 2 - 20, 20, WHITE);
            DrawText("Press ENTER to Restart", SCREEN_WIDTH / 2 - MeasureText("Press ENTER to Restart", 10) / 2, SCREEN_HEIGHT / 2 + 10, 10, LIGHTGRAY);
        }

        EndDrawing();
    }

    CloseWindow();
    return 0;
}
