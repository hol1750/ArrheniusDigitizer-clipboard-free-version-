> Inspired by existing plot digitizing tools; this is an independent open-source implementation.

# PlotDigitizer1234 — 윈도우 배포 빌드

WSL용 원본 `../plotdigitizer1234.py` 를 **수정 없이** 네이티브 윈도우용 단일 실행파일
(`.exe`)로 패키징하기 위한 폴더. 일반 사용자는 Python·WSL 없이 exe 더블클릭만으로 사용.

```
windows_build/
  plotdigitizer_win.py   진입점: 원본 import + WSL 전용 함수 3개만 윈도우용으로 교체
  build.bat              윈도우용 PyInstaller 빌드 스크립트
  README.md              이 문서
```

---

## 동작 원리 (왜 원본을 안 고치나)

원본은 WSL 가정(`wslpath`, `explorer.exe`, `/mnt/wslg` 검사)이 있어 네이티브 윈도우에서
그대로 안 돈다. `plotdigitizer_win.py` 는 원본을 **import 만** 하고 다음 3개만 덮어쓴다:

| 함수 | 원본(WSL) | 윈도우 교체 |
|---|---|---|
| `check_display_environment` | `/mnt/wslg`·DISPLAY 검사 | 항상 통과 (윈도우 GUI 기본 동작) |
| `open_image_async` | `wslpath -w` → `explorer.exe` | `os.startfile()` |
| `_clean_dropped_path` | `C:\…` → `/mnt/c` (wslpath) | 따옴표만 제거, `C:\…` 그대로 사용 |

클립보드(`_grab_clipboard_image`)는 윈도우 네이티브 `ImageGrab` 이 추가 설치 없이
동작하므로 교체하지 않는다.

→ 본체 기능을 고칠 땐 **`../plotdigitizer1234.py` 만 수정**하면 exe에도 자동 반영
(이 3개 함수의 이름/시그니처를 바꿀 때만 `plotdigitizer_win.py` 도 점검).
수정 후에는 **exe를 다시 빌드**해야 반영된다.

---

## 빌드 방법 (윈도우에서)

> ⚠️ 빌드는 반드시 **윈도우**에서. WSL/리눅스에서 빌드하면 윈도우에서 안 도는
> 리눅스 바이너리가 나온다. (PyInstaller는 크로스 컴파일 미지원)

WSL 에서는 빌드하지 않는다. 아래 3개 파일을 **윈도우 폴더 하나에 같이 복사**한 뒤
`build.bat` 만 더블클릭한다:

```
D:\어디든\
  plotdigitizer1234.py     ← WSL 원본을 복사 (고치면 다시 복사)
  plotdigitizer_win.py
  build.bat
```

1. 윈도우에 Python 3.x 설치 (`build.bat` 의 `set "PY=..."` 가 그 python.exe 를 가리키게).
2. 위 3개 파일을 윈도우 폴더 하나에 복사.
3. `build.bat` 더블클릭.
4. 완료되면 `dist\PlotDigitizer1234.exe` 생성. **이 exe 하나만 배포**.

> WSL 원본(`/workspace/src/plotdigitizer1234.py`)이 단일 소스. 윈도우 폴더의 사본은
> 빌드용이며, 본체를 고치면 그 사본을 다시 복사해야 반영된다(`plotdigitizer_win.py` 도 동일).

### 전역 Python 오염 없음 (venv 격리 + 자동 정리)

`build.bat` 은 의존성을 전역 Python 이 아니라 그 폴더의 `.venv` 안에만 설치하고,
빌드가 끝나면 **`.venv` 를 자동 삭제**한다 → 안 쓰는 윈도우 Python 에 라이브러리가
남지 않는다. 중간 산출물 `build\` 폴더와 `*.spec` 은 남으니 원하면 수동 삭제(있어도
exe 동작엔 무관).

> ⚠️ `build.bat` 은 **CP949 인코딩**으로 저장돼 있다(한글 메시지 + `D:\프로그램\` 같은
> 한글 경로 때문). 에디터로 열어 **UTF-8 로 다시 저장하면 한글이 깨져 빌드가 실패**한다.

수동 빌드(같은 동작을 손으로, 3개 파일이 있는 폴더에서):
```bat
"전역python" -m venv .venv
.venv\Scripts\python.exe -m pip install pyinstaller pillow opencv-python-headless matplotlib numpy
.venv\Scripts\python.exe -m PyInstaller --onefile --paths . --name PlotDigitizer1234 --collect-data matplotlib plotdigitizer_win.py
rmdir /s /q .venv
```

---

## 사용자(배포 대상) 실행 방법

- `PlotDigitizer1234.exe` 더블클릭 → 콘솔 창이 뜬다.
- 이미지 지정:
  - 이미지 파일을 콘솔 창에 **드래그&드롭** (경로 자동 입력, `C:\`·`D:\` 모두 가능)
  - 또는 `v` / 빈 엔터 = **클립보드에 복사된 이미지** 사용 (캡처 `Win+Shift+S` 등)
  - 또는 경로 직접 입력
- 결과 JSON은 이미지와 같은 폴더(클립보드 이미지는 현재 폴더 `clipboard_<시각>.png` 옆)에 저장.

---

## 참고 / 트러블슈팅

- **용량**: `--onefile` 기준 약 100~300MB (cv2/numpy/matplotlib/tkinter 동봉). 정상.
- **첫 실행이 느림**: onefile은 실행 시 `%TEMP%\_MEIxxxxxx` 에 임시 압축해제 후 구동
  (종료 시 자동 삭제). 시스템에 영구 설치되는 것은 없다.
- **백신 오탐**: 서명 없는 onefile은 가끔 백신이 의심 처리. 줄이려면 `--onefile` 대신
  `--onedir`(폴더 배포) 사용 또는 코드서명.
- **matplotlib 폰트/데이터 누락**으로 실행 오류 시: `--collect-data matplotlib`(기본 포함)
  외에 `--collect-all matplotlib` 로 강화. 한글 깨짐 시 폰트 파일을 `--add-data` 로 동봉.
- **tkinter 관련 오류**: 빌드 PC의 Python에 tkinter가 포함돼 있어야 함(공식 윈도우 설치본은 기본 포함).
