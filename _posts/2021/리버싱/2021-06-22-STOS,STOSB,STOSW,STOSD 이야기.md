---
title:  "[리버싱] STOS / STOSB / STOSW / STOSD 이야기"
excerpt: "STOS / STOSB / STOSW / STOSD에 대해 알아보자."

categories: 리버싱


tags:
    - STOS
    - STOSB
    - STOSW
    - STOSD

last_modified_at: 2021-06-22T16:15:00+09:00
---


STOS / STOSB / STOSW / STOSD 명령은 문자별 테마를 지정된 주소에 저장하는 역할을 수행합니다.



일부 명령을 자세히 살펴보면 다음과 같습니다:

|명령어|설명|
|:---:|:------:|
|STOSB| AL을 ES: (E)DI 주소에 저장|
|STOSB| AX를 ES: (E)DI 주소에 저장|
|STOSB|EAX를 ES: (E)DI 주소에 저장|



BYTE, WORD, DOUBLE BYTE를 각각 AL, AX, EAX 저장 Operand에 저장합니다.

저장 Operand는 메모리 주소에 있고, 이 주소는 (명령의 주소 체계, 즉 32 혹은 16비트에 따라서) 각각 ES: EDI 혹은 ES: DI에서 읽을 수 있게 됩니다. ES 세그먼트는 세그먼트 Override Prefix로 Override 할 수 없습니다. 어셈블리 코드 수준에서는, 이 명령의 Operand 지정 형식과 Operand 없음의 2가지 형식이 사용될 수 있습니다. Operand 지정 형식 (STOS Mnemonic에서 지정합니다)는, 메모리 Operand를 명시적으로 지정하는 것이 가능합니다.

*( Operand는 이런 사이즈와 주소를 나타내는 기호입니다. 레지스터 Operand는 자동적으로 선택됩니다. (BYTE Operand로는 AL, WORD Operand로는 AX, DOUBLE WORD Operand로는 EAX). )*

Operand를 지정하는 형식은 소스코드의 가독성을 중요시하여 도입되었습니다만, 사용상의 오해가 있을 우려가 있는 경우가 있어서, 주의가 필요합니다. 즉, 양방의 Operand의 기호는 올바른 타입 (서비스)의 Operand(BYTE, WORD, DOUBLE WORD)를 지정할 필요가 있습니다만, 올바른 주소를 지정할 필요는 없습니다. 양방의 Operand의 주소는 항상 ES:(E)DI에 따라 지정되어, 문자열 저장 명령을 실행하기 전에, 올바른 주소를 ES:(E)DI에 로드해둘 필요가 있습니다.

Operand가 없는 형식은, 생략형식의 BYTE, WORD, DOUBLE WORD용의 STOS명령이 사용될 수 있습니다. 이런 경우도, 프로세서는 ES:(E)DI가 메모리 주소로, AL, AX, EAX가 읽기 전 Operand라고 상정하여 동작합니다.

Operand의 서비스는 STOSB(AL로부터 BYTE를 읽어 들임). STOSW(AX로부터 WORD를 읽어 들임) STOSD(EAX로부터 DOUBLE WORD를 읽어 들임)의 각 Mnemonic으로 선택합니다. AL, AX, EAX가 메모리에 전송된 후, EFLAFS레지스터의 DF플래그의 설정에 따라서, (E)DI의 값이 자동적으로 증가 또는 감소됩니다. (DF가 0일 경우는 레지스터가 증가하고, DF가 1일 경우 레지스터가 감소합니다) 이 레지스터는 BYTE 조작의 경우 1로, WORD 조작의 경우는 2로, DOUBLE WORD 조작의 경우는 4, 등의 증가 혹은 감소가 됩니다.

STOS, STOSB, STOSW, STOSD명령은 전의 REP Prefix를 붙이는 것보단, ECX로 지정되는 BYTE, WORD, DOUBLE WORD단위로 반복 로드를 실행할 수 있습니다. 보통은, 데이터를 저장하기 전에 AL, AX, EAX레지스터의 값을 갱신할 필요가 있으므로, 일반적으로는 Status Flag의 값에 따라 어떠한 처리를 하는 루프 중에서 사용됩니다.

<br>
<br>

출처: http://softwaretechnique.jp/OS_Development/Tips/IA32_Instructions/STOS.html
