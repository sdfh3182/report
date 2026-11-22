1. scanf 및 문자열 역순 출력

#define _CRT_SECURE_NO_WARNINGS
#include <stdio.h>
#include <string.h>

int main(void) {
    // [수정] 문자열 배열 크기를 100에서 200으로 변경
    char str[200];

    printf("문장 입력: ");
    scanf("%199[^\n]", str);

    // 문자열을 거꾸로 출력
    printf("역순 출력: ");
    for (int i = (int)strlen(str) - 1; i >= 0; i--) {
        printf("%c", str[i]);
    }

    printf("\n");
    return 0;
}

2. 함수 포인터

#include <stdio.h>

// [수정] 덧셈 함수에 두 수를 더한 후 10을 추가
int add(int a, int b) {
    return a + b + 10;
}

int main(void) {
    int a = 11, b = 22;

    int (*sum)(int, int) = add;

    printf("계산 결과: %d\n", sum(a, b));

    return 0;
}

3. 2차원 배열

#include <stdio.h>

int main(void) {
    // [수정] 기존 3행 4열에서 4행 4열로 변경
    int a[4][4] = {
        {1, 2, 3, 4},
        {5, 6, 7, 8},
        {9, 10, 11, 12},
        {13, 14, 15, 16}
    };

    int i, j, s = 0;

    for (i = 0; i < 4; i++) {
        for (j = 0; j < 4; j++) {
            s += a[i][j];
        }
    }

    printf("배열 전체 합계: %d\n", s);

    return 0;
}

4. 구조체를 이용한 바이너리 파일 저장

#define _CRT_SECURE_NO_WARNINGS
#include <stdio.h>

struct S {
    int k, e, m;
};

int main(void) {
    // [수정] 학생을 2명에서 3명으로 변경
    struct S a[3] = {
        {80, 90, 85},
        {70, 75, 95},
        {95, 85, 90}
    };

    FILE* p = fopen("my.bin", "wb");

    if (p == NULL) {
        printf("파일 생성 실패\n");
        return 1;
    }

    fwrite(a, sizeof(struct S), 3, p);

    fclose(p);

    printf("3명의 성적을 my.bin에 저장했습니다.\n");

    return 0;
}

5. 바이너리 파일 읽기 및 평균 계산

#define _CRT_SECURE_NO_WARNINGS
#include <stdio.h>

struct S {
    int k, e, m;
};

int main(void) {
    // [수정] 4번에서 저장한 학생 3명의 성적을 읽음
    struct S a[3];

    FILE* p = fopen("my.bin", "rb");

    if (p == NULL) {
        printf("파일을 열 수 없습니다.\n");
        return 1;
    }

    int count = (int)fread(a, sizeof(struct S), 3, p);
    fclose(p);

    if (count != 3) {
        printf("파일 데이터가 올바르지 않습니다.\n");
        return 1;
    }

    // 개인별 평균
    for (int i = 0; i < 3; i++) {
        double avg = (a[i].k + a[i].e + a[i].m) / 3.0;
        printf("개인%d 평균: %.2f\n", i + 1, avg);
    }

    // 과목별 평균
    printf("국어 평균: %.2f\n",
        (a[0].k + a[1].k + a[2].k) / 3.0);

    printf("영어 평균: %.2f\n",
        (a[0].e + a[1].e + a[2].e) / 3.0);

    printf("수학 평균: %.2f\n",
        (a[0].m + a[1].m + a[2].m) / 3.0);

    return 0;
}

6. 버블 정렬

#include <stdio.h>

int main(void) {
    // [수정] 기존 {2, 4, 1, 5, 3}에서 숫자 변경
    int a[] = {9, 4, 7, 2, 6, 1};
    int n = sizeof(a) / sizeof(a[0]);
    int i, j, t;

    for (i = 0; i < n - 1; i++) {
        for (j = 0; j < n - 1 - i; j++) {
            if (a[j] > a[j + 1]) {
                t = a[j];
                a[j] = a[j + 1];
                a[j + 1] = t;
            }
        }
    }

    printf("오름차순 정렬 결과: ");

    for (i = 0; i < n; i++) {
        printf("%d ", a[i]);
    }

    printf("\n");

    return 0;
}

7. 정수형 변수 2개의 합계

#include <stdio.h>

int main(void) {
    // [수정] 기존 22와 33에서 45와 55로 변경
    int a = 45;
    int b = 55;

    int sum = a + b;

    printf("첫 번째 숫자: %d\n", a);
    printf("두 번째 숫자: %d\n", b);
    printf("두 수의 합: %d\n", sum);

    return 0;
}

