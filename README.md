#include <stdio.h>

void main(){
    int N, i, j;
    scanf_s("%d", &N);

    // 위쪽
    for (i = 0; i < N / 2 + 1; i++)
    {
        for (j = 0; j < i; j++)
            printf(" ");

        for (j = 0; j < N - 2 * i; j++)
            printf("*");
        printf("\n");
    }

    // 아래쪽
    for (i = N / 2 - 1; i >= 0; i--)
    {
        for (j = 0; j < i; j++)
            printf(" ");

        for (j = 0; j < N - 2 * i; j++)
            printf("*");
        printf("\n");
    }
}
