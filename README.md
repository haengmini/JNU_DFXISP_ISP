# JNU DFXISP ISP: 카메라 원본(RAW) 영상 처리 단독 실험

이 저장소는 [DFXISP 연구](https://github.com/haengmini/dfxisp)에서 **ISP 부분만** 떼어 낸 것입니다. FPGA 보드, Vivado, PetaLinux 없이 일반 PC에서 ISP 계산과 검증을 돌려 볼 수 있습니다.

**ISP**는 카메라 센서가 낸 원본 데이터(RAW)를 사람이 보는 컬러 영상으로 바꾸는 처리 과정입니다. 이 저장소에는 두 가지 ISP가 들어 있습니다.

| ISP | 설명 |
|---|---|
| `default_ISP` | 밝은 장면용 표준 ISP. AMD Vitis Vision 라이브러리의 처리 순서를 따릅니다. |
| `lowlight_ISP` | 어두운 장면용 ISP. 2x2 묶기(binning), 노출 이득, 노이즈 보정 톤 변환 등 저조도 처리를 더한 것입니다. |

## 폴더 구성

| 폴더 | 내용 |
|---|---|
| `cpp/` | 정수 연산으로 만든 C++ ISP 소스(`src/`, `include/`)와 시험 코드(`tests/`), 골든 벡터 CSV. 이 코드가 FPGA 회로(HLS)로 옮겨 가는 기준입니다. |
| `python/` | 같은 ISP를 Python으로 다시 구현한 기준 코드, 골든 벡터 생성기, 장면 밝기 판단기(`checker.py`) |
| `python/sim/` | 단계별 실험(블랙 레벨, 게인, 묶기, 감마, 화이트 밸런스, checker) 스크립트와 결과 보고서, 그림 |
| `docs/` | `default_isp.md`, `lowlight_isp.md` 설계 노트 |

## 바로 확인해 보기

C++ 컴파일러(g++)와 Python 3, numpy가 필요합니다.

```text
pip install -r requirements.txt
make test
```

`make test`는 아래 세 가지를 차례로 확인합니다.
1. C++ ISP 결과가 골든 벡터와 한 픽셀도 다르지 않게 일치하는지(`default_ISP` 528픽셀, `lowlight_ISP` 147픽셀)
2. 영상 크기의 입력을 빠르게 처리하는 Python 구현이 C++ 기준 구현과 비트 단위로 일치하는지
3. Python이 새로 만든 골든 벡터가 저장소에 있는 CSV와 바이트 단위로 같은지

## 단계별 실험 실행

`python/sim/` 아래 스크립트는 각 단계가 왜 그렇게 설계됐는지 보여 주는 실험입니다. 각 폴더의 `*.md`와 `*_report.md`에 방법과 결과가 있습니다.

```text
cd python/sim/blc
python3 blc_sim.py --help
```

- 이 스크립트들은 matplotlib을 추가로 요구하고, `AWB/` 일부 스크립트는 rawpy와 Pillow도 요구합니다.
- 실제 RAW 데이터셋(LOD, PASCALRAW)은 이 저장소에 없습니다. 데이터는 [JNU_DFXISP_AI](https://github.com/haengmini/JNU_DFXISP_AI)의 `dataset/README.md`에 안내된 위치에서 받아야 합니다. 데이터가 없으면 검증용 `make test`만 실행됩니다.
- 스크립트 머리말의 `isppipeline/sw/...` 같은 경로는 원본 저장소의 옛 경로 표기입니다. 이 저장소에서는 `python/`, `cpp/`, `docs/` 아래에 있습니다.

## 전체 연구에서 이 저장소의 위치

| 단계 | 저장소 |
|---|---|
| ISP만 PC에서 실험 | 이 저장소 |
| 연구 전체와 결과, 보드 구현 | [dfxisp](https://github.com/haengmini/dfxisp) |
| ZCU104와 PetaLinux 기본 익히기 | [JNU_Lab_Petalinux](https://github.com/haengmini/JNU_Lab_Petalinux) |
| FPGA 설계(HLS, DFX 흐름) | [JNU_DFXISP_FPGA](https://github.com/haengmini/JNU_DFXISP_FPGA) |
| 물체 검출 모델과 데이터셋 | [JNU_DFXISP_AI](https://github.com/haengmini/JNU_DFXISP_AI) |

## 출처

[`haengmini/dfxisp`](https://github.com/haengmini/dfxisp)의 `isppipeline/sw`, `isppipeline/hls`에서 ISP 관련 파일만 골라 커밋 기록과 함께 옮겼습니다. 원본과의 차이는 폴더 이름과 빌드용 `Makefile`, 이 README뿐입니다.
