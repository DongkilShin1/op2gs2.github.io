---
title:  "[컴퓨터 관련] (Vi/Vim) Found a swap file by the name 2가지 해결방법"
excerpt: "Vi/Vim에서 SWAP파일이 발견되었을 때 해결 방법에 대해 알아보자."

categories:
  - 컴퓨터 관련

tags:
  - Vi/Vim
  - Vi/Vim swqp
  - Found a swap file by the name

last_modified_at: 2021-07-10T15:51:00+09:00

---

"Found a swap file by the name"은 비정상적인 종료 등으로 .swp 파일이 남겨져 있다고 하는 일종의 에러다. 물론 무시하고 사용해도 무방하지만, 매번 들어갈 때 마다 표시되는 창은 귀찮을 것이다.

그런 사람들을 위해, 2가지의 해결 방법을 소개하고자 한다. 어떤 방법이든 동일한 결과를 가져오니, 각자 상황에 맞는 편한 방법으로 사용하면 좋을 듯 하다.

---

- 해결 방법(1): vi/vim에서 자동으로 swap파일을 삭제하기
    1. Q(Quit)이나 A(Abort)를 눌러 vi/vim을 빠져나간다.

        ```
        E325: ATTENTION
        Found a swap file by the name ".week07-02.c.swp"
                  owned by: hallym   dated: Sat Apr 17 01:17:42 2021
                 file name: ~hallym/week07/week07-02.c
                  modified: YES
                 user name: hallym   host name: S26-VM-N8-186
                process ID: 12101 (still running)
        While opening file "week07-02.c"
                     dated: Fri Apr 16 09:45:23 2021

        (1) Another program may be editing the same file.  If this is the case,
            be careful not to end up with two different instances of the same
            file when making changes.  Quit, or continue with caution.
        (2) An edit session for this file crashed.
            If this is the case, use ":recover" or "vim -r week07-02.c"
            to recover the changes (see ":help recovery").
            If you did this already, delete the swap file ".week07-02.c.swp"
            to avoid this message.

        Swap file ".week07-02.c.swp" already exists!
        [O]pen Read-Only, (E)dit anyway, (R)ecover, (Q)uit, (A)bort:
        ```

    2. ps를 눌러 vi나 vim이 실행 상태에 있는지 확인한다.

        ```bash
        hallym@S26-VM-N8-186:~/week07$ ps
          PID TTY          TIME CMD
        12085 pts/0    00:00:00 bash
        12101 pts/0    00:00:00 vi
        12106 pts/0    00:00:00 ps
        ```

    3. 만약 실행 중이라면, kill 명령을 통해 해당 프로세스를 종료한다.

        ```bash
        hallym@S26-VM-N8-186:~/week07$ kill -9 12101
        [1]+  Killed                  vi week07-02.c
        ```

    4. 다시 vi를 들어가게 되면, swap파일을 삭제할 수 있는 창이 뜬다. 여기서, D(Delete)를 눌러 해당 파일을 삭제한다.

        ```
        E325: ATTENTION
        Found a swap file by the name ".week07-02.c.swp"
                  owned by: hallym   dated: Sat Apr 17 01:17:42 2021
                 file name: ~hallym/week07/week07-02.c
                  modified: YES
                 user name: hallym   host name: S26-VM-N8-186
                process ID: 12101
        While opening file "week07-02.c"
                     dated: Fri Apr 16 09:45:23 2021

        (1) Another program may be editing the same file.  If this is the case,
            be careful not to end up with two different instances of the same
            file when making changes.  Quit, or continue with caution.
        (2) An edit session for this file crashed.
            If this is the case, use ":recover" or "vim -r week07-02.c"
            to recover the changes (see ":help recovery").
            If you did this already, delete the swap file ".week07-02.c.swp"
            to avoid this message.

        Swap file ".week07-02.c.swp" already exists!
        [O]pen Read-Only, (E)dit anyway, (R)ecover, (D)elete it, (Q)uit, (A)bort:
        ```

    5. 삭제하고 다시 들어가면, vi/vim에서 오류를 띄우지 않고 정상적으로 들어갈 수 있다.

---

- 해결 방법(2): 직접 swap 파일을 찾아 삭제하기
    1. Q(Quit)이나 A(Abort)를 눌러 vi/vim을 빠져나간다.

        ```
        E325: ATTENTION
        Found a swap file by the name ".week07-02.c.swp"
                  owned by: hallym   dated: Sat Apr 17 01:17:42 2021
                 file name: ~hallym/week07/week07-02.c
                  modified: YES
                 user name: hallym   host name: S26-VM-N8-186
                process ID: 12101 (still running)
        While opening file "week07-02.c"
                     dated: Fri Apr 16 09:45:23 2021

        (1) Another program may be editing the same file.  If this is the case,
            be careful not to end up with two different instances of the same
            file when making changes.  Quit, or continue with caution.
        (2) An edit session for this file crashed.
            If this is the case, use ":recover" or "vim -r week07-02.c"
            to recover the changes (see ":help recovery").
            If you did this already, delete the swap file ".week07-02.c.swp"
            to avoid this message.

        Swap file ".week07-02.c.swp" already exists!
        [O]pen Read-Only, (E)dit anyway, (R)ecover, (Q)uit, (A)bort:
        ```

    2. ls 명령을 이용해 swap(.swp) 파일을 찾는다.

        ```bash
        hallym@S26-VM-N8-186:~/week07$ ls -la
        total 68
        drwxrwx--- 2 hallym hallym 4096 Apr 17 01:29 .
        drwxr-xr-x 9 hallym hallym 4096 Apr 17 01:29 ..
        -rw-rw---- 1 hallym hallym   14 Apr 16 08:10 a.txt
        -rw-rw---- 1 hallym hallym   18 Apr 16 08:10 b.txt
        -rwxrwx--- 1 hallym hallym 8496 Apr 16 08:08 week07-01
        -rw-rw---- 1 hallym hallym  590 Apr 16 08:08 week07-01.c
        -rwxrwx--- 1 hallym hallym 8472 Apr 16 09:45 week07-02
        -rw-rw---- 1 hallym hallym  385 Apr 16 09:45 week07-02.c
        -rw-r----- 1 hallym hallym 4096 Apr 17 01:29 .week07-02.c.swp
        -rwxrwx--- 1 hallym hallym 8648 Apr 16 09:56 week07-03
        -rw-rw---- 1 hallym hallym  419 Apr 16 09:57 week07-03.c
        ```

    3.  swap(.swp) 파일을 확인했으면 삭제한다.

        ```bash
        hallym@S26-VM-N8-186:~/week07$ rm .week07-02.c.swp
        ```

    4. 삭제하고 다시 들어가면, vi/vim에서 오류를 띄우지 않고 정상적으로 들어갈 수 있다.
