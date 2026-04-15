## 1. 프로젝트 개요

로컬 개발 환경(터미널, Docker, Git, GitHub)을 직접 구축하고
각 도구의 기본 사용법을 실습하여 개발 워크스테이션을 완성한다.

## 2. 실행 환경

| 항목 | 값 |
|---|---|
| OS | Windows 10/11 |
| 쉘 / 터미널 | Windows PowerShell|
| Docker 버전 | 29.3.1 |
| Git 버전 | 2.53.0.windows.2 |

## 3. 수행 항목 체크리스트

- [x] 터미널 조작 로그 (PowerShell 기반)
- [x] 권한 실습
- [x] Docker 설치 및 기본 점검
- [x] Docker 기본 운영 명령
- [x] 컨테이너 실행 실습
- [x] Dockerfile 커스텀 이미지 제작
- [x] 포트 매핑 및 접속 증거
- [x] 바인드 마운트
- [x] Docker 볼륨 영속성 검증
- [x] Git 설정 및 GitHub 연동

## 3-1. 프로젝트 디렉토리 구조
```
docker-assignment/
├── .git/            # Git 관리 폴더 (자동 생성)
├── README.md        # 프로젝트 설명 및 기술 문서
├── test.txt         # 과제 완료 테스트 파일 (Assignment complete)
└── test_file.txt    # 도커 컨테이너에서 처음 생성한 파일
```

**구성 기준**
- `app/` → 웹서버 소스코드를 별도 폴더로 분리하여 Dockerfile의 COPY 경로를 명확하게 관리
- `practice/` → 터미널 실습 내용을 저장소 루트와 분리하여 실습 흔적을 명확히 구분
- `Dockerfile` → 저장소 루트에 위치시켜 `docker build .` 명령을 루트에서 바로 실행 가능하게 구성
## 4. 터미널 조작 로그

### 현재 위치 확인
```bash
PS C:\Windows\system32\practice> pwd

Path
----
C:\Windows\system32\practice
```

### 목록 확인 (숨김 파일 포함)
```bash
# 1. 초기 상태 확인 (아무것도 없는 상태)
PS C:\Windows\system32\practice> ls -Force

# (출력 결과 없음 - 초기 클린 상태 확인)
```

### 폴더 생성 및 이동
```bash
# 1. 'testdir' 폴더 생성
PS C:\Windows\system32\practice> mkdir testdir

    디렉터리: C:\Windows\system32\practice

Mode                LastWriteTime         Length Name
----                -------------         ------ ----
d-----        2026-04-09  오후 2:55              testdir

# 2. 생성 후 목록 확인 (실제로 생겼는지 검증)
PS C:\Windows\system32\practice> ls

    디렉터리: C:\Windows\system32\practice

Mode                LastWriteTime         Length Name
----                -------------         ------ ----
d-----        2026-04-09  오후 2:55              testdir

# 3. 생성한 폴더로 이동
PS C:\Windows\system32\practice> cd testdir

# 4. 폴더 이동확인
PS C:\Windows\system32\practice\testdir> pwd

Path
----
C:\Windows\system32\practice\testdir
```

### 파일 생성
```bash
# 1. New-Item 명령어로 빈 파일(hello.txt) 생성
PS C:\Windows\system32\practice\testdir> New-Item hello.txt

    디렉터리: C:\Windows\system32\practice\testdir

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----        2026-04-09  오후 2:57              0 hello.txt

# 2. 파일 생성 여부 최종 확인
PS C:\Windows\system32\practice\testdir> ls

    디렉터리: C:\Windows\system32\practice\testdir

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----        2026-04-09  오후 2:57              0 hello.txt
```

