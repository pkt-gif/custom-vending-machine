# 🥤 AXI4-Lite 기반 커스터마이징 음료 자판기 (Customizable Vending Machine)

![FPGA](https://img.shields.io/badge/FPGA-Artix--7-orange)
![Board](https://img.shields.io/badge/Board-Basys3-blue)
![Language](https://img.shields.io/badge/Language-Verilog_HDL-green)
![Tool](https://img.shields.io/badge/Tool-Vivado_2023.2-red)

> **Basys3 (Artix-7) FPGA** 보드를 활용하여 하드웨어 가속 및 제어 로직을 구현한 **'사용자 맞춤형 음료 자판기'** 프로젝트입니다. 
> AXI4-Lite 버스 프로토콜을 직접 설계하여 모듈 간 통신을 구현하였으며, IP 코어의 재사용성을 높이기 위해 Wrapper-Core 계층 구조를 적용했습니다.

---

## 📑 Table of Contents
1. [Project Overview](#1-project-overview)
2. [System Architecture](#2-system-architecture)
3. [Key Features & Scenarios](#3-key-features--scenarios)
4. [Hardware Setup & Pinmap](#4-hardware-setup--pinmap)
5. [Implementation Results](#5-implementation-results)
6. [Trouble Shooting](#6-trouble-shooting)

---

## 1. Project Overview

* **프로젝트명:** 커스터마이징 음료 자판기 RTL 설계 및 보드 검증
* **개발 환경:** Xilinx Vivado, Verilog HDL
* **타겟 보드:** Digilent Basys3 (Xilinx Artix-7)
* **핵심 목표:** 
  * 5종의 음료와 당도(5단계), 얼음양(5단계)을 조합하는 복합 FSM 설계
  * **AXI4-Lite 프로토콜**을 적용한 Master-Slave 데이터베이스 분리 설계
  * 서보 모터(구동계)와 FSM 간의 **비동기식 핸드셰이크(Handshake)** 제어
  * 하드웨어 계층화(Hierarchy) 및 모듈화 기법 적용

---

## 2. System Architecture

본 시스템은 역할을 명확히 분리하기 위해 4개의 핵심 구역(Zone)으로 모듈화하여 설계되었습니다.

### 🧠 Core Architecture Design
* **Zone 1 [사용자 인터페이스]:** 스위치(음료/당도/얼음 선택) 및 버튼(금액 투입/엔터/반환), 기본 FND 제어
* **Zone 2 [Main FSM - 두뇌]:** 사용자 입력을 취합하여 상태를 결정하고 하위 모듈에 제어 펄스(`req_write/read`, `flag_servo`)를 하달하는 중앙 제어기
* **Zone 3 [AXI4-Lite 통신부 - DB]:** 
  * 통신 프로토콜을 전담하는 **Wrapper (AXI Slave)**와 순수 데이터 연산만 수행하는 **Core (Inventory)**로 계층을 분리 (Decoupling).
* **Zone 4 [하드웨어 구동계]:** 서보 모터 PWM 제어 및 외부 LED 애니메이션을 담당. Main FSM의 연산을 막지 않기 위해 비동기 타이머 기반으로 동작 후 `완료 펄스(cplt)` 반환.

*(※ 깃허브 마크다운에서 아래 Mermaid 다이어그램이 자동 렌더링됩니다.)*

```mermaid
flowchart LR
    Z1[Zone 1: UI Input] -->|Button Pules / SW| Z2(Zone 2: Main FSM)
    Z2 -->|fsm_state| Z1_OUT[Zone 1: UI Output FND]
    
    Z2 <-->|AXI4-Lite 5-Channel Handshake<br>32-bit Data & Trigger| Z3[(Zone 3: AXI Slave DB)]
    
    Z2 -->|Command 펄스| Z4[Zone 4: Servo & LED Actuators]
    Z4 -->|완료 Feedback 펄스| Z2
