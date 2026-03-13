# LinM 한글 설치 가이드

LinM은 Linux용 텍스트 UI 파일 관리자(Mdir 클론)입니다. 이 문서는 사용자 홈 경로(`$HOME/.local`)에 안전하게 설치하는 방법을 안내합니다.

- 오리지널 프로젝트: https://github.com/la9527/linm
- fork (최신 패치 포함): https://github.com/zirize/linm
- 이 저장소: LinM 설치 가이드 문서 전용

---

## 1) 사전 준비 (의존성 설치)

빌드에 필요한 도구와 라이브러리를 먼저 설치합니다. (관리자 권한 필요)

### Debian / Ubuntu
```bash
sudo apt-get update
sudo apt-get install -y build-essential cmake git gettext \
    libncursesw5-dev libssl-dev libssh2-1-dev \
    libsource-highlight-dev
```

### Arch Linux
```bash
sudo pacman -S --needed base-devel cmake git gettext \
    ncurses openssl libssh2 source-highlight
```

### Fedora / RHEL / CentOS
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

## 2) 소스 클론 및 사용자 경로 설치

아래 방식은 `sudo` 없이 사용자 경로에 설치하므로 시스템 전역 환경에 영향을 주지 않습니다.

> 최신 패치(v0.9.3)가 포함된 fork를 클론하려면 아래 URL을 사용하세요:
> ```bash
> git clone https://github.com/zirize/linm.git
> ```
> 오리지널 저장소를 사용하려면:
> ```bash
> git clone https://github.com/la9527/linm.git
> ```

```bash
cd linm

mkdir -p build
cd build

cmake -DCMAKE_INSTALL_PREFIX=$HOME/.local ..
make
make install
```

설치 후 `$HOME/.local/bin`이 `PATH`에 없다면 `~/.bashrc`에 추가합니다:
```bash
export PATH="$HOME/.local/bin:$PATH"
source ~/.bashrc
```

---

## 3) `linm.sh` 경로 보정

작업 디렉토리 유지 기능을 사용하려면 바이너리(`linm`)가 아닌 스크립트(`linm.sh`)를 통해 실행해야 합니다.

1. 파일 열기:
   ```bash
   nano $HOME/.local/bin/linm.sh
   ```
2. 실행 라인을 확인해 실제 경로로 변경:
    - 설치 상태에 따라 `@bindir@/linm $@` 또는 `OFF/linm $@` 형태일 수 있습니다.
    - 두 경우 모두 플레이스홀더이므로, 실제 바이너리 경로로 바꿔야 합니다.
   ```bash
   /home/<사용자명>/.local/bin/linm $@
   ```

예: 사용자명이 `zirize`이면 아래와 같습니다.
```bash
/home/zirize/.local/bin/linm $@
```

---

## 4) Bash alias 등록 (`.bash_aliases` 권장)

Bash에서는 `~/.bashrc` 직접 수정 대신 `~/.bash_aliases`에 alias를 두는 방식을 권장합니다.

1. 파일 열기(없으면 생성):
   ```bash
   nano ~/.bash_aliases
   ```
2. 아래 alias 추가:
   ```bash
   alias linm='. $HOME/.local/bin/linm.sh'
   ```
   > ⚠️ 점(`.`)과 공백이 반드시 있어야 현재 셸 세션에 디렉토리 변경이 반영됩니다.
3. 적용:
   ```bash
   source ~/.bash_aliases
   source ~/.bashrc
   ```

`~/.bash_aliases`가 자동 로드되지 않으면 `~/.bashrc`에 아래가 있는지 확인하세요.
```bash
if [ -f ~/.bash_aliases ]; then
    . ~/.bash_aliases
fi
```

---

## 5) 동작 확인

```bash
linm
```

실행 후 다른 디렉토리로 이동해서 종료했을 때, 터미널 현재 경로가 마지막 작업 위치로 유지되면 정상입니다.

---

## 6) 버전 업그레이드

새 버전 빌드·설치 후 처음 실행하면 자동으로 설정을 업그레이드합니다:

- 기존 설정을 `~/.config/linm/back/`에 백업
- 새로운 설정 키만 병합 (기존 커스터마이징 유지)
- 설정 반영을 위해 자동 재시작

별도로 설정을 초기화하거나 파일을 복사할 필요가 없습니다.

---

## 주요 단축키 (v0.9.3 기준)

| 키 | 기능 |
|----|------|
| `Alt+F` | 파일명 필터 (실시간, 디렉토리 제외, 이동 시 유지) |
| `Alt+S` | 정렬 방식 팝업 선택 (None/Name/Ext/Size/Time/Color) |
| `Alt+X` / `Ctrl+Q` / `Ctrl+D` | 종료 |
| `F8` / `Alt+D` | 삭제 |
| `F1` | 도움말 |
| `Ctrl+B` | 설정 변경 |
| `Ctrl+O` | 서브쉘 |
| `Ctrl+F` | 파일 찾기 |

전체 단축키는 `F1` 도움말 또는 `~/.config/linm/keyset.cfg`를 참고하세요.

---

## 마우스 비활성화 옵션

```bash
# 실행 시 옵션
linm --nomouse
linm -M

# 환경변수
LINM_NO_MOUSE=1 linm

# ~/.config/linm/default.cfg 설정
Mouse = Off
```