### 파일 내용 작성 및 확인
```bash
# 1. 파일에 내용 쓰기
PS C:\Windows\system32\practice\testdir> echo "Hello, Docker!" > hello.txt

# 2. 파일 내용 정상 확인
PS C:\Windows\system32\practice\testdir> cat hello.txt
Hello, Docker!

# 3. [참고] 인자값 미지정 시의 동작 확인
PS C:\Windows\system32\practice\testdir> cat
cmdlet Get-Content(명령 파이프라인 위치 1)
다음 매개 변수에 대한 값을 제공하십시오.
Path[0]:

특이사항: cat 명령어 실행 시 대상 파일을 지정하지 않을 경우, PowerShell이 실시간으로 매개변수(Path) 입력을 요구하는 인터렉티브(Interactive) 모드로 진입함을 확인하였다
```

### 파일 복사
```bash
PS C:\Windows\system32\practice\testdir> cp hello.txt hello_copy.txt
PS C:\Windows\system32\practice\testdir> ls


    디렉터리: C:\Windows\system32\practice\testdir


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----      2026-04-09   오후 3:17             32 hello.txt
-a----      2026-04-09   오후 3:17             32 hello_copy.txt
```

### 파일 이름 변경
```bash
PS C:\Windows\system32\practice\testdir> mv hello_copy.txt hello_renamed.txt
PS C:\Windows\system32\practice\testdir> ls


    디렉터리: C:\Windows\system32\practice\testdir


Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----      2026-04-09   오후 3:17             32 hello.txt
-a----      2026-04-09   오후 3:17             32 hello_renamed.txt
```

### 파일 및 폴더 삭제
```bash
PS C:\Windows\system32\practice\testdir> rm hello_renamed.txt
PS C:\Windows\system32\practice\testdir> cd ..
PS C:\Windows\system32\practice> rm -r testdir
PS C:\Windows\system32\practice> ls
PS C:\Windows\system32\practice>

실습 완료 후 rm 명령어를 사용하여 생성했던 임시 파일과 디렉토리를 제거하였습니다. 특히 디렉토리 삭제 시 -r(Recursive) 옵션을 사용하여 하위 항목을 포함한 완전한 자원 회수 과정을 실습했습니다.
```
### 절대 경로 vs 상대 경로

**절대 경로** — 루트(Root) 드라이브부터 시작하여 목적지까지의 모든 경로를 다 적는 방식
```bash
cd C:\Windows\system32\practice\testdir
# 어디서 실행해도 항상 같은 위치로 이동
```

**상대 경로** — 현재 내가 있는 위치'를 기준으로 목적지를 찾는 방식
```bash
cd ..        # 한 단계 위(practice 폴더)로 이동
cd ./testdir # 현재 위치의 testdir 폴더로 이동
```

**선택 기준**

| 상황 | 선택 | 이유 |
|---|---|---|
| 스크립트, 자동화 | 절대 경로 | 실행 위치가 달라도 항상 동일하게 동작 |
| 터미널 직접 입력 | 상대 경로 | 타이핑이 짧고 빠름 |
| Dockerfile COPY | 상대 경로 | 빌드 컨텍스트 기준으로 동작 |
| Docker 볼륨 마운트 | 절대 경로 | 정확한 호스트 경로 지정 필요 |
## 5. 권한 실습
### 권한 숫자 표기 규칙

권한은 소유자/그룹/others 3개 그룹으로 나뉘며 각 그룹의 권한을 숫자로 더해서 표현한다.
```
r (Read / 읽기): 파일 내용을 보거나 복사할 수 있는 권한 4
w (Write / 쓰기): 파일 내용을 수정하거나 삭제할 수 있는 권한 2
x (Execute / 실행): 파일을 프로그램처럼 실행할 수 있는 권한 1
없음 0
```

각 그룹의 권한을 더한 숫자 3개를 나열한다.
```
755  =  rwx r-x r-x
        ↑   ↑   ↑
        7   5   5
        소유자 그룹 others

7 = 4+2+1 = rwx (읽기+쓰기+실행)
5 = 4+0+1 = r-x (읽기+실행)
5 = 4+0+1 = r-x (읽기+실행)
```
로컬 환경(Windows PowerShell/NTFS)에서는 chmod 명령어가 지원되지 않음을 확인하였으며, 이를 해결하기 위해 직접 Docker로 Ubuntu 컨테이너를 실행하여 리눅스 환경 내에서 명령어를 수행하고 그 결과를 기록하였습니다.

