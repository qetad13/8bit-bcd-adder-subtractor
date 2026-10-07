# 8-Bit BCD Adder / Subtractor

> 디지털 회로 실험 및 설계 Term Project · **2025.04 – 2025.06** · 5인 팀 (팀장)

74 시리즈 TTL IC로 만든 **두 자리 BCD(00–99) 가감산기**입니다.
DIP 스위치로 두 수를 입력하고, 토글 스위치로 가산/감산 모드를 고르면 결과가 7-세그먼트 2자리에 10진수로 표시됩니다.
Multisim으로 설계·시뮬레이션한 뒤 브레드보드 검증 → 만능기판 납땜 → 3D 프린팅 케이스까지 제작했습니다.

<p align="center">
  <img src="images/final_product.jpg" width="360" alt="최종 완성품">
</p>

## 주요 기능

- **입력**: DIP 스위치 A, B — 각 8비트 (BCD 2자리)
- **모드 선택**: 토글 스위치 (ADD / SUB), 감산 모드일 때 LED 점등
- **감산**: XOR(74LS86)로 B를 1의 보수로 만들고 Cin = 1을 넣어 2의 보수 덧셈으로 처리
- **BCD 보정**: 자리값이 9를 넘거나 캐리가 생기면 보정값을 더해 올바른 BCD로 변환
- **출력**: 74LS47 디코더 2개 → 7-세그먼트 2자리 (십의 자리 / 일의 자리)

## Block Diagram

<p align="center"><img src="docs/block_diagram.svg" width="720" alt="Block Diagram"></p>

## Flow Chart

<p align="center"><img src="docs/flowchart.svg" width="520" alt="Flow Chart"></p>

## 회로도

최종 회로 파일: [`circuit/bcd_adder_subtractor_final.ms14`](circuit/bcd_adder_subtractor_final.ms14) (NI Multisim 14)

![회로도 블록 구분](images/schematic_annotated.png)

| 색 | 블록 | 구성 |
|---|---|---|
| 🟨 노랑 | 입력 제어부 | DIP 스위치 S1(A), S2(B) + 1 kΩ 풀업 저항 |
| ⬛ 검정 | 가감산 선택 | 토글 스위치 S3 (SUB 신호) |
| 🟩 초록 | 보수 연산 및 제어 | 74LS86 ×2 — B ⊕ SUB |
| 🟦 파랑 | 4비트 가산기 | 74LS83 — U1(하위)·U2(상위) 이진 가감산, U15·U5·U6 보정 가산 |
| 🟪 보라 | 조합 논리 회로 | 74LS08 · 74LS32 — 보정 조건(자리값 > 9, 캐리) 판별 |
| 🩵 하늘 | 멀티플렉서 | 74LS153 — 가산/감산 모드에 따른 보정 경로 선택 |
| 🟥 빨강 | BCD → 7세그먼트 디코더 | 74LS47 ×2 |

### 감산 원리 (2의 보수)

```
A − B = A + (~B + 1)
```

| SUB | B ⊕ SUB | Cin | 연산 |
|:---:|:---:|:---:|---|
| 0 | B | 0 | A + B |
| 1 | ~B (1의 보수) | 1 | A + ~B + 1 = A − B |

74LS83은 4비트 가산기이므로 두 개를 직렬로 연결(U1 Cout → U2 Cin)해 8비트 연산을 합니다.

## 부품 목록

| 부품 | 용도 | 수량 |
|---|---|:---:|
| 74LS83 | 4비트 이진 가산기 | 5 |
| 74LS86 | XOR 게이트 (보수 생성) | 2 |
| 74LS08 | AND 게이트 (보정 조건) | 1 |
| 74LS32 | OR 게이트 (보정 조건) | 1 |
| 74LS153 | 듀얼 4:1 멀티플렉서 | 1 |
| 74LS47 | BCD → 7세그먼트 디코더 | 2 |
| 7-Segment (Common Anode) | 결과 2자리 표시 | 2 |
| DIP 스위치 (8핀) | A, B 입력 | 2 |
| 토글 스위치 | 가산/감산 선택 | 1 |
| LED | 감산 모드 표시 | 1 |
| 저항 1 kΩ / 330 Ω | 스위치 풀업 / LED 전류 제한 | 16 / 1 |
| IC 소켓 14핀 / 16핀 | IC 장착 | 3 / 4 |
| DC 잭 + 5 V 어댑터 | 전원 | 1 |

## 결과

### 시뮬레이션 (Multisim)

| 가산: 6 + 12 = 18 | 감산: 14 − 8 = 6 |
|:---:|:---:|
| <img src="images/sim_add.png" width="400"> | <img src="images/sim_sub.png" width="400"> |

### 브레드보드 구현

<p align="center"><img src="images/breadboard.jpg" width="420"></p>

| 가산: 5 + 8 = 13 | 감산: 12 − 4 = 8 |
|:---:|:---:|
| <img src="images/breadboard_add.jpg" width="400"> | <img src="images/breadboard_sub.jpg" width="400"> |

브레드보드에서 처음엔 플로팅 현상이 생겨, 스위치 출력단에 풀업 저항을 달아 해결했습니다.

### 3D 모델링 (케이스)

| 윗면 | 바닥면 |
|:---:|:---:|
| <img src="images/3d_top.jpg" width="380"> | <img src="images/3d_bottom.jpg" width="380"> |

### 납땜 및 최종 완성품

| 만능기판 납땜 | 가산 모드 (LED OFF) | 감산 모드 (LED ON) |
|:---:|:---:|:---:|
| <img src="images/soldered_board.jpg" width="260"> | <img src="images/final_add.jpg" width="260"> | <img src="images/final_sub.jpg" width="260"> |

동작 영상: [`videos/demo_1.mp4`](videos/demo_1.mp4), [`videos/demo_2.mp4`](videos/demo_2.mp4)

## 고찰

- 시뮬레이션에서는 정상이던 회로가 실제 납땜 후 다르게 동작해, 배선을 재구성하고 연결을 다시 점검해서 해결했습니다.
- `15 − 8`처럼 결과의 십의 자리가 바뀌는 경우 세그먼트 출력 오류가 있었고, 자리 올림/내림 보정 로직을 수정해 해결했습니다.
- 계산기처럼 작게 만들려고 IC를 촘촘히 배치하다 보니 배선 정리와 합선 디버깅에 시간이 많이 들었습니다. 다음에는 KiCad 등으로 PCB를 직접 설계할 계획입니다.

## 팀 구성

5인 팀 프로젝트 (디지털 회로 실험 및 설계 2조)

- **김준형 (팀장)** — 시뮬레이션, 부품 구매, 회로 구성, 납땜
- 팀원 4명 — 자료조사, 시뮬레이션, 3D 모델링, 보고서·PPT 작성, 발표

## 참고 자료

- [74LS83 Datasheet](https://www.alldatasheet.co.kr/datasheet-pdf/view/51091/FAIRCHILD/74LS83.html)
- [74LS47 Datasheet](https://www.alldatasheet.co.kr/datasheet-pdf/view/51080/FAIRCHILD/74LS47.html)
- [74LS153 Datasheet](https://www.alldatasheet.co.kr/datasheet-pdf/view/27384/TI/74LS153.html)
