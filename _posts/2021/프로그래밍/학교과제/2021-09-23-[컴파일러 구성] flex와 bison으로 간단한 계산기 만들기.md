---
title:  "[컴파일러 구성] flex와 bison으로 간단한 계산기 만들기"
excerpt: "flex와 bison으로 사칙연산이 가능한 간단한 계산기 만들어봅시다."

categories:
  - 학교과제

tags:
  - 컴파일러 구성
  - 학교과제

last_modified_at: 2021-09-23T23:57:00+09:00
---

## 1. 문제 제시

사칙연산이 가능한 코드를 미리 제시하고, 이를 기반으로 새로운 연산을 추가하는 문제.
추가해야 하는 연산은, 나머지 연산(%)과 지수연산(^)이다.

소스코드 파일은 flex파일인 .l 파일과, bison파일인 .y 파일이 있다.

간단히 설명하자면,
flex 파일에서는 토큰을 구분하는 역할을 수행한다.
bison 파일에서는 flex에서 구분한 토큰을 기반으로, 파일에서 정의한 정규문법을 처리하는 역할을 수행하게 된다.

## 2. 풀이 코드

- flex 파일 (04-2.l)

```c

%{
// 구문분석기(bison)과 연결하기 위한 파일. 구문분석기를 실행하면 자동 생성됨.
#include "04-2.tab.h"
%}

%%
// 각 연산자 기호가 토큰이 되고, 이를 인식하면 특정 문자열을 반환한다.
"+" { return ADD; }
"-" { return SUB; }
"*" { return MUL; }
"/" { return DIV; }
"%" { return REM; }
"^" { return IND; }
[0-9]+ { yylval = atoi(yytext);
	return NUMBER;}
\n { return EOL; } 
[ \t] { }
. { printf("Undefined %c\n", *yytext); }

%%


```

- bison 파일 (04-2.y)

```c

%{
int yylex(void);
#include <stdio.h>
#include <math.h> // 지수 연산(pow())을 수행하기 위한 헤더파일
%}
// flex에서 반환한 문자열을 토큰으로써 사용
%token NUMBER
%token ADD SUB MUL DIV REM IND
%token EOL
%%
// 정규문법을 정의한다. 계산기 동작을 구현하였다.
calclist:
	| calclist exp EOL { printf("= %d\n", $2); }
	;
exp: factor
   | exp ADD factor { $$ = $1 + $3; }
   | exp SUB factor { $$ = $1 - $3; }
   ;
factor: term
      | factor MUL term { $$ = $1 * $3; }
      | factor DIV term { $$ = $1 / $3; }
      | factor REM term { $$ = $1 % $3; } // 나머지 연산 수행
      | factor IND term { $$ = pow($1,$3); } // 지수 연산 수행
      ;
term: NUMBER
;
%%
int main(int argc, char **argv)
{
return yyparse();
}
yyerror(char *s)
{
fprintf(stderr, "error: %s\n",s);
}


```

## 3. 풀이 방법

### 3.1. 나머지 연산(%)을 추가하기

1. flex 파일(04-2.l)에서 토큰을 찾으면, 특정 문자열을 반환하는 문장을 추가한다.

  ```c
  "%" { return REM; }
  ```
2. bison 파일(04-2.y)에서 나머지 연산을 처리하는 문법을 추가한다.

  ```c
  factor REM term { $$ = $1 % $3 }
  ```

### 3.2. 지수 연산(^)을 추가하기

1. flex 파일(04-2.l)에서 토큰을 찾으면, 특정 문자열을 반환하는 문장을 추가한다.

  ```c
  "^" { return IND; }
  ```

2. bison 파일(04-2.y)에서 지수 연산을 처리하는 문법을 추가한다.
	
	- math.h를 정의부분안에 include한다.
	(pow()는 math.h에 들어있다.)
	- 문법 부분에 지수 연산을 처리하는 문법을 추가한다.
	(^연산은 문법에서 먹히지 않으므로, pow()로 대체한다.)
	```c
 	factor IND term { $$ = pow($1, $3); }
	```
3. gcc 컴파일 시, -lm 옵션을 추가한다. (수학라이브러리를 연결하기 위함)
(출처: https://stackoverflow.com/questions/8671366/undefined-reference-to-pow-and-floor)

```bash
$ gcc lex.yy.c 04-2.tab.c -lfl -lm -o 04-2
```

## 4. flex와 bison 코드 컴파일하기

- 코드를 컴파일하기 위한 명령어

```bash

$ bison -d [파일이름.y]
$ flex [파일이름.l]
$ gcc lex.yy.c [파일이름.tab.c] -lfl -o [오브젝트이름] 
# -o 옵션은 선택사항이다. 다만, 지정하지 않으면 a.out으로 나옴

```

- 위 명령어를 이용해, 이번코드를 컴파일하기 위해 다음과 같이 입력하면 된다.

```bash

$ bison -d 04-2.y
$ flex 04-2.l
$ gcc lex.yy.c 04-2.tab.c -lfl -o 04-2

```

출처: 한림대학교 컴파일러 구성 강의