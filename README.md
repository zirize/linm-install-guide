# Installation Guide for LinM

LinM은 리눅스용 비주얼 파일 관리자(Mdir 클론)입니다. 이 문서는 주요 배포판별 설치 방법과 환경 설정 가이드를 제공합니다.

---

## 1. 배포판별 의존성 설치

빌드에 필요한 도구와 라이브러리를 먼저 설치해야 합니다. (이 단계는 시스템 관리자 권한이 필요할 수 있습니다.)

### **A. Debian / Ubuntu 계열**
```bash
sudo apt-get update
sudo apt-get install -y build-essential cmake git gettext \
    libncursesw5-dev libssl-dev libssh2-1-dev \
    libsource-highlight-dev
```

### **B. Arch Linux**
```bash
sudo pacman -S --needed base-devel cmake git gettext \
    ncurses openssl libssh2 source-highlight
```

### **C. Red Hat / Fedora / CentOS 계열**
```bash
# Fedora
sudo dnf install -y gcc-c++ cmake git make gettext \
    ncurses-devel openssl-devel libssh2-devel \
    source-highlight-devel

# CentOS/RHEL (EPEL 저장소 활성화 필요)
sudo yum install -y gcc-c++ cmake3 git make gettext \
    ncurses-devel openssl-devel libssh2-devel \
    source-highlight-devel
```

---

## 2. 소스 클론 및 빌드 (Root 권한 없이 설치)

사용자 계정의 홈 디렉토리 내에 설치하여 시스템 전역에 영향을 주지 않고 활용할 수 있습니다.

### **A. 소스 코드 가져오기**
```bash
# GitHub에서 프로젝트 복제
git clone https://github.com/la9527/linm.git
cd linm
```

### **B. 빌드 및 사용자 레벨 설치**
`-DCMAKE_INSTALL_PREFIX`를 `$HOME/.local`로 설정하면 `sudo` 없이 설치가 가능합니다.
```bash
# 빌드 디렉토리 생성
mkdir build
cd build

# 사용자 계정 내 설치 경로 설정
cmake -DCMAKE_INSTALL_PREFIX=$HOME/.local ..
make

# 설치 (sudo 불필요)
make install
```

---

## 3. 실행 스크립트(linm.sh) 및 바이너리 경로 수정

`linm`을 종료한 후에도 작업하던 디렉토리를 유지하려면 실제 바이너리가 아닌 `linm.sh` 스크립트를 통해 실행해야 합니다.

1.  **스크립트 위치 확인**: `$HOME/.local/bin/linm.sh`에 위치합니다.
2.  **바이너리 경로 수정**:
    에디터로 해당 파일을 열고 `@bindir@` 부분을 실제 바이너리가 설치된 경로인 `$HOME/.local/bin`으로 수정합니다. (직접 전체 경로를 입력해 주세요.)

```bash
# 수정 전 (예시)
@bindir@/linm $@

# 수정 후 (예: 사용자가 'bill'인 경우)
/home/bill/.local/bin/linm $@
```

---

## 4. Alias 등록 (종료 시 디렉토리 유지)

`linm` 종료 시 해당 위치를 유지하려면 쉘 설정 파일(`~/.bashrc` 또는 `~/.zshrc`)에 아래 alias를 등록하십시오.

1.  **쉘 설정 파일 열기**:
    ```bash
    nano ~/.bashrc  # 또는 nano ~/.zshrc
    ```

2.  **아래 내용 추가**:
    반드시 앞에 점(`.`)과 공백이 있어야 현재 쉘 세션에 디렉토리 변경이 반영됩니다.
    ```bash
    # 사용자 로컬 설치 기준
    alias linm='. $HOME/.local/bin/linm.sh'
    ```

3.  **설정 적용**:
    ```bash
    source ~/.bashrc
    ```

이제 터미널에서 `linm` 명령어로 실행하면, 종료 시에도 마지막 작업 디렉토리가 유지됩니다.
