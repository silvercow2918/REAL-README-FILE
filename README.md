# TOP · PS · JOBS · KILL

* * *

## 목차

1. 프로젝트 개요  
2. 사전 요구사항  
3. 실행 환경 준비(설치 방법)  
4. 명령어 한눈에 보기  
5. 명령어별 사용법  
   * top – 실시간 시스템 모니터링  
   * ps – 프로세스 스냅샷 조회  
   * jobs – 현재 셸 작업 목록  
   * kill – 프로세스 종료  
6. 자주 사용하는 조합 예시  
7. 자주 발생하는 문제와 해결 방법  
8. 심화 학습 및 참고 자료  
9. 변경 로그  
10. 라이선스  
11. 작성자 및 문의  

* * *

## 사전 요구사항

이 문서를 따라 하기 위해서는 다음과 같은 환경이 필요합니다.

- 리눅스 환경 (또는 WSL / 가상머신 Ubuntu 등)  
- 기본적인 터미널 사용 능력  
- Bash 셸 사용(대부분 기본값)  

* * *

## 실행 환경 준비(설치 방법)

별도의 설치 없이, **대부분의 리눅스 배포판에는 기본 포함**되어 있습니다.

### 1) 리포지토리 클론(예시)

```bash
git clone https://github.com/yourname/linux-process-commands.git
cd linux-process-commands
````

### 2) 명령어 존재 여부 확인

```bash
which top
which ps
type jobs   # bash 내장 명령어
which kill
```

> 최소 설치 환경에서는 `procps` 패키지 등이 빠져 있을 수 있습니다.
> 그런 경우 패키지 관리자로 설치하면 됩니다.

```bash
# Debian/Ubuntu
sudo apt install procps

# RHEL/CentOS
sudo yum install procps-ng
```

---

## 명령어 설명

| 명령어    | 역할                | 특징                   |
| ------ | ----------------- | -------------------- |
| `top`  | 실시간 시스템/프로세스 모니터링 | CPU·메모리 사용률 실시간 갱신   |
| `ps`   | 프로세스 “스냅샷” 조회     | 실행 시점의 상태만 1회 출력     |
| `jobs` | 현재 셸 작업 목록 확인     | 백그라운드/중지 작업 상태 확인    |
| `kill` | 프로세스에 시그널 전송      | 종료, 일시중지, 재개 등 제어 가능 |

---

## 명령어별 사용법

### 1. top – 실시간 시스템 모니터링

> CPU, 메모리 사용량 및 실행 중인 프로세스를 **실시간으로 확인**하는 명령어

#### (1) 기본 사용

```bash
top
```

```bash
# 특정 사용자 프로세스만 보기
top -u 사용자이름

# 배치 모드(스크립트/로그용)로 7번만 출력
top -b -n 7 > top_output.txt
```

#### (2) 화면 주요 항목

상단 요약 영역:

* `load average` : 1 / 5 / 15분 평균 부하
* `Tasks` : 전체 프로세스 개수(실행/대기/중지/좀비 등)
* `%Cpu(s)` : user / system / idle 등 CPU 사용률
* `Mem`, `Swap` : 메모리 / 스왑 사용량

하단 프로세스 목록의 대표 컬럼:

| 항목        | 설명           |
| --------- | ------------ |
| `PID`     | 프로세스 ID      |
| `USER`    | 프로세스 실행 사용자  |
| `%CPU`    | CPU 사용률      |
| `%MEM`    | 메모리 사용률      |
| `TIME+`   | 누적 CPU 사용 시간 |
| `COMMAND` | 실행된 명령어/프로그램 |

#### (3) 자주 사용하는 단축키

* `q` : `top` 종료
* `h` : 도움말 표시
* `P` : CPU 사용률 기준 정렬
* `M` : 메모리 사용률 기준 정렬
* `k` : 선택한 프로세스 종료(내부적으로 `kill` 사용)
* `1` : CPU 코어별 사용률 표시 토글

---

### 2. ps – 프로세스 스냅샷 조회

> 실행 중인 프로세스를 **“한 번” 스냅샷 형태로 출력**하는 명령어

#### (1) 기본 사용

```bash
ps            # 현재 터미널의 프로세스
ps -e         # 시스템 전체 프로세스
ps -ef        # 전체 + 풀 포맷
ps aux        # CPU/메모리 사용률 포함 전체
ps -u 사용자  # 특정 사용자 프로세스만
```

#### (2) 자주 쓰는 옵션

| 옵션    | 설명                     |
| ----- | ---------------------- |
| `-e`  | 시스템의 모든 프로세스 출력        |
| `-f`  | UID, PPID 등 풀 포맷       |
| `-u`  | 특정 사용자가 실행한 프로세스만 출력   |
| `aux` | BSD 스타일 출력(CPU/메모리 포함) |

#### (3) 실전 예제

```bash
# 시스템 전체 프로세스를 긴 포맷으로
ps -ef

# CPU 사용률 기준 상위 10개
ps aux --sort=-%cpu | head -n 10

# 메모리 사용률 기준 상위 10개
ps aux --sort=-%mem | head -n 10