8. 명령행 인자를 이용한 두 정수의 합

#include <stdio.h>
#include <stdlib.h>

int main(int argc, char* argv[]) {
    // [수정] 인자가 2개가 아닌 경우 사용법 출력
    if (argc != 3) {
        printf("사용법: 프로그램명 숫자1 숫자2\n");
        return 1;
    }

    int a = atoi(argv[1]);
    int b = atoi(argv[2]);

    int sum = a + b;

    printf("%d + %d = %d\n", a, b, sum);

    return 0;
}

9. 반복문을 이용한 5부터 10까지의 합

#include <stdio.h>

int main(void) {
    // [수정] 기존 for문 대신 while문 사용
    int i = 5;
    int sum = 0;

    while (i <= 10) {
        sum += i;
        i++;
    }

    printf("5부터 10까지의 합: %d\n", sum);

    return 0;
}

10. 동적 메모리 할당

#include <stdio.h>
#include <stdlib.h>

int main(void) {
    // [수정] 정수 3개 대신 정수 5개 공간 할당
    int* p = (int*)malloc(5 * sizeof(int));

    if (p == NULL) {
        printf("메모리 할당 실패\n");
        return 1;
    }

    // 기존 숫자 3개에서 5개로 변경
    p[0] = 11;
    p[1] = 33;
    p[2] = 55;
    p[3] = 77;
    p[4] = 99;

    int sum = 0;

    for (int i = 0; i < 5; i++) {
        sum += p[i];
    }

    printf("저장된 숫자: ");

    for (int i = 0; i < 5; i++) {
        printf("%d ", p[i]);
    }

    printf("\n전체 합계: %d\n", sum);

    // 동적 메모리 해제
    free(p);

    return 0;
}

11. 구조체를 이용한 3명의 성적 및 평균 계산

#include <stdio.h>

// [수정] 이름도 함께 저장할 수 있도록 구조체 확장
struct Student {
    char name[20];
    int kr;
    int en;
};

int main(void) {
    // [수정] 학생 3명의 이름과 성적 초기화
    struct Student s[3] = {
        {"J.C. Shim", 90, 85},
        {"G.D. Hong", 80, 95},
        {"J.Y. Ko", 85, 90}
    };

    // 개인별 평균 계산
    for (int i = 0; i < 3; i++) {
        double avg = (s[i].kr + s[i].en) / 2.0;

        printf("%s 평균: %.2f\n",
            s[i].name, avg);
    }

    return 0;
}

### 2036년 11월 11일, 수요일

오늘은 문득 10년 전의 내가 생각나는 하루였다.

10년 전 나는 대학교에서 컴퓨터공학을 공부하며 프로그래밍을 배우고 있었다. 당시에는 C언어의 기초 문법조차 어렵게 느껴졌고, 코드를 작성하다가 오류가 발생하면 어떻게 해결해야 할지 몰라 막막했던 기억이 난다. 앞으로 어떤 직업을 가져야 할지, 어떤 분야에서 일하고 싶은지도 확실하게 정하지 못한 상태였다.

하지만 지금의 나는 IT 분야에서 소프트웨어 개발자로 일하고 있다. 처음에는 간단한 프로그램을 만드는 것부터 시작했지만, 다양한 프로젝트와 경험을 쌓으면서 점차 실력을 키울 수 있었다. 특히 내가 직접 작성한 프로그램이 실제로 사람들에게 도움이 되고, 누군가에게 유용하게 사용된다는 사실이 무척 뿌듯하다.

물론 지금까지 모든 과정이 순탄했던 것은 아니다. 새로운 기술을 배워야 할 때마다 어려움을 겪었고, 프로젝트가 예상대로 진행되지 않아 밤늦게까지 고민했던 날도 많았다. 그래도 포기하지 않고 꾸준히 공부했던 과거의 경험들이 지금의 나를 만들었다고 생각한다.

앞으로도 현재에 만족하지 않고 새로운 기술에 도전하고 싶다. 언젠가는 내가 직접 기획하고 개발한 프로그램으로 많은 사람에게 도움을 주는 것이 또 하나의 목표다.



오늘은 10년 전의 나에게 고맙다는 말을 전하고 싶다. 비록 당시에는 미래가 불확실했지만, 포기하지 않고 한 걸음씩 나아갔기 때문에 지금의 내가 있을 수 있었다.

2036년 11월 11일, 오늘도 더 나은 미래를 위해 노력하는 나에게.
