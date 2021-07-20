---
title:  "[PWN] pwnable.kr collision 문제풀이"
excerpt: "pwnable.kr collision 문제를 풀어봅시다."

categories:
  - PWN

tags:
  - PWN
  - Collision

last_modified_at: 2021-07-20T09:39:00+09:00
---

## 1. 문제 풀이 환경 접속하기
ssh를 통해, 문제 풀이 환경에 접속 할 수 있다. 접속을 하면, 아래 사진과 같은 화면이 출력된다.

또한, 'ls'명령을 통해, 디렉토리에 소스코드가 있음을 알 수 있다.


![사진1. 문제풀이 환경 접속하기](https://github.com/op2gs2/op2gs2.github.io/blob/main/assets/images/2021/PWN/pwmable.kr%20collision%20%EB%AC%B8%EC%A0%9C%ED%92%80%EC%9D%B4/1.png?raw=true)


## 2. 소스코드 살펴보기

### 2.1. 소스코드 들어가보기

소스코드에 들어가면, 다음과 같이 소스코드를 볼 수 있다.

![사진2. 문제 소스코드](https://github.com/op2gs2/op2gs2.github.io/blob/main/assets/images/2021/PWN/pwmable.kr%20collision%20%EB%AC%B8%EC%A0%9C%ED%92%80%EC%9D%B4/2.png?raw=true)

```c
#include <stdio.h>
#include <string.h>
unsigned long hashcode = 0x21DD09EC;
unsigned long check_password(const char* p){
        int* ip = (int*)p;
        int i;
        int res=0;
        for(i=0; i<5; i++){
                res += ip[i];
        }
        return res;
}

int main(int argc, char* argv[]){
        if(argc<2){
                printf("usage : %s [passcode]\n", argv[0]);
                return 0;
        }
        if(strlen(argv[1]) != 20){
                printf("passcode length should be 20 bytes\n");
                return 0;
        }

        if(hashcode == check_password( argv[1] )){
                system("/bin/cat flag");
                return 0;
        }
        else
                printf("wrong passcode.\n");
        return 0;
}
```

### 2.2. 소스코드 분석하기

해당 문제는 flag를 얻는 것이 목적이다.
따라서, flag가 있는 파일을 여는 코드를 실행 시키는 것이 목적이라 하겠다.

즉,

아래의 조건문을 만족하는 형태가 되어야 한다.

```c
if(hashcode == check_password( argv[1] )){
        system("/bin/cat flag");
        return 0;
}
```

그래서, check_password()의 코드를 보면 다음과 같다.

```c
unsigned long check_password(const char* p){
        int* ip = (int*)p;
        int i;
        int res=0;
        for(i=0; i<5; i++){
                res += ip[i];
        }
        return res;
}
```

이 코드는, char* 로 정의된 매개변수 p를 int형으로 바꾼다.
그 다음, int형에 저장된 변수를 5번에 걸처서 가져오고 저장하게 된다. 만약, 20바이트 만큼의 입력이 주어지면, 1번 실행때 마다 4바이트씩 가져오게 된다. 
마지막으로 그 값을 반환하게 된다.


## 3. 문제 풀어보기

### 3.1. 내가 시도했던 방법

처음에 나는, ASCII 코드를 통해 값에 접근하려고 했다.
A를 입력하면, 65라는 값이 입력되는 식이다. ASCII 값을 조합하여, hashcode인 0x21DD09EC에 맞추려고 했다.

그리고, AA를 입력하면 65*65=4225가 나올 것으로 예상했다.
하지만, 실제 코드에서는 달랐다. 실제로는 16705라는 값이 나왔는데, A의 ASCII인 65를 이진수로 두번 가져다 놓으면 그런 값이 나오기 때문이다.

![사진3. 두 개의 ASCII코드 값](https://github.com/op2gs2/op2gs2.github.io/blob/main/assets/images/2021/PWN/pwmable.kr%20collision%20%EB%AC%B8%EC%A0%9C%ED%92%80%EC%9D%B4/3.png?raw=true)

이런 방법으론 문제가 있었다. hashcode보다 너무 많이 커지는 것이였다. 또한, 문제에서는 20바이트 이상의 입력을 요구하고 있었으니(if(strlen(argv[1]) != 20)), 해당 방법으론 절대 정답을 도출해낼 수 없었다.

### 3.2. 검색을 통해 알아본 방법

그래서, 검색을 하였고, 문제 푸는 방법을 알아낼 수 있었다.
- (자세한 사항은 [이곳](https://m.blog.naver.com/timtaeil/221311968675)을 확인하기 바란다.)

검색을 통해 내가 몰랐던 사실이 있는데, char는 빅엔디언방식으로 저장되고, int는 리틀엔디언방식으로 저장된다는 것이다. 따라서, char->int로 형변환이 일어날때는, 리틀엔디언으로 바뀌게 되며, 데이터의 순서 또한 바뀌게 된다.

그리고 20바이트의 문제를 해결하는 방법이 있었다. 이 식대로 입력을 해주면 된다. 이렇게 하면, 값과 입력 조건(20바이트)을 만족할 수 있게 된다.

> ((hashcode를 5로 나눈 몫) * 4) + (hashcode - ((hashcode를 5로 나눈 몫) * 4))

여기서 몫을 4번 곱한 이유는, hashcode로 나눴을 때 나머지가 존재하기 때문이다. 따라서, 별도의 계산값을 더해준 것이다.

### 3.3. 문제를 풓어봅시다.

1. hashcode가 되는 입력값 구하기

- hashcode인 0x21DD09EC를 5로 나눠본다.
- 그러면, 0x06C5CEC8이 나오게 된다.
![사진4. hashcode를 나눈 몫 구하기](https://github.com/op2gs2/op2gs2.github.io/blob/main/assets/images/2021/PWN/pwmable.kr%20collision%20%EB%AC%B8%EC%A0%9C%ED%92%80%EC%9D%B4/4.png?raw=true)

- 또한 hashcode에서 위의 계산 결과를 빼준다. 이는 계산 결과를 hashcode가 나오도록 하기 위함이다.
- 그러면, 0x06C5CECC가 나오게 된다.
![사진5. 나머지 값 구하기](https://github.com/op2gs2/op2gs2.github.io/blob/main/assets/images/2021/PWN/pwmable.kr%20collision%20%EB%AC%B8%EC%A0%9C%ED%92%80%EC%9D%B4/5.png?raw=true)

2. 구한 입력값을 대입하여, flag를 얻어내기

- 1.에서 구한 값을 파이썬 스크립트를 이용해, 대입해본다.

![사진6. flag 구하기](https://github.com/op2gs2/op2gs2.github.io/blob/main/assets/images/2021/PWN/pwmable.kr%20collision%20%EB%AC%B8%EC%A0%9C%ED%92%80%EC%9D%B4/6.png?raw=true)

- 그려면, flag를 얻어낼 수 있다.

## 4. 여기서 배운 점

- char형은 빅엔디언을, int형은 리틀엔디언으로 저장된다.
- 이 둘 사이에 형변환이 일어날 때, 데이터의 순서도 변경된다.
- ASCII 코드 여러개를 같이 놓으면, 2진수 값으로 같이 놓게 된다. 즉, AA는 10진수로 65 65가 되는 것이 아닌, "0100 0001 0100 0001"와 같이 놓이게 되며, 10진수값도 이 값을 변형한 값이 된다.