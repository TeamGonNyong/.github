# 🕵️ What Is Me? - 키워드 추리 게임

![Java](https://img.shields.io/badge/Java-ED8B00?style=for-the-badge&logo=openjdk&logoColor=white)
![Socket](https://img.shields.io/badge/Socket-Programming-blue?style=for-the-badge)
![Swing](https://img.shields.io/badge/Java-Swing-orange?style=for-the-badge)

**Team Gon-Nyong** | 한성대학교 네트워크 프로그래밍 팀 프로젝트

---

## 📑 목차

1. [📖 프로젝트 개요](#-프로젝트-개요)
2. [👥 팀 소개](#-팀-소개)
3. [✨ 주요 기능](#-주요-기능)
   - [🎮 게임 시스템](#-게임-시스템)
   - [🎨 낙서 시스템](#-낙서-시스템)
   - [🖼️ UI/UX](#️-uiux)
4. [🏗️ 아키텍처](#️-아키텍처)
   - [패키지 구조](#패키지-구조)
   - [실행 흐름](#실행-흐름)
5. [🚀 실행 방법](#-실행-방법)
6. [🎯 게임 방법](#-게임-방법)
7. [🔧 프로토콜 명세](#-프로토콜-명세)
8. [💡 핵심 기술](#-핵심-기술)
9. [🎨 외부 API](#-외부-api)
10. [🐛 알려진 이슈](#-알려진-이슈)
11. [📝 라이선스](#-라이선스)

---

## 📖 프로젝트 개요

What Is Me?는 Java 소켓 프로그래밍을 활용한 2인용 네트워크 기반 키워드 추리 게임입니다.

각 플레이어는 상대방에게 **YES/NO 질문**을 던져 상대방의 숨겨진 키워드를 먼저 맞추는 것이 목표입니다. 정답을 맞춘 플레이어는 상대방 얼굴에 **3초간 낙서**할 수 있는 보상을 얻으며, 낙서가 **60%를 초과**하면 최종 승리합니다!

---

## 👥 팀 소개

**Team Gon-Nyong (곤뇽)**

| 프로필 | 이름 | 역할 | 담당 업무 | GitHub |
|:------:|------|------|-----------|:------:|
| <img src="https://github.com/Dawon-Y.png" width="80" height="80"> | **양다원** | Full-stack Developer | UI 디자인 & 프론트엔드 개발 | [![GitHub](https://img.shields.io/badge/-@Dawon--Y-181717?style=flat-square&logo=github)](https://github.com/Dawon-Y) |
| <img src="https://github.com/SeoukwooLee.png" width="80" height="80"> | **이석우** | Data Engineer | 백엔드 | [![GitHub](https://img.shields.io/badge/-@SeoukwooLee-181717?style=flat-square&logo=github)](https://github.com/SeoukwooLee) |

---

## ✨ 주요 기능

### 🎮 게임 시스템

- **턴제 질문/답변**: 플레이어가 번갈아가며 YES/NO 질문 주고받기
- **랜덤 키워드 배정**: 20개 키워드 풀에서 중복 없이 랜덤 배정
- **정답 맞추기**: `/guess [키워드]` 명령어로 언제든지 정답 도전 가능
- **자동 턴 전환**: 질문/답변 후 자동으로 턴 전환

### 🎨 낙서 시스템

- **3초 제한 낙서**: 정답 맞춘 후 3초간 낙서 권한 부여
- **60% 게임 종료**: 얼굴 면적의 60% 낙서 시 최종 승리/패배
- **실시간 면적 계산**: 픽셀 기반 낙서 면적 측정 (알파 채널 활용)
- **낙서 도구**: 8가지 색상, 브러시 크기 조절 (1~20)

### 🖼️ UI/UX

- **셜록 홈즈 테마**: 메인 메뉴 셜록 실루엣 디자인
- **아바타 API 연동**: [DiceBear Avatars API](https://api.dicebear.com) 사용하여 매 게임 랜덤 얼굴 생성
- **반응형 레이아웃**: 좌측 얼굴 패널 + 우측 게임 UI (2단 구조)
- **승리/패배 팝업**: 게임 종료 시 다이얼로그 (다시 하기 / 메인으로)
- **채팅 스타일링**: 시스템/플레이어 메시지 색상 구분

---

## 🏗️ 아키텍처

### 패키지 구조
```bash
WhatIsMe/
├── src/
│   ├── GameServer.java                    # 게임 서버 (포트 30000)
│   └── WhatIsMe/
│       ├── Main.java                      # 애플리케이션 진입점
│       ├── game/
│       │   ├── GameManager.java          # 게임 상태 관리 (Mediator 패턴)
│       │   └── GameUICallback.java       # UI 콜백 인터페이스
│       ├── network/
│       │   ├── GameNetworkManager.java   # 실제 네트워크 통신
│       │   ├── NetworkInterface.java     # 네트워크 인터페이스
│       │   ├── GameDataListener.java     # 게임 데이터 리스너
│       │   ├── MessageListener.java      # 메시지 리스너
│       │   └── DummyNetwork.java         # 테스트용 더미 구현
│       └── ui/
│           ├── MainMenuFrame.java        # 메인 메뉴
│           ├── GameFrame.java            # 메인 게임 화면
│           ├── FacePanel.java            # 얼굴 표시 및 낙서 (핵심!)
│           ├── ChatPanel.java            # 채팅 + 질문/답변 UI
│           └── PlayerPanel.java          # 키워드 카드
└── resources/
    └── sherlock.png                       # 배경 이미지
```

### 실행 흐름
```bash
GameServer.java (서버)
    ↕ (TCP 소켓 통신)
GameNetworkManager.java (클라이언트 네트워크)
    ↓
GameManager.java (게임 로직 - Mediator)
    ↓
GameFrame.java (UI 컨트롤러)
    ↓
    ├─ ChatPanel.java (채팅 UI)
    ├─ FacePanel.java (낙서 UI)
    └─ PlayerPanel.java (키워드 UI)
```

---

## 🚀 실행 방법

### 요구 사항

- **Java**: JDK 8 이상
- **IDE**: Eclipse (권장) 또는 IntelliJ IDEA
- **네트워크**: 로컬 또는 동일 네트워크 내 연결
- **OS**: Windows, macOS, Linux 모두 지원

### 실행 순서

#### 1. 서버 실행
```bash
# Eclipse에서 GameServer.java 실행
# 또는 터미널에서
javac GameServer.java
java GameServer
```

→ 서버가 포트 **30000**에서 대기합니다.

#### 2. 클라이언트 1 실행
```bash
# Eclipse에서 Main.java 실행
# 또는 터미널에서
javac WhatIsMe/Main.java
java WhatIsMe.Main
```

→ 서버 주소 입력: `localhost`, 포트: `30000`

#### 3. 클라이언트 2 실행
```bash
# 동일하게 Main.java 한 번 더 실행
```

→ 두 클라이언트 접속 완료 시 **게임 자동 시작**!

---

## 🎯 게임 방법

### 기본 규칙

1. 각 플레이어는 비밀 키워드를 받습니다
2. 턴마다 상대방에게 **YES/NO 질문**을 할 수 있습니다
3. 질문을 통해 상대방의 키워드를 추리하세요
4. 정답이라고 생각하면 `/guess [키워드]`로 제출하세요

### 낙서 승리

- 정답을 맞춘 후 **3초 동안** 낙서 기회가 주어집니다
- 상대방 얼굴의 **60% 이상**을 낙서하면 즉시 승리!
- 60% 미달 시 **자동으로 새 라운드** 시작

### 주의사항

- ❌ 정답을 틀리면 패배합니다
- ❌ 낙서가 60%를 초과당하면 패배합니다

---

## 🔧 프로토콜 명세

### 📤 클라이언트 → 서버

| 명령어 | 설명 | 예시 |
|--------|------|------|
| `/guess [키워드]` | 정답 제출 | `/guess 사과` |
| `/restart` | 게임 재시작 요청 | `/restart` |
| `/doodle_full` | 낙서 60% 도달 알림 | `/doodle_full` |
| `[일반 텍스트]` | 질문 전송 | `빨간색인가요?` |

### 📥 서버 → 클라이언트

| 프로토콜 | 설명 | 예시 |
|----------|------|------|
| `KEYWORD:[키워드]` | 자신의 키워드 할당 | `KEYWORD:사과` |
| `TURN:MY` | 내 턴 | `TURN:MY` |
| `TURN:OPPONENT` | 상대방 턴 | `TURN:OPPONENT` |
| `QUESTION:[질문]` | 상대방 질문 수신 | `QUESTION:빨간색인가요?` |
| `ANSWER:[YES/NO]` | 상대방 답변 수신 | `ANSWER:YES` |
| `DOODLE_START` | 낙서 모드 시작 (3초) | `DOODLE_START` |
| `DOODLE_VICTORY` | 낙서 60% 승리 | `DOODLE_VICTORY` |
| `DOODLE_DEFEAT` | 낙서 60% 패배 | `DOODLE_DEFEAT` |
| `SYSTEM:[메시지]` | 시스템 메시지 | `SYSTEM:게임 시작!` |

---

## 💡 핵심 기술

### 네트워크

- **TCP 소켓 통신**: `ServerSocket`, `Socket`
- **멀티스레드**: 클라이언트별 독립 스레드 (`ClientHandler`)
- **비동기 메시지 송수신**: `BufferedReader`, `PrintWriter`

### GUI

- **Java Swing**: `JFrame`, `JPanel`, `JButton`, `JTextPane`
- **커스텀 UI**: `Graphics2D`를 활용한 셜록 실루엣 그리기
- **반응형 레이아웃**: `BorderLayout`, `FlowLayout`, `GridLayout`

### 디자인 패턴

- **Mediator 패턴**: `GameManager`가 네트워크와 UI 중재
- **Listener 패턴**: `GameDataListener`, `MessageListener`, `GameUICallback`
- **Interface 기반 설계**: `NetworkInterface`로 느슨한 결합

### 이미지 처리

- **BufferedImage**: 이중 레이어 (얼굴 + 낙서)
- **알파 채널 감지**: 투명도 기반 낙서 면적 계산
- **외부 API**: DiceBear Avatars API v7.x

---

## 🎨 외부 API

### DiceBear Avatars API v7.x

매 게임마다 랜덤 아바타 이미지 생성

**URL 포맷**:
```
https://api.dicebear.com/7.x/avataaars/png?seed=[random]&size=700
```

---

## 🐛 알려진 이슈

- 네트워크 지연 시 턴 전환이 약간 느릴 수 있습니다
- 3명 이상 동시 접속은 지원하지 않습니다 (2인 전용)

---

## 📝 라이선스

본 프로젝트는 한성대학교 네트워크 프로그래밍 수업의 텀프로젝트로 제작되었습니다.

---

**Made with ❤️ by Team Gon-Nyong**
