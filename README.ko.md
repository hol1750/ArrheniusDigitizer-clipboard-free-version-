> Inspired by existing plot digitizing tools; this is an independent open-source implementation.

[English](README.md) | **한국어**

# ArrheniusDigitizer

이미지 속 그래프(특히 **아레니우스 플롯**)에서 데이터 점을 클릭만으로 추출해
`csv` / `json` 으로 저장하는 데스크톱 디지타이저.

- 논문·교과서의 그래프 이미지에서 수치 데이터를 복원
- **linear / log 축** 자동 처리, 축 보정 2점만 찍으면 됨
- 클릭 시 곡선에 **자동 스냅**(에지 검출), 수동 모드 토글 가능
- 추출 후 **선형 회귀**(기울기·R²) 자동 계산 — 아레니우스 기울기에서 활성화에너지 분석에 활용
- 한글 라벨/폰트 지원


---

## 요구 사항

- Python 3.10+
- 의존성: `numpy`, `matplotlib`, `opencv-python-headless`, `pillow`
- GUI 표시를 위한 `tkinter` (공식 윈도우/맥 설치본은 기본 포함, 리눅스는 `python3-tk` 필요)

## 실행 (소스에서)

`uv` 사용 시 별도 설치 없이 바로 실행:

```bash
uv run --with numpy,matplotlib,opencv-python-headless,pillow plotdigitizer1234.py
```

이미지 경로를 바로 넘길 수도 있습니다:

```bash
uv run --with numpy,matplotlib,opencv-python-headless,pillow plotdigitizer1234.py /path/to/graph.png
```

## 사용법

1. 실행하면 이미지 입력을 받습니다.
   - 터미널에 이미지 파일을 **드래그&드롭** (윈도우 `C:\`·`D:\` 경로 자동 변환)
   - `v` 또는 빈 엔터 → **클립보드 이미지** 사용 (`Win+Shift+S` 캡처 등)
   - 경로 직접 입력도 가능
2. 창이 뜨면 **축 보정**: X축에서 값을 아는 점 2개, Y축에서 2개를 클릭하고 실제 값을 입력.
3. **데이터 점 클릭** → 곡선에 자동 스냅. 우클릭은 마지막 점 취소.
   - 스냅이 어긋나면 `Manual` 버튼으로 수동 클릭 모드 전환.
4. 닫으면 결과가 저장됩니다.

## 출력

실행 파일(또는 스크립트) 위치의 `digitized/` 폴더에 저장:

```
digitized/<이미지명>.csv    # x,y 점 데이터 (엑셀에서 바로 열림)
digitized/<이미지명>.json   # 축 종류·기울기·R² 등 메타데이터
```

(클립보드 이미지는 현재 폴더에 `clipboard_<시각>.png` 로 먼저 저장됩니다.)

## 윈도우 `.exe` 빌드 / 배포

Python·환경설정 없이 더블클릭으로 쓰는 단일 실행파일은 `windows_build/` 에서 빌드합니다.
빌드 절차와 주의사항은 [`READMEexe.md`](READMEexe.md) 참고.

최신 빌드된 `.exe` 는 **[Releases](../../releases)** 탭에서 받을 수 있습니다.

---

## 라이선스

이 프로젝트는 [MIT 라이선스](LICENSE) 하에 공개됩니다. 자유롭게 사용·수정·재배포할 수
있으며, 저작권 고지만 유지하면 됩니다.

## Acknowledgments

Inspired by existing plot digitizing tools (WebPlotDigitizer, Engauge Digitizer 등).
This is an independent, from-scratch open-source implementation and is not affiliated
with any of them.