### 파일 권한 변경
실습 환경: Docker Container (Ubuntu Linux)

참고사항: 로컬 Windows 환경의 NTFS 파일 시스템 한계로 인해 chmod 실습은 Docker 컨테이너 내부 리눅스 환경에서 수행함.
```bash
# 1. 파일 생성 및 기본 권한 확인
root@0ce58de8f83d:/practice# touch test.txt
root@0ce58de8f83d:/practice# ls -la test.txt
-rw-r--r-- 1 root root 0 Apr  9 08:15 test.txt

# 2. 모든 권한 허용 (777)
root@0ce58de8f83d:/practice# chmod 777 test.txt
root@0ce58de8f83d:/practice# ls -la test.txt
-rwxrwxrwx 1 root root 0 Apr  9 08:16 test.txt

# 3. 소유자 전용 모드 (600)
root@0ce58de8f83d:/practice# chmod 600 test.txt
root@0ce58de8f83d:/practice# ls -la test.txt
-rw------- 1 root root 0 Apr  9 08:17 test.txt

# 4. 표준 권한 복구 (644)
root@0ce58de8f83d:/practice# chmod 644 test.txt
root@0ce58de8f83d:/practice# ls -la test.txt
-rw-r--r-- 1 root root 0 Apr  9 08:18 test.txt
```

### 디렉토리 권한 변경
```bash
# 1. 디렉토리 생성 및 초기 상태 확인
root@0ce58de8f83d:/practice# mkdir testdir
root@0ce58de8f83d:/practice# ls -la | grep testdir
drwxr-xr-x 2 root root 4096 Apr  9 08:19 testdir

# 2. 보안 강화 (700)
root@0ce58de8f83d:/practice# chmod 700 testdir
root@0ce58de8f83d:/practice# ls -la | grep testdir
drwx------ 2 root root 4096 Apr  9 08:20 testdir

# 3. 표준 디렉토리 권한 설정 (755)
root@0ce58de8f83d:/practice# chmod 755 testdir
root@0ce58de8f83d:/practice# ls -la | grep testdir
drwxr-xr-x 2 root root 4096 Apr  9 08:21 testdir
```

## 6. Docker 설치 및 기본 점검

### Docker 버전 확인
```bash
PS C:\Windows\system32\practice> docker --version
Docker version 29.3.1, build c2be9cc
```

### Docker 데몬 동작 확인
```bash
PS C:\Windows\system32\practice> docker info
Client:
  Version:    29.3.1
  Context:    desktop-linux
...
Server:
  Containers: 8
    Running: 0
    Paused: 0
    Stopped: 8
  Images: 10
  Server Version: 29.3.1
  OSType: linux
  Architecture: x86_64
  CPUs: 18
  Total Memory: 7.623GiB
```

## 7. Docker 기본 운영 명령

### 이미지 목록 확인
```bash
PS C:\Windows\system32\practice> docker images
                                                        i Info →   U  In Use
IMAGE                    ID             DISK USAGE   CONTENT SIZE   EXTRA
alpine:latest            25109184c71b       13.1MB         3.95MB    U
hanyang-web:1.0          316716abc619       92.6MB           26MB
hello-world:latest       452a468a4bf9       25.9kB         9.49kB    U
my-custom-nginx:latest   a4e6734f755b       92.6MB           26MB    U
my-web:1.0               759478fc6191       92.6MB           26MB    U
nginx:alpine             e7257f1ef28b       93.5MB         26.9MB
ubuntu:latest            84e77dee7d1b        119MB         31.7MB    U
```

