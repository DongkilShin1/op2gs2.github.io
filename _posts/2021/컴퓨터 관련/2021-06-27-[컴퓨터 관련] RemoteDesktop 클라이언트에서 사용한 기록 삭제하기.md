## 들어가면서

컴퓨터를 사용하면서, 외부에서 내 윈도우 데스크톱을 연결해야 할 때가 있다. 혹은 노트북 대신에 데스크톱을 놓고 외부PC를 이용해 접속하는 사람들이 많다. 나 또한, 사양, 개발환경등의 문제로 노트북 대신에 외부에 있는 컴퓨터로 내 데스크톱에 접속하곤한다.

하지만, 외부PC에서 원격접속을 한 뒤 외부PC에 기록이 남게 된다. 나는 항상 그 부분이 찜찜 했고, 해결책을 찾았다. 다음 방법을 따라하면, 외부PC에 접속했던 기록을 지울 수 있게 된다.


## 1. 레지스트리 지우기

레지스트리란, 일종의 설정 파일 저장소 같은 개념이다. 프로그램이 설치되고 나면, 환경설정용으로 참고할 값이나 데이터가 저장되는 곳이다. 마찬가지로, 외부PC → 내 컴퓨터로 원격 데스크톱을 사용하면 레지스트리 기록이 남게 된다.



### 1. 레지스트리 편집기를 열기
- [Window 키] + [R] 키를 누르면 사진과 같은 창이 뜨는데, 그곳에 "regedit"을 입력하고 확인을 누른다.

![그림1. 레지스트리 편집기를 열기](https://github.com/op2gs2/op2gs2.github.io/blob/main/assets/images/2021/%EC%BB%B4%ED%93%A8%ED%84%B0%20%EA%B4%80%EB%A0%A8/2021-06-27-%5B%EC%BB%B4%ED%93%A8%ED%84%B0%20%EA%B4%80%EB%A0%A8%5D%20RemoteDesktop%20%ED%81%B4%EB%9D%BC%EC%9D%B4%EC%96%B8%ED%8A%B8%EC%97%90%EC%84%9C%20%EC%82%AC%EC%9A%A9%ED%95%9C%20%EA%B8%B0%EB%A1%9D%20%EC%82%AD%EC%A0%9C%ED%95%98%EA%B8%B0/1.png?raw=true "그림1. 레지스트리 편집기를 열기")

### 2. RemoteDesktop 접속 관련 레지스트리가 있는 디렉터리로 이동
- 레지스트리 편집기에서 "HKEY_CURENT_USERS\SOFTWARE\Microsoft\Terminal Server Client\"로 이동한다.
- 사진의 빨간 부분을 참고하면 된다. (Termianl Server는 한참 아래에 있어 사진에 싣지는 못했다.)

![그림2. RemoteDesktop 접속 관련 레지스트리가 있는 디렉터리로 이동](https://github.com/op2gs2/op2gs2.github.io/blob/main/assets/images/2021/%EC%BB%B4%ED%93%A8%ED%84%B0%20%EA%B4%80%EB%A0%A8/2021-06-27-%5B%EC%BB%B4%ED%93%A8%ED%84%B0%20%EA%B4%80%EB%A0%A8%5D%20RemoteDesktop%20%ED%81%B4%EB%9D%BC%EC%9D%B4%EC%96%B8%ED%8A%B8%EC%97%90%EC%84%9C%20%EC%82%AC%EC%9A%A9%ED%95%9C%20%EA%B8%B0%EB%A1%9D%20%EC%82%AD%EC%A0%9C%ED%95%98%EA%B8%B0/2.png?raw=true "그림2. RemoteDesktop 접속 관련 레지스트리가 있는 디렉터리로 이동")

### 3. RemoteDesktop 접속 관련 레지스트리를 삭제하기(1)
- "HKEY_CURENT_USERS\SOFTWARE\Microsoft\Terminal Server Client\Servers\" 하위에 존재하는, 접속 주소 이름으로 된 폴더(키)를 삭제한다. (사진의 빨간색 부분을 삭제하면 된다.)

![그림 3. RemoteDesktop 접속 관련 레지스트리를 삭제하기(1)](https://github.com/op2gs2/op2gs2.github.io/blob/main/assets/images/2021/%EC%BB%B4%ED%93%A8%ED%84%B0%20%EA%B4%80%EB%A0%A8/2021-06-27-%5B%EC%BB%B4%ED%93%A8%ED%84%B0%20%EA%B4%80%EB%A0%A8%5D%20RemoteDesktop%20%ED%81%B4%EB%9D%BC%EC%9D%B4%EC%96%B8%ED%8A%B8%EC%97%90%EC%84%9C%20%EC%82%AC%EC%9A%A9%ED%95%9C%20%EA%B8%B0%EB%A1%9D%20%EC%82%AD%EC%A0%9C%ED%95%98%EA%B8%B0/3.png?raw=true "그림 3. RemoteDesktop 접속 관련 레지스트리를 삭제하기(1)")

### 4. RemoteDesktop 접속 관련 레지스트리를 삭제하기(2)
- "HKEY_CURENT_USERS\SOFTWARE\Microsoft\Terminal Server Client\Default\" 하위에 존재하는, 접속 주소 이름으로 된 값을 삭제하면 된다.



## 2. 원격접속환경 파일 지우기

### 1. "내PC\문서" 폴더로 이동
- 사진에 표시된 대로, [보기] - [숨긴 항목]을 클릭한다.

![그림 4. 숨긴 항목 표시하기](https://github.com/op2gs2/op2gs2.github.io/blob/main/assets/images/2021/%EC%BB%B4%ED%93%A8%ED%84%B0%20%EA%B4%80%EB%A0%A8/2021-06-27-%5B%EC%BB%B4%ED%93%A8%ED%84%B0%20%EA%B4%80%EB%A0%A8%5D%20RemoteDesktop%20%ED%81%B4%EB%9D%BC%EC%9D%B4%EC%96%B8%ED%8A%B8%EC%97%90%EC%84%9C%20%EC%82%AC%EC%9A%A9%ED%95%9C%20%EA%B8%B0%EB%A1%9D%20%EC%82%AD%EC%A0%9C%ED%95%98%EA%B8%B0/4.png?raw=true "그림 4. 숨긴 항목 표시하기")

### 2. 원격 데스크톱 설정 파일 삭제하기
- "원격 데스크톱 연결"이라고 연한 아이콘의 파일이 나오는데, 이 파일을 삭제하면 된다.

![그림 5. 원격 데스크톱 설정 파일 삭제하기](https://github.com/op2gs2/op2gs2.github.io/blob/main/assets/images/2021/%EC%BB%B4%ED%93%A8%ED%84%B0%20%EA%B4%80%EB%A0%A8/2021-06-27-%5B%EC%BB%B4%ED%93%A8%ED%84%B0%20%EA%B4%80%EB%A0%A8%5D%20RemoteDesktop%20%ED%81%B4%EB%9D%BC%EC%9D%B4%EC%96%B8%ED%8A%B8%EC%97%90%EC%84%9C%20%EC%82%AC%EC%9A%A9%ED%95%9C%20%EA%B8%B0%EB%A1%9D%20%EC%82%AD%EC%A0%9C%ED%95%98%EA%B8%B0/5.png?raw=true "그림 5. 원격 데스크톱 설정 파일 삭제하기")