# 특정 프로그램 검색
ps aux | grep nginx
ps -ef | grep python
```

---

### 3. jobs – 현재 셸 작업 목록

> 현재 셸에서 실행 중이거나 멈춘 작업(job)을 보여주는 **bash 내장 명령어**

#### (1) 기본 사용

```bash
jobs     # 현재 셸의 작업 목록
jobs -l  # PID까지 함께 표시
```

#### (2) 백그라운드 실행 및 확인 예시

```bash
sleep 100 &
sleep 200 &
jobs
```

출력 예시:

```text
[1]   Running    sleep 100 &
[2]-  Running    sleep 200 &
```

> `[1]+`, `[2]-` 표시는 **기본 대상(+)과 그 이전(-) 작업**을 나타내며,
> `fg`, `bg` 사용 시 기본으로 선택되는 순서를 의미합니다.

#### (3) 주요 옵션

* `jobs -l` : 작업 번호 + PID 출력
* `jobs -p` : PID만 출력
* `jobs -r` : 실행 중(Running) 작업만
* `jobs -s` : 일시중지(Stopped) 작업만

#### (4) 관련 명령어

```bash
fg %1   # 1번 job을 포그라운드로
bg %1   # 1번 job을 백그라운드에서 계속 실행
kill %1 # 1번 job에 시그널 전송(실제 PID 대신 %번호 사용)
```

---

### 4. kill – 프로세스에 시그널 전송(종료)

> PID 또는 job 번호를 이용해 **종료·일시중지·재개 등의 시그널을 보내는 명령어**

#### (1) 기본 사용

```bash
kill PID        # 기본: SIGTERM(15)
kill -9 PID     # 강제 종료(SIGKILL)
```

기본 시그널은 **`SIGTERM(15)`** 으로, “정상 종료 요청”에 해당합니다.

#### (2) 자주 쓰는 시그널

| 번호 | 이름      | 설명                    |
| -- | ------- | --------------------- |
| 2  | SIGINT  | 인터럽트(Ctrl + C와 유사)    |
| 9  | SIGKILL | 강제 종료(잡을 수 없고 무조건 종료) |
| 15 | SIGTERM | 정상 종료 요청(기본값)         |

> 실무에서는 보통 `kill PID` → 안 되면 마지막에 `kill -9 PID` 순으로 시도합니다.

#### (3) ps와 조합한 예제

```bash
# 1) ps로 PID 확인
ps -ef | grep python

# 2) 정상 종료 요청
kill 5678        # == kill -15 5678

# 3) 말 안 들을 때 강제 종료
kill -9 5678
```

#### (4) 프로세스 그룹 / 전체 종료 (주의)

```bash
# 매우 위험: 현재 로그인한 사용자 프로세스 대부분 종료
kill -9 -1

# 프로세스 그룹 ID(PGID)가 1234인 그룹 전체에 SIGTERM
kill -15 -1234
```

---

## 자주 사용하는 조합 예시

### 1) CPU를 많이 사용하는 프로세스 찾아서 종료하기

```bash
# CPU 사용률 기준 상위 10개
ps aux --sort=-%cpu | head -n 10

# 문제 프로세스 종료
kill 12345
```

### 2) top에서 바로 프로세스 종료하기

```text
top 실행
  ↓
문제 프로세스를 방향키로 선택
  ↓
k 입력 → PID 확인 후 시그널 번호 입력(기본: 15)
```

### 3) jobs + fg/bg + kill 조합

```bash
# 1) 긴 작업 실행 후 Ctrl+Z로 잠시 멈춤
python long_task.py
^Z

# 2) 백그라운드로 재개
bg %1

# 3) 현재 작업 확인
jobs

# 4) 다시 포그라운드로 가져오기
fg %1

# 5) 필요하면 종료
kill %1
```

---

## 자주 발생하는 문제와 해결 방법

실제로 명령어를 사용할 때 자주 만나는 문제들을 정리했습니다.

### 1) `top` 관련 문제

#### Q1. `top: command not found` 가 뜰 때

**원인**

* 최소 설치 환경(미니멀 ISO, 컨테이너 등)에서 `procps` 패키지가 설치돼 있지 않은 경우

**해결 방법**

```bash
# Debian/Ubuntu
sudo apt update
sudo apt install procps

# RHEL/CentOS
sudo yum install procps-ng
```

---

#### Q2. `top` 화면 글자가 깨져 보이거나 한글이 이상하게 나올 때

**원인**

* 서버의 로케일(locale) 설정이 깨져 있거나, 클라이언트 터미널 인코딩이 맞지 않을 때

**해결 방법**

```bash
# 서버 로케일 확인
locale