### 컨테이너 목록 확인
```bash
PS C:\Windows\system32\practice> docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES

PS C:\Windows\system32\practice> docker ps -a
CONTAINER ID   IMAGE             COMMAND                   CREATED          STATUS                     PORTS                  NAMES
0ce58de8f83d   ubuntu            "bash"                    21 minutes ago   Exited (0) 6 minutes ago                          practice-linux
2565a20fe622   hello-world       "/hello"                  20 hours ago     Exited (0) 20 hours ago                           condescending_rosalind
7ba50d20b7fd   my-custom-nginx   "/docker-entrypoint.…"   5 days ago       Exited (255) 3 days ago    0.0.0.0:8080->80/tcp   my-web-server
69071833a146   hello-world       "/hello"                  5 days ago       Exited (0) 5 days ago                             practical_haibt
818625059f0e   hello-world       "/hello"                  5 days ago       Exited (0) 5 days ago                             crazy_booth
9c1a67791bfb   my-web:1.0        "/docker-entrypoint.…"   6 days ago       Exited (255) 5 days ago    0.0.0.0:8081->80/tcp   web-8081
bc6c79925370   my-web:1.0        "/docker-entrypoint.…"   6 days ago       Exited (255) 5 days ago    0.0.0.0:8080->80/tcp   web-8080
db8d4b43406e   alpine            "sleep infinity"          6 days ago       Exited (255) 5 days ago                           vol-test2
```

### 로그 확인
```bash
PS C:\Windows\system32\practice> docker logs practice-linux
root@0ce58de8f83d:/# mkdir /practice
root@0ce58de8f83d:/# cd /practice
root@0ce58de8f83d:/practice# touch test.txt
root@0ce58de8f83d:/practice# ls -la test.txt
-rw-r--r-- 1 root root 0 Apr  9 08:15 test.txt
root@0ce58de8f83d:/practice# chomd 777 test.txt
bash: chomd: command not found
root@0ce58de8f83d:/practice# ls -la test.txt
-rw-r--r-- 1 root root 0 Apr  9 08:15 test.txt
root@0ce58de8f83d:/practice# chmod 777 test.txt
root@0ce58de8f83d:/practice# ls -la test.txt
-rwxrwxrwx 1 root root 0 Apr  9 08:15 test.txt
root@0ce58de8f83d:/practice# chmod 600 test.txt
root@0ce58de8f83d:/practice# ls -la test.txt
-rw------- 1 root root 0 Apr  9 08:15 test.txt
root@0ce58de8f83d:/practice# mod 644 test.txt
bash: mod: command not found
root@0ce58de8f83d:/practice# chmod 644 test.txt
root@0ce58de8f83d:/practice# ls -la test.txt
-rw-r--r-- 1 root root 0 Apr  9 08:15 test.txt
root@0ce58de8f83d:/practice# exit
exit
```

### 리소스 확인
```bash
PS C:\Windows\system32\practice> docker stats --no-stream
CONTAINER ID   NAME   CPU %   MEM USAGE / LIMIT   MEM %   NET I/O   BLOCK I/O   PIDS
# (실행 중인 컨테이너가 없으므로 비어있음)
```

## 8. 컨테이너 실행 실습

### hello-world 실행
```bash
PS C:\Windows\system32\practice> docker run hello-world

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/
```

### ubuntu 컨테이너 진입 및 명령 실행
```bash
PS C:\Windows\system32\practice> docker run -it ubuntu bash
root@5f6759d2dcf2:/# pwd (현재 위치 확인)
/
root@5f6759d2dcf2:/# ls (폴더 목록 확인)
bin   dev  home  lib64  mnt  proc  run   srv  tmp  var
boot  etc  lib   media  opt  root  sbin  sys  usr
root@5f6759d2dcf2:/# cat /etc/os-release (Os 버전확인)
PRETTY_NAME="Ubuntu 24.04.4 LTS"
NAME="Ubuntu"
VERSION_ID="24.04"
VERSION="24.04.4 LTS (Noble Numbat)"
VERSION_CODENAME=noble
ID=ubuntu
ID_LIKE=debian
HOME_URL="https://www.ubuntu.com/"
SUPPORT_URL="https://help.ubuntu.com/"
BUG_REPORT_URL="https://bugs.launchpad.net/ubuntu/"
PRIVACY_POLICY_URL="https://www.ubuntu.com/legal/terms-and-policies/privacy-policy"
UBUNTU_CODENAME=noble
LOGO=ubuntu-logo
```

