# AXI4-Lite 기반 커스터마이징 음료 자판기

> Basys3 FPGA 보드와 AXI4-Lite 프로토콜을 활용해 주문, 결제, 재고 관리 및 음료 배출을 구현한 RTL 자판기 시스템입니다.

## 프로젝트 정보

- 개발 기간: 2026.06.24 ~ 2026.06.30
- 개발 형태: 4인 팀 프로젝트
- 개발 환경: Digilent Basys3, Xilinx Vivado, Verilog HDL

## 프로젝트 개요

사용자가 5종의 음료와 당도·얼음양을 각각 5단계로 선택할 수 있는 커스터마이징 자판기입니다.

메인 FSM이 사용자 입력과 전체 동작을 제어하며, 결제 및 재고 데이터는 AXI4-Lite Master/Slave 통신으로 처리합니다. 서보모터와 LED 구동에는 Handshake 방식을 적용해 각 모듈의 동작 완료 여부를 확인하도록 구성했습니다.

## 주요 기능

- 5종 음료 및 당도·얼음양 5단계 선택
- 동전 투입, 결제, 잔액 계산 및 반환
- AXI4-Lite 기반 결제·재고 데이터 통신
- 서보모터를 이용한 음료 배출
- LED Bar를 이용한 제조 과정 및 재고 상태 표시
- 7-Segment를 통한 금액·선택 정보·상태 메시지 출력
- 재고 및 잔액 상태에 따른 예외 처리

## 시스템 동작 흐름

```text
버튼 · 스위치 입력
        ↓
     Main FSM
        ├─ AXI4-Lite Master ↔ Slave / Inventory Core
        │                          └─ 결제 · 재고 처리
        └─ Handshake ─────────────→ LED Bar · 서보모터
        ↓
   7-Segment 상태 출력
```

## 예외 처리

| 출력 | 동작 |
|---|---|
| `LESS` | 투입 금액이 부족한 경우 결제를 중단하고 추가 투입 또는 반환 대기 |
| `NONE` | 선택한 음료의 재고가 없는 경우 다른 음료를 다시 선택 |
| `END` | 모든 재고가 소진된 경우 입력을 차단하고 영업 종료 상태로 전환 |

## 담당 역할

- AXI4-Lite Master Interface 설계 및 5개 채널 Handshake 검증
- 버튼·스위치 기반 입출력 UI 및 물리 구조 설계
- Basys3 전체 하드웨어 설계 및 통합

## 기술 스택

`Basys3` · `Verilog HDL` · `Xilinx Vivado` · `AXI4-Lite` · `FSM` · `PWM`

## 데모 영상

[YouTube 시연 영상](https://www.youtube.com/watch?v=7eshwTPMTOo)
