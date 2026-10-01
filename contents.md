# Structured Bloom

## 프로젝트 개요

**Structured Bloom**은 사용자가 현재 상태를 자유로운 문장으로 입력하고  
사용 가능한 시간을 선택하면, AI가 입력 내용을 정리하여  
현재 상태에 맞는 작은 회복 활동을 제안하는 웹 서비스입니다.

사용자가 복잡한 계획을 직접 세우기보다  
현재 느끼는 상태를 문장으로 적고 사용할 수 있는 시간만 선택하면  
실행 가능한 활동과 첫 행동을 구체적으로 제안하도록 구성했습니다.

---

## 서비스 주소

- 소개 페이지: https://s23.aiweb2026.site
- 실제 구현 페이지: https://s23.aiweb2026.site/app/
- GitHub Repository: https://github.com/lightleaping/structured-bloom-ai-service

---

## 왜 만들었는가

피로하거나 생각이 복잡한 상태에서는  
쉬어야 한다는 것을 알아도 무엇부터 해야 할지 결정하기 어려울 수 있습니다.

Structured Bloom은 사용자의 상태를 긴 설문으로 받는 대신  
자유로운 문장과 사용 가능한 시간이라는 간단한 입력을 사용합니다.

입력된 내용을 AI가 정리하고,  
현재 상황에서 바로 시작할 수 있는 작은 행동을 추천하는 것을 목표로 했습니다.

---

## 입력

사용자는 다음 두 가지 정보를 입력합니다.

- 현재 상태를 설명하는 자유 텍스트
- 사용 가능한 시간

사용 가능한 시간은 다음 범위에서 선택할 수 있습니다.

`5분 · 10분 · 20분 · 30분 · 1시간 · 2시간 · 반나절 · 상관없음`

예시:

```text
오늘 너무 지치고 머리가 복잡해.
쉬어야 하는데 뭘 해야 할지 모르겠어.
```

---

## 처리 흐름

```text
사용자 입력
↓
React
↓
FastAPI /analyze
↓
OpenAI API
↓
JSON 형식 응답
↓
Pydantic Validation
↓
React 결과 화면
```

Backend는 사용자의 상태와 사용 가능한 시간을 OpenAI API에 전달합니다.

LLM에는 정해진 JSON 형식으로만 응답하도록 요청하고,  
반환된 문자열을 JSON으로 변환한 뒤 Pydantic Schema로 검증합니다.

검증된 결과를 Frontend로 전달하면  
React가 각 필드를 이용해 분석 결과와 추천 활동을 화면에 표시합니다.

---

## 주요 기능

### 1. 자유 텍스트 상태 입력

사용자가 현재 느끼는 상태나 상황을 직접 문장으로 입력합니다.

### 2. 사용 가능한 시간 선택

현재 사용할 수 있는 시간을 선택해  
추천 활동의 길이에 반영합니다.

### 3. AI 기반 상태 정리

OpenAI API를 사용해 입력 문장에서 다음과 같은 정보를 구성합니다.

```text
감정 상태
에너지 수준
현재 상황
추천 방향
```

### 4. 실행 가능한 활동 추천

현재 상태와 시간을 바탕으로 다음 정보를 생성합니다.

```text
추천 활동
소요 시간
부담감
첫 행동
실행 단계
추천 이유
```

### 5. 구조화된 결과 처리

LLM의 자연어 답변을 그대로 출력하지 않고  
정해진 JSON 구조로 응답하도록 요청합니다.

Backend에서는 이를 Pydantic Model로 검증한 뒤  
Frontend가 정해진 필드에 맞춰 화면을 구성합니다.

### 6. 결과 UI

분석 결과와 추천 내용을 카드 형태로 표시합니다.

LLM이 반환한 `color_theme`, `flower_theme` 등의 값을 이용해  
결과에 따라 화면의 시각적 분위기도 변경합니다.

---

## 사용 기술

| 구분 | 사용 기술 |
|---|---|
| Frontend | React, JavaScript, CSS, Vite |
| Backend | Python, FastAPI |
| LLM | OpenAI API |
| Validation | Pydantic |
| Environment | python-dotenv |
| 소개 페이지 | HTML, CSS, zero-md |
| 버전 관리 | Git, GitHub |

---

## 현재 구현 흐름

Backend의 주요 API는 다음과 같습니다.

```text
GET /
POST /analyze
```

`POST /analyze`의 처리 흐름은 다음과 같습니다.

```text
사용자 입력
→ AnalyzeRequest
→ OpenAI API 호출
→ response.output_text
→ json.loads()
→ AnalyzeResponse Validation
→ Frontend Response
```

현재 OpenAI 호출에는 `gpt-4o-mini` 모델을 사용하고 있습니다.

---

## 출력 구조

현재 Backend에서는 다음과 같은 구조의 데이터를 Frontend에 전달합니다.

```text
emotion
energy
situation
goal
template
background
color_theme
flower_theme

activity
 ├─ title
 ├─ time
 ├─ burden
 ├─ first_action
 └─ steps

drink
space
clothes
mood_message
reason
```

Frontend는 이 데이터를 이용해  
현재 상태 요약, 추천 활동, 첫 행동, 실행 단계와 추천 이유를 표시합니다.

---

## 현재 상태

**Status: Re-validation in Progress**

현재 Repository에는 다음 구현 코드가 존재합니다.

```text
React UI
→ FastAPI
→ OpenAI API
→ JSON Parsing
→ Pydantic Validation
→ Result UI
```

과거 과제로 구현한 코드를 다시 실행하면서  
Frontend와 Backend 연결, 실제 API 응답, 오류 처리와 Validation을  
직접 설명하고 수정할 수 있도록 재검증하고 있습니다.

---

## 현재 한계

현재 버전에는 사용자 계정과 사용자 기록 저장 Database가 없습니다.

LLM의 JSON 형식 준수를 Prompt에 의존하고 있어  
형식이 잘못된 응답에 대한 처리를 더 강화할 필요가 있습니다.

Frontend의 Backend 주소도 현재 코드에 직접 지정되어 있어  
Local과 배포 환경을 분리할 수 있도록 설정 방식 개선이 필요합니다.

의료적 진단이나 치료를 목적으로 하는 서비스는 아닙니다.

---

## 개선 방향

```text
Local End-to-End 실행 검증
→ 환경별 Backend URL 분리
→ OpenAI API 오류 처리 개선
→ JSON / Validation 오류 처리 검증
→ Backend API 테스트 추가
→ README와 실제 실행 결과 동기화
```

---

## 프로젝트 요약

Structured Bloom은

```text
사용자 상태
+ 사용 가능한 시간
↓
LLM 분석
↓
구조화된 JSON
↓
Pydantic 검증
↓
실행 가능한 활동 추천
```

흐름으로 구성한 AI 웹 서비스입니다.

현재는 과거 구현을 다시 실행하고 수정·검증하면서  
프로젝트의 실제 구현 범위와 기술 흐름을 재정리하고 있습니다.