### exec vs attach 차이 관찰

**exec 방식** — 새로운 프로세스를 추가로 실행하므로 exit 후에도 컨테이너가 유지된다.
```bash
# 1. 컨테이너를 백그라운드 모드(-d)로 실행
PS C:\Windows\system32\practice> docker run -it  -d --name myubuntu ubuntu bash
14846311221e11cf71b03e6e403072f1ceac218093077816681e97ea1e152b6d
# 2. exec 명령어로 새로운 프로세스를 띄워 진입
PS C:\Windows\system32\practice> docker exec -it myubuntu bash
root@14846311221e:/# exit
# 3. 컨테이너 상태 확인
PS C:\Windows\system32\practice> docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED          STATUS          PORTS     NAMES
14846311221e   ubuntu    "bash"    6 minutes ago    Up 6 minutes              myubuntu
5f6759d2dcf2   ubuntu    "bash"    18 minutes ago   Up 18 minutes             dreamy_clarke
# 컨테이너가 여전히 실행 중
```

**attach 방식** — 메인 프로세스에 직접 연결하므로 exit 시 컨테이너도 종료된다.
```bash
# 1. 실행 중인 컨테이너의 메인 프로세스에 연결
PS C:\Windows\system32\practice> docker attach myubuntu
root@14846311221e:/# exit
# 2. 컨테이너 상태 확인 (프로세스가 종료되어 목록에서 사라짐)
PS C:\Windows\system32\practice> docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED          STATUS          PORTS     NAMES
5f6759d2dcf2   ubuntu    "bash"    19 minutes ago   Up 19 minutes             dreamy_clarke
3. 전체 목록 확인 시 종료(Exited)된 것을 확인 가능
PS C:\Windows\system32\practice> docker ps -a
CONTAINER ID   IMAGE         COMMAND    CREATED          STATUS                     NAMES
14846311221e   ubuntu        "bash"     10 minutes ago   Exited (0) 2 minutes ago   myubuntu
5f6759d2dcf2   ubuntu        "bash"     22 minutes ago   Up 22 minutes              dreamy_clarke
a10021c98f33   hello-world   "/hello"   23 minutes ago   Exited (0) 23 minutes ago  xenodochial_gould
0ce58de8f83d   ubuntu        "bash"     52 minutes ago   Exited (0) 37 minutes ago  practice-linux
... (이하 생략) ...
# 컨테이너가 종료됨
```

## 9. Dockerfile 커스텀 이미지 제작

### 이미지 vs 컨테이너 — 빌드/실행/변경 관점

| 관점 | 이미지 | 컨테이너 |
|---|---|---|
| 빌드 | `docker build`로 생성 | 이미지를 기반으로 생성 |
| 실행 | 실행되지 않음 (틀) | `docker run`으로 실행됨 |
| 변경 | 불변 (변경 불가) | 실행 중 내부 변경 가능 |
| 삭제 후 | 이미지는 그대로 남음 | 컨테이너 안의 변경사항 사라짐 |
| 재사용 | 하나의 이미지로 컨테이너 여러 개 생성 가능 | 각 컨테이너는 독립적 |
```
docker build → 이미지 생성 (불변의 틀)
                    ↓
docker run  → 컨테이너 생성 (실행 가능한 인스턴스)
                    ↓
내부 변경   → 컨테이너에만 반영, 이미지는 그대로
                    ↓
docker rm   → 컨테이너 삭제, 변경사항 사라짐
```
### 선택한 베이스 이미지
nginx:latest (웹서버 베이스 이미지 활용 — A 방식)

