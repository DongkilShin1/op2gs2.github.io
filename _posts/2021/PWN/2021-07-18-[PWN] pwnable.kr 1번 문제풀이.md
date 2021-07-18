---
title:  "[PWN] pwnable.kr 1번 문제풀이"
excerpt: "pwnable.kr 1번 문제를 풀어봅시다."

categories:
  - PWN

tags:
  - PWN
  - File Descriptor

last_modified_at: 2021-07-18T11:25:00+09:00
---

## 1. 문제 풀이 환경 접속하기
ssh를 통해, 문제 풀이 환경에 접속 할 수 있다. 접속을 하면, 아래 사진과 같은 화면이 출력된다.

![사진1. 문제풀이 환경 접속하기](https://github.com/op2gs2/op2gs2.github.io/blob/main/assets/images/2021/PWN/pwnable.kr%201%EB%B2%88%20%EB%AC%B8%EC%A0%9C%ED%92%80%EC%9D%B4/1.png?raw=true)


## 2. 소스코드 살펴보기

### 2.1. 소스코드 들어가보기
'ls'명령을 통해, 디렉터리에 무엇이 있는지 살펴보자. 소스코드가 있다. 출제자의 의도는 소스코드를 보고 문제를 풀이하라는 것 같다. 소스코드로 들어가보자.

![사진2. 소스코드 들어가보기](https://github.com/op2gs2/op2gs2.github.io/blob/main/assets/images/2021/PWN/pwnable.kr%201%EB%B2%88%20%EB%AC%B8%EC%A0%9C%ED%92%80%EC%9D%B4/2.png?raw=false)


위 명령어를 사용해서 소스코드에 들어가면, 다음과 같이 소스코드를 볼 수 있다.

![사진3. 문제 소스코드](https://github.com/op2gs2/op2gs2.github.io/blob/main/assets/images/2021/PWN/pwnable.kr%201%EB%B2%88%20%EB%AC%B8%EC%A0%9C%ED%92%80%EC%9D%B4/3.png?raw=true)

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
char buf[32];
int main(int argc, char* argv[], char* envp[]){
        if(argc<2){
                printf("pass argv[1] a number\n");
                return 0;
        }
        int fd = atoi( argv[1] ) - 0x1234;
        int len = 0;
        len = read(fd, buf, 32);
        if(!strcmp("LETMEWIN\n", buf)){
                printf("good job :)\n");
                system("/bin/cat flag");
                exit(0);
        }
        printf("learn about Linux file IO\n");
        return 0;

}
```

### 2.2. 소스코드 분석하기

해당 문제는 flag를 얻는 것이 목적이다.
따라서, flag가 있는 파일을 여는 코드를 실행 시키는 것이 목적이라 하겠다.

즉,
```c
if(!strcmp("LETMEWIN\n", buf)){
    printf("good job :)\n");
    system("/bin/cat flag");
    exit(0);
}
```
이 코드를 열도록, 위에서 조작해야 한다는 말이다. 
여기는 if문이 있는데, buf변수가 있고, strcmp로 비교를 한다. 이를 보아, 문자열 비교가 있고 문자열 입력이 있음을 유추할 수 있다.

그럼 해당 코드는 분석이 됐으니, 위로 올라가보자.

```c
int fd = atoi( argv[1] ) - 0x1234;
int len = 0;
len = read(fd, buf, 32);
```

이 코드의 첫줄부터 살펴보자. argv[1]은 프로그램을 실행하면서 주는 인자값을 의미한다. 이 값을 정수형으로 변경 후, 0x1234를 뺀다. 이 값이 fd값이 된다.

그 다음 줄은... 별 의미 없는 코드다. (밑에서도 호출되지 않기 때문이다.)

그리고 마지막 줄은, read() 함수이다.
fd 변수, File Descriptor를 참조하여 모드를 설정하고, 32바이트만큼 buffer에 읽어들이겠다는 뜻이 된다.
위에서 우리는 buf값을 입력해야 한다는 사실을 발견했다. 즉, 우리가 buf에 무언가를 쓰기 위해서는 fd값이 '0'이 되어야 한다. fd값이 0이 되어야 read()가 입력모드가 되기 때문이다. 자세한 사항은 [링크](https://z-man.tistory.com/151)를 참조해보길 바란다.



## 3. 문제 풀어보기

### 3.1. fd 계산하기
우리는 위에서 fd가 '0'이 되어야 한다는 것을 알았다.

fd가 '0'이 되기 위해서는, 16진수 값은 0x1234와 뺐을 때 0이 되어야 한다. 0x1234는 10진수로 4660이므로, 처음 입력 값은 4660이 되어야 한다.

![사진4. 0x1234 계산하기](https://github.com/op2gs2/op2gs2.github.io/blob/main/assets/images/2021/PWN/pwnable.kr%201%EB%B2%88%20%EB%AC%B8%EC%A0%9C%ED%92%80%EC%9D%B4/4.png?raw=true)

그래서 프로그램 실행 인자 값으로, 4660을 주고 프로그램을 실행하였다.

### 3.2. 문자열 비교

그 다음에는 "LETMEWIN"이라는 문자열을 입력해야 한다. 맨 처음에서 보았듯이, 해당 문자열이 맞는지 확인하기 때문이다. 만약에 맞다면, flag를 보여주는 코드를 실행하기 때문이다.

![사진5. 프로그램 실행하기](https://github.com/op2gs2/op2gs2.github.io/blob/main/assets/images/2021/PWN/pwnable.kr%201%EB%B2%88%20%EB%AC%B8%EC%A0%9C%ED%92%80%EC%9D%B4/5.png?raw=true)

이렇게 프로그램을 실행하고 나니, "mommy! I think I know what a file descriptor is!!"라는 flag를 얻을 수 있었다.

## 4. 여기서 배운 점

- File Descriptor는 파일 입출력의 동작을 결정하는 역할을 수행하기도 한다.
- 0이면 표준 입력을, 1이면 표준 출력을, 2이면 표준 에러라는 동작을 하도록 만든다.
(출처: https://z-man.tistory.com/151)