<div align="center">

# 👋 Hi, I'm K (Daehoon Kim)

**Enterprise Automation Developer & AI System Engineer**

*Building DX experiences through automation, AI integration, and data-driven systems*

[![Instagram](https://img.shields.io/badge/@iamdaehoon-E4405F?style=flat-square&logo=instagram&logoColor=white)](https://www.instagram.com/iamdaehoon/)
[![GitHub](https://img.shields.io/badge/eogns6357-181717?style=flat-square&logo=github&logoColor=white)](https://github.com/eogns6357)

</div>

---

## 🌐 Experience

| Period | Role |
|--------|------|
| 2023.01 ~ | **Enterprise Automation Developer & AI System Engineer** @ Parnas Hotel |
| 2024.01 ~ 2024.12 | Engineer @ GS Holdings 52g (파견) |
| 2021.06 ~ 2022.12 | **Developer** @ DEX Consulting |
| 2021.10 ~ 2022.12 | GS Retail DCX추진팀 RPA Center (파견) |

---

## 🚀 Projects

### 🏨 Hotel OTA Booking Automation (OWS Handler)
> OTA 예약 이메일 → Opera PMS 자동 처리 시스템

호텔 OTA(야놀자, 네이버 등)에서 수신되는 예약 이메일을 파싱하여  
Oracle OWS SOAP API를 통해 Opera PMS에 자동으로 예약을 생성·수정·취소하는 서버리스 시스템

**Architecture Flow**
```
OTA 예약 이메일
    └── AWS SES → S3 (raw .eml 저장)
            └── AWS Lambda (트리거)
                    ├── Rule Parser (정형 이메일 규칙 기반 파싱)
                    └── Amazon Bedrock Agent (비정형/복잡 이메일 AI 파싱)
                            └── Oracle OWS SOAP API
                                    └── Opera PMS (예약 생성 / 수정 / 취소)
                                            └── DynamoDB (실행 로그 기록)
```

**주요 구현 내용**
- OTA별 이메일 포맷 차이를 흡수하는 **이중 파싱 파이프라인** 설계
  - 1차: 규칙 기반 파서
  - 2차: Amazon Bedrock Agent fallback (비정형·복잡 케이스 AI 처리)
- 한글 성명 → 로마자 변환 로직 (성씨별 관용 표기 + 종성 보정 처리)
- Oracle OWS SOAP XML 요청 생성 및 응답 파싱 (`create` / `modify` / `cancel`)
- 멀티룸 예약 처리: DynamoDB 상태 추적 기반 분할 전송 및 그룹 관리
- 수신 이메일 주소(To 헤더) 기반 멀티 호텔 라우팅
- Bedrock API 일시 장애 대응

**Stack**
`Python` `AWS Lambda` `Amazon S3` `Amazon SES` `Amazon DynamoDB` `Amazon Bedrock` `Oracle OWS` `SOAP/XML` `boto3`


---

### 🎟️  상품권 불출 관리 시스템
> Gift Voucher Management System

호텔 상품권 발행·불출·서명·정산 전 과정을 디지털화한 사내 전용 시스템
```
**Architecture Flow**
Next.js 16 (App Router / TypeScript)
    └── AWS API Gateway
            └── AWS Lambda (Python 3.11 · Serverless Framework)
                    ├── Amazon DynamoDB (재고 · 불출 이력 관리)
                    └── Microsoft Graph API
                                └── SharePoint (불출대장 동기화)
```

**주요 구현 내용**
- **역할 분리 UI** — 관리자(발행·승인·정산) / 수령자(모바일 수령·서명) 흐름 분리
- 전자 서명 캡처 및 서명 이미지 저장 (`react-signature-canvas`)
- 불출 이력 Excel 내보내기 (`xlsx`)
- Microsoft MSAL(Azure AD) SSO 기반 관리자 인증
- SharePoint 상품권 불출대장 자동 동기화 (Microsoft Graph API)
- DynamoDB 단일 테이블 설계로 재고 추적 및 이력 관리

**Stack:** `Next.js 16` `React 19` `TypeScript` `Tailwind CSS` `Python 3.11` `AWS Lambda` `Amazon DynamoDB` `Serverless Framework` `Microsoft Graph API` `Azure MSAL`

---

### 🎙️  VoiceClip — 실시간 다국어 음성 번역 시스템
 > 호텔 프론트데스크용 실시간 음성 번역 웹 애플리케이션 (PoC)

외국인 투숙객과 직원 간 언어 장벽을 해소하기 위한 브라우저 기반 실시간 번역 시스템.
버튼 하나로 녹음 → 언어 감지 → 번역 → 음성 재생까지 자동으로 처리됩니다.

**Architecture Flow**
```
마이크 입력 (MediaRecorder API)
    └── Azure Speech SDK (브라우저 Client-side STT)
            ├── 자동 언어 감지 (ko / en / ja / zh-CN / fr-FR / hi-IN)
            └── 음성 → 텍스트 변환
                    └── Next.js API Route (/api/translate-and-speak)
                            ├── Azure OpenAI GPT-4o (텍스트 번역)
                            └── Azure Speech Service (Server-side TTS)
                                    └── MP3 Base64 → 브라우저 재생
```

**주요 구현 내용**
- STT는 브라우저에서, 번역·TTS는 서버에서 처리하는 **분산 파이프라인** 설계
  - 오디오 파일 업로드 없이 Azure Speech SDK 클라이언트 직접 실행
  - API 키는 서버에만 보관 (`/api/speech-config` 엔드포인트로 안전하게 노출)
- 언어 변경 시 **재녹음 없이** 기존 원문을 즉시 재번역하는 UX 최적화
- 녹음 종료 즉시 자동 번역 트리거 (별도 버튼 제거)
- 언어별 캐릭터 아이콘 UI로 타깃 언어 선택

**Stack:** `Next.js 14` `React 18` `TypeScript` `Azure Speech SDK` `Azure OpenAI` `Tailwind CSS` `Vercel`

---

### 📈 개인용 주식 트레이딩 대시보드
> Personal Korean Stock Trading Dashboard

한국 주식 단기 매매를 위한 개인용 웹 대시보드.
KIS OpenAPI로 실시간 시세·차트를 수신하고, Gemini AI 기술 분석과 Telegram 알림을 통합한 올인원 트레이딩 시스템.

**Architecture Flow**
```
KIS OpenAPI (한국투자증권)
    └── Next.js API Routes (서버사이드 전용)
            ├── 현재가 / 일봉 OHLCV / KOSPI·KOSDAQ 지수
            ├── 스크리너 (30개 종목 × 200일 → 점수 정렬)
            └── Google Gemini 2.0 Flash (AI 기술분석)
                    └── Telegram Bot API (알림 발송)
```

**주요 구현 내용**
- KIS OpenAPI 토큰 23시간 메모리 캐시 및 `kisGet()` 헬퍼 추상화
- 30개 종목 스크리너: RSI / MACD / Bollinger Band / SMA / ATR 지표 계산 후 점수 정렬
- lightweight-charts v5 기반 캔들스틱 + 거래량 복합 차트
- Gemini AI 기술분석 리포트 자동 생성 (POST `/api/ai`)
- Telegram Bot 연동 알림 발송 및 QR 코드 설정 UI
- 포지션 사이징 리스크 계산기 / localStorage 기반 매매 일지

**Stack:** `Next.js 16` `TypeScript` `Tailwind CSS v4` `shadcn/ui` `KIS OpenAPI` `Google Gemini 2.0 Flash` `Telegram Bot API` `lightweight-charts v5` `Vercel`

---

### 📊 BI & 대시보드 구축
> Business Intelligence & Dashboard Development

- 경영진·현업 대상 KPI 기반 Power BI 대시보드 설계
- 데이터 소스 통합 및 자동 갱신 리포팅 환경 구축
- **Stack:** `Power BI` `SharePoint` `M365`

---

### 🤖 RPA 워크플로우
> RPA Workflow Automation

- 반복 업무 자동화를 통한 업무 효율화 및 오류 감소
- 크로스 시스템 데이터 연동 및 예외 처리 로직 설계
- **Stack:** `Power Automate` `UiPath` `Python`

---

### 🧠 AI 교육 프로그램
> Enterprise AI Education Program

- 사내 GenAI·LLM 활용 역량 강화 교육 커리큘럼 기획 및 운영
- 프롬프트 엔지니어링, RAG, AI 워크플로우 실습 과정 구성
- **Stack:** `Azure OpenAI` `Notion` `MISO AI`

---

## 🛠️ Tech Stack

### Automation & RPA
![Power Automate](https://img.shields.io/badge/Power%20Automate-0066FF?style=for-the-badge&logo=Microsoft&logoColor=white)
![RPA](https://img.shields.io/badge/RPA-FF6B35?style=for-the-badge&logo=robot&logoColor=white)

### AI & Data
![Azure OpenAI](https://img.shields.io/badge/Azure%20OpenAI-0078D4?style=for-the-badge&logo=Microsoft-Azure&logoColor=white)
![Power BI](https://img.shields.io/badge/Power%20BI-F2C811?style=for-the-badge&logo=Power%20BI&logoColor=black)

### Integration & Backend
![Oracle](https://img.shields.io/badge/Oracle%20OWS-F80000?style=for-the-badge&logo=Oracle&logoColor=white)
![Python](https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=Python&logoColor=white)

### Productivity & Platform
![Microsoft 365](https://img.shields.io/badge/Microsoft%20365-D83B01?style=for-the-badge&logo=Microsoft-Office&logoColor=white)
![SharePoint](https://img.shields.io/badge/SharePoint-0078D4?style=for-the-badge&logo=Microsoft-SharePoint&logoColor=white)

---

## 🇰🇷 About Me

저는 **Developer | DX Strategy** 분야에서

업무 자동화, 데이터 분석, AI 기반 기능 도입, 시스템 연동을 설계·개발하고 있습니다.

업무 흐름을 구조적으로 재해석하고, 불필요한 단계를 제거하며,

사용자 중심의 자동화와 데이터 흐름을 설계하는 방식으로 DX를 추진하고 있습니다.

---

<div align="center">

⭐ From [K](https://github.com/eogns6357)

</div>