### Dockerfile
```dockerfile
# 베이스 이미지: nginx 최신 버전
FROM nginx:latest

# 호스트의 app/index.html 파일을 컨테이너 내부의 nginx 경로로 복사
COPY app/index.html /usr/share/nginx/html/index.html

# 컨테이너가 80번 포트를 사용함을 명시
EXPOSE 80
```

### 커스텀 포인트
| 항목 | 목적 |
|---|---|
| `FROM nginx:latest` | 별도의 웹서버 설치 없이 바로 서비스 가능한 환경 구축 |
| `COPY app/index.html` | "Hello, Docker! This is Han-gyeol's Web Server." 문구가 담긴 커스텀 페이지 반영 |
| `EXPOSE 80` | 웹 서비스의 기본 포트인 80번을 외부와 연결하기 위해 설정 |

### 빌드 및 실행
```bash
# 1. 빌드 환경 설정 및 이미지 빌드 실행
# (BuildKit 이슈 대응을 위해 레거시 빌더 활용 및 Dockerfile 인코딩 최적화 후 진행)
PS C:\docker-practice> $env:DOCKER_BUILDKIT=0
PS C:\docker-practice> docker build -t my-nginx .

Sending build context to Docker daemon  3.584kB
Step 1/3 : FROM nginx:latest
 ---> 7f0adca1fc6c
Step 2/3 : COPY app/index.html /usr/share/nginx/html/index.html
 ---> 6e502c94f0fe
Step 3/3 : EXPOSE 80
 ---> Running in d6fe75582d62
Successfully built 4447afea7e58
Successfully tagged my-nginx:latest

# 2. 컨테이너 실행 및 포트 매핑 (8081 -> 80)
PS C:\docker-practice> docker run -d -p 8081:80 --name my-nginx-container my-nginx
a30fdf20c77fbd2ff560b6009eeb25f537d7f7c58ecf8da3d85aaa58753a0b15
```

## 10. 포트 매핑 및 접속 증거

### 컨테이너 내부 포트로 직접 접속할 수 없는 이유

컨테이너는 격리된 네트워크 환경을 가진다. 컨테이너 내부의 포트는 외부(호스트)에서 직접 접근할 수 없다.
```
브라우저 → localhost:80 접속 시도
         → 호스트의 80번 포트로 요청
         → 컨테이너 내부 포트와 연결 안 됨
         → 접속 실패
```

포트 매핑(`-p`)으로 호스트 포트와 컨테이너 포트를 연결해야 한다.
```
docker run -p 8081:80 my-nginx
               ↑    ↑
          호스트  컨테이너

브라우저 → localhost:8081
         → 호스트 8081 포트
         → 컨테이너 80 포트로 전달
         → nginx 응답
         → 접속 성공
```

**포트 매핑이 필요한 이유**
- 격리성 및 보안: 컨테이너 내부 환경을 보호하고 필요한 서비스만 외부에 노출하기 위함.
- 포트 충돌 방지: 호스트의 서로 다른 포트(8081, 8082 등)를 사용하여 동일한 이미지 기반의 컨테이너를 여러 개 동시에 실행 가능.

실행 명령어
```bash
PS C:\docker-practice> docker run -d -p 8081:80 --name my-nginx-container my-nginx
```

브라우저에서 `http://localhost:8081` 접속 성공:

<img width="956" height="559" alt="포트매핑 성공 인증사진" src="https://github.com/user-attachments/assets/1eb8331d-08d6-45c8-9007-7a2ef38c0bc2" />" />

## 11. 바인드 마운트