# UTF-8 로케일이 없다면 설치/설정
sudo locale-gen ko_KR.UTF-8
sudo update-locale LANG=ko_KR.UTF-8
```

터미널도 **UTF-8** 로 설정되어 있는지 확인합니다.

---

#### Q3. `top` 화면 갱신 속도가 너무 빠르거나 느릴 때

**해결 방법**

* `top` 실행 상태에서 `d` 키를 눌러 **refresh 간격(초)**를 변경
* 실행 시 옵션으로 지정:

```bash
top -d 2   # 2초마다 갱신
```

---

### 2) `ps` 관련 문제

#### Q1. `ps aux`가 안 되고 옵션 오류가 날 때 (특히 busybox 환경)

**현상 예시**

```text
ps: unrecognized option: aux
```

**원인**

* BusyBox의 `ps`는 **BSD 스타일 옵션을 지원하지 않음**
* Docker의 알파인 이미지 등 최소 환경에서 자주 발생

**해결 방법**

* 지원되는 형식으로 사용:

```bash
ps        # 기본
ps -o pid,ppid,user,stat,command
```

또는 **`procps` 패키지 설치** 후 일반적인 `ps aux` 사용.

---

#### Q2. `ps aux | grep something` 했을 때 `grep` 자신도 같이 나올 때

**원인**

* `grep something` 프로세스 자신도 검색 결과에 포함되기 때문

**해결 방법**

```bash
ps aux | grep [n]ginx
ps aux | grep '[b]ash'
```

또는 `pgrep`, `pidof` 등을 활용:

```bash
pgrep nginx
pidof nginx
```

---

### 3) `jobs` / `fg` / `bg` 관련 문제

#### Q1. 다른 터미널에서 실행한 프로세스가 `jobs`에 안 보임

**원인**

* `jobs`는 **현재 셸 세션에만** 해당
* 다른 터미널/다른 세션에서 실행한 작업은 보이지 않음

**해결 방법**

* 시스템 전체 프로세스는 `ps`, `top`을 사용해서 확인합니다.

```bash
ps aux | grep 프로그램이름
```

---

#### Q2. `fg %1` 실행 시 `no such job` 오류

**원인**

* 해당 job이 이미 종료되었거나
* 셸을 새로 열어서 **기존 job 정보가 사라진 상태**

**해결 방법**

* 다시 `jobs`로 목록을 확인하고, 없다면 프로세스를 새로 실행해야 합니다.
* 프로세스가 실제로 살아 있는지 확인하려면:

```bash
ps aux | grep 프로그램이름
```

---

### 4) `kill` 관련 문제

#### Q1. `kill: (PID) - Operation not permitted` / `Permission denied`

**원인**

* **본인이 소유하지 않은 프로세스** 또는
* 시스템 핵심 프로세스(root 소유)를 일반 계정으로 종료하려고 한 경우

**해결 방법**

```bash
# 프로세스 소유자를 확인
ps -o pid,user,command -p PID

# 정말 필요한 경우, 관리자 권한으로 실행
sudo kill PID
```

> 단, 시스템 핵심 프로세스를 종료하면 서버가 다운될 수 있으므로 주의가 필요합니다.

---

#### Q2. `kill`을 했는데 프로세스가 계속 살아 있을 때

**가능한 원인**

* `SIGTERM`(15)을 보냈지만 프로그램이 종료 요청을 무시하거나, 종료 처리에 시간이 걸리는 경우
* 이미 **좀비(zombie) 상태**인 프로세스 (부모가 `wait()` 하지 않음)

**해결 방법**

1. 우선 `SIGTERM` → 안 되면 `SIGKILL` 순서로 시도

```bash
kill PID      # SIGTERM
kill -9 PID   # SIGKILL
```

2. 좀비 프로세스라면, **부모 프로세스(Parent PID)**를 종료해야 할 수 있음

```bash
ps -o pid,ppid,stat,command -p PID
```

`STAT`에 `Z`가 있다면 좀비 상태입니다.

---

#### Q3. `kill -9 -1` 등으로 실수로 너무 많이 죽였을 때

**원인**

* 프로세스 그룹/모든 프로세스에 시그널을 보내는 위험한 사용

**해결 방법**

* 미리 **명령어 의미를 정확히 이해**한 후 사용
* 실수로 시스템이 불안해졌다면, **로그인 세션을 정리**하거나,
  최악의 경우 **시스템 재부팅**이 필요할 수 있습니다.

> 이 문서에서는 교육 목적으로만 예시를 보여주며,
> 실제 서버/서비스 환경에서는 `kill -9 -1` 같은 명령은 사용하지 않는 것을 권장합니다.

---

## 심화 학습 및 참고 자료

### 1) 공식 매뉴얼

* `man top`
* `man ps`
* `help jobs` (bash 내장)
* `man kill`

### 2) 참고한 문서 출처

* top 명령어 설명(Naver 블로그)

  * [https://m.blog.naver.com/appeal7712/222400108716](https://m.blog.naver.com/appeal7712/222400108716)
* ps 명령어 설명(Naver 블로그)

  * [https://blog.naver.com/tmk0429/222318530824](https://blog.naver.com/tmk0429/222318530824)
* jobs 명령어 설명(Tistory)

  * [https://monkeybusiness.tistory.com/630](https://monkeybusiness.tistory.com/630)
* kill 명령어 설명(Tistory)

  * [https://monkeybusiness.tistory.com/626](https://monkeybusiness.tistory.com/626)

***