호스트의 `app/` 폴더와 컨테이너의 `/usr/share/nginx/html`을 연결하여 호스트에서 파일을 수정하면 컨테이너에 즉시 반영되는 것을 확인하였다.
```bash
PS C:\docker-practice> docker run -d -p 8081:80 `
>>   -v "C:\docker-practice\app:/usr/share/nginx/html" `
>>   --name my-nginx-container my-nginx
11fa65929604bd560a59f8938b4965876e58831f315cd1ce47f84c2fef363f61
```

호스트에서 `index.html`에 "바인드 마운트 성공!" 문구 추가 후 브라우저 새로고침 시 즉시 반영됨:

<img width="959" height="599" alt="바인드 마운트 실습 성공사진" src="https://github.com/user-attachments/assets/94626431-4666-4856-bd1a-a8a03ba84a58" />

## 12. Docker 볼륨 영속성 검증

### 볼륨 생성 및 연결
```bash
PS C:\docker-practice> docker volume create my-hankyul-volume
my-hankyul-volume

PS C:\docker-practice> docker volume ls
DRIVER    VOLUME NAME
local     my-hankyul-volume
local     my-stirage
local     my-storage
local     mydata
```

### 컨테이너에서 데이터 저장
```bash
PS C:\docker-practice> docker run -it --name test-cnt1 -v my-hankyul-volume:/data ubuntu bash
root@986910c77884:/# echo "Hankyul volume test success!" > /data/test.txt
root@986910c77884:/# cat /data/test.txt
Hankyul volume test success!
```

### 컨테이너 삭제 후 새 컨테이너에서 데이터 확인
```bash
PS C:\docker-practice> docker rm -f test-cnt1
test-cnt1
PS C:\docker-practice> docker run -it --name test-cnt2 -v my-hankyul-volume:/data ubuntu bash
root@ac41adf2fcd7:/# cat /data/test.txt
Hankyul volume test success!
# 컨테이너가 삭제됐어도 데이터가 그대로 유지됨
```

## 13. Git 설정 및 GitHub 연동
```bash
root@ac41adf2fcd7:/# git config --list --global
user.name=rukimarineogohangyeol
user.email=yongjussi91@gmail.com
```

GitHub 저장소 연동 및 push 완료:
```bash
root@ac41adf2fcd7:/data# git add .
root@ac41adf2fcd7:/data# git commit -m "add Dockerfile, app, practice"
[main b4ad34f] add Dockerfile, app, practice
 1 file changed, 1 insertion(+)

root@ac41adf2fcd7:/data# git push -u origin main --force
Enumerating objects: 6, done.
Counting objects: 100% (6/6), done.
Delta compression using up to 18 threads
Compressing objects: 100% (3/3), done.
Writing objects: 100% (6/6), 553 bytes | 553.00 KiB/s, done.
Total 6 (delta 0), reused 0 (delta 0), pack-reused 0
To https://github.com/rukimarineogohangyeol/docker-assignment.git
 + 871dd12...b4ad34f main -> main (forced update)
```

## 14. 트러블슈팅

### 트러블슈팅 1 — Windows PowerShell 환에서 chmod 미동작

- **문제**:Windows PowerShell 환경에서 chmod 명령어를 사용하여 파일 권한 변경을 시도했으나, 해당 명령어를 인식하지 못하거나 권한 체계가 일치하지 않는 오류 발생.
- **원인 분석**: Windows의 NTFS 파일 시스템은 권한 관리를 위해 ACL(Access Control List) 방식을 사용함.
-반면 chmod는 리눅스의 POSIX 표준 권한(rwx) 관리 명령어로, 두 운영체제의 파일 시스템 및 권한 관리 체계가 근본적으로 다르기 때문임.
- **해결**: 호스트 OS(Windows)의 한계를 인지하고, Docker Desktop을 통해 가상의 Ubuntu Linux 컨테이너 환경을 구축함.

docker run -it ubuntu bash 명령어로 컨테이너 내부 터미널에 직접 접속하여 리눅스 표준 환경을 구현함.

해당 리눅스 환경 내부에서 chmod 명령어가 정상 작동함을 직접 확인하고, 이를 실습 로그로 확보하여 리포트에 반영함.

### 트러블슈팅 2 — 빌드 엔진 통신 오류 (INTERNAL_ERROR)

- **문제**: `docker build 실행 시 Internal: stream terminated by RST_STREAM with error code: INTERNAL_ERROR 발생하며 중단됨.
- **원인 가설**: 최신 빌드 엔진인 BuildKit과 Windows 가상화 네트워크 환경 간의 일시적 통신 충돌.
- **확인**: `작업 디렉터리를 변경하고 Docker를 재시작해도 동일 증상 반복됨을 확인.
- **해결**: 환경 변수 $env:DOCKER_BUILDKIT=0 설정을 통해 BuildKit을 비활성화하고 레거시 빌더를 사용하여 정상적으로 빌드 프로세스 진입 성공.

### 트러블슈팅 3 — Dockerfile 구문 해석 오류 (Unknown Instruction)
**문제**: 빌드 시작 시 dockerfile parse error on line 1: unknown instruction: FROM 오류 발생.
**원인 가설**: Windows PowerShell의 echo 명령어로 생성된 파일에 BOM(Byte Order Mark) 인코딩이 포함되어 도커 엔진이 첫 줄을 인식하지 못함.
**확인**: 에러 메시지의 `` 문자를 통해 인코딩 깨짐 현상 확인.
**해결**: PowerShell에서 [System.IO.File]::WriteAllLines 메서드를 활용하여 UTF-8(BOM 없음) 형식으로 Dockerfile을 재생성한 후 빌드 성공.

### 트러블슈팅 4 — 컨테이너 이름 중복 충돌 (Conflict)
**문제**: docker run 실행 시 Conflict. The container name "/my-nginx-container" is already in use 오류 발생.
**원인 가설**: 동일한 --name 옵션을 가진 컨테이너가 이미 생성되어 있거나 실행 중임.
**확인**: docker ps -a 명령을 통해 동일한 이름을 가진 기존 컨테이너의 존재 확인.
**해결**: docker rm -f [컨테이너명] 명령어로 기존 컨테이너를 삭제한 후 재실행하여 해결.

## 15. 검증 방법 — 재현 가능한 실행 방법

아래 명령어를 순서대로 실행하면 전체 환경을 재현할 수 있다.
```bash
# 1. 저장소 클론 (질문자님 실제 주소)
git clone https://github.com/rukimarineogohangyeol/docker-assignment.git
cd docker-assignment

# 2. 이미지 빌드
# 실습에서 사용한 베이스 이미지를 기반으로 빌드 (예: my-ubuntu-image)
docker build -t my-docker-app .

# 3. 볼륨 생성 및 데이터 보존 확인
docker volume create assignment-vol
docker run -it --name test-cnt2 -v assignment-vol:/data ubuntu bash

# 4. 컨테이너 내부 작업 (데이터 생성)
# root@ac41adf2fcd7:/data# echo "Assignment Complete" > test_file.txt

# 5. Git 연동 및 Push (PAT 토큰 사용)
git remote add origin https://rukimarineogohangyeol:[본인_토큰]@github.com/rukimarineogohangyeol/docker-assignment.git
git push -u origin main --force

# 6. GitHub 저장소 확인
# https://github.com/rukimarineogohangyeol/docker-assignment

**확인**: docker attach 또는 exec를 통해 컨테이너 접속 시, 볼륨으로 연결된 /data 디렉터리 내 파일이 정상적으로 보존됨을 확인.

**해결**: 초기 GitHub 연동 시 발생한 Authentication failed 오류를 Personal Access Token(PAT) 인증 방식으로 해결하였으며, 이력 불일치 문제는 --force 옵션을 사용하여 성공적으로 동기화함.
