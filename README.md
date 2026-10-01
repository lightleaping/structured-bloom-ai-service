# Structured Bloom

**React + FastAPI + OpenAI API 기반 AI 웹 서비스**

> **Status: Re-validation in Progress**  
> AI웹융합 과제로 구현한 기존 코드를 다시 실행하면서  
> 요청·응답 흐름, Validation, 오류 처리와 직접 구현 범위를 재검증하고 있습니다.

---

## Project Overview

Structured Bloom은 사용자가 현재 상태를 자유로운 문장으로 입력하고  
사용 가능한 시간을 선택하면, LLM이 입력 내용을 구조화하여  
현재 상태와 실행 가능한 작은 회복 활동을 제안하는 웹 서비스입니다.

단순한 자연어 응답을 화면에 그대로 출력하는 대신,  
LLM에 정해진 JSON 형식을 요청하고 Backend에서 Pydantic Schema로 검증한 뒤  
Frontend가 정해진 필드에 맞춰 결과를 렌더링하도록 구성했습니다.

---

## Current Flow

```text
User
↓
React
↓
POST /analyze
↓
FastAPI
↓
OpenAI API (gpt-4o-mini)
↓
JSON Text
↓
json.loads()
↓
Pydantic Validation
↓
API Response
↓
React Result UI
```

---

## Input

사용자는 두 가지 정보를 입력합니다.

1. **현재 상태**
   - 자유 텍스트 입력
   - Pydantic을 통한 입력값 검증

2. **사용 가능한 시간**
   - 5분
   - 10분
   - 20분
   - 30분
   - 1시간
   - 2시간
   - 반나절
   - 상관없음

Frontend Request 예시:

```json
{
  "text": "오늘 너무 지치고 머리가 복잡해. 쉬어야 하는데 뭘 해야 할지 모르겠어.",
  "available_time": "20min"
}
```

---

## Structured Output

LLM에는 다음 필드를 포함한 JSON 객체만 반환하도록 요청합니다.

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

Backend는 응답 문자열을 JSON으로 변환한 뒤  
`AnalyzeResponse` Pydantic Model로 검증합니다.

---

## Backend

### API

```text
GET  /
POST /analyze
```

### POST /analyze

처리 흐름:

```text
AnalyzeRequest
→ user_context 구성
→ OpenAI Responses API 호출
→ response.output_text
→ json.loads()
→ AnalyzeResponse
→ API Response
```

현재 OpenAI 호출 모델:

```text
gpt-4o-mini
```

### Request Validation

```python
class AnalyzeRequest(BaseModel):
    text: str = Field(..., min_length=1, max_length=800)
    available_time: Optional[str] = None
```

### Response Validation

Pydantic의 `Literal`을 사용해 일부 필드의 허용값을 제한합니다.

예:

```text
energy
→ low / medium / high

goal
→ quiet_rest
→ light_reset
→ focus_restart
→ outside_refresh
→ emotional_comfort

burden
→ 낮음 / 보통 / 높음
```

LLM이 `burden`을 `중간`으로 반환할 경우  
`field_validator`에서 `보통`으로 정규화합니다.

---

## Frontend

React에서는 주요 상태를 다음과 같이 관리합니다.

```text
text
availableTime
result
loading
```

처리 흐름:

```text
사용자 입력
→ analyzeMood()
→ fetch()
→ POST /analyze
→ JSON Response
→ setResult()
→ Result UI Rendering
```

구현된 UI 처리:

- 빈 입력 확인
- 요청 중 Loading 상태
- API 실패 시 오류 메시지
- 감정·에너지·상황 표시
- 추천 활동과 단계 표시
- 추천 이유와 첫 행동 표시
- 응답의 theme 값을 이용한 화면 스타일 변경

---

## Tech Stack

| Area | Technology |
|---|---|
| Frontend | React, JavaScript, CSS, Vite |
| Backend | Python, FastAPI |
| LLM | OpenAI API (`gpt-4o-mini`) |
| Validation | Pydantic |
| Environment | python-dotenv |
| Introduction Page | HTML, CSS, zero-md |
| Version Control | Git, GitHub |

---

## Project Structure

```text
structured-bloom-ai-service/
├── backend/
│   ├── main.py
│   └── requirements.txt
│
├── frontend/
│   ├── public/
│   ├── src/
│   │   ├── App.jsx
│   │   └── ...
│   ├── package.json
│   └── vite.config.js
│
├── app/
│   └── static frontend build
│
├── contents.md
├── index.html
├── style.css
├── screenshot.png
└── README.md
```

---

## How to Run

### Backend

```bash
cd backend
pip install -r requirements.txt
```

Backend는 환경변수에서 OpenAI API Key를 읽습니다.

```text
OPENAI_API_KEY=your_api_key
```

`.env` 파일은 GitHub에 포함하지 않습니다.

실행:

```bash
uvicorn main:app --reload
```

기본 주소:

```text
http://127.0.0.1:8000
```

### Frontend

```bash
cd frontend
npm install
npm run dev
```

기본 개발 주소:

```text
http://localhost:5173
```

---

## Deployment

과제 구현 당시 Frontend와 Backend를 별도로 구성했습니다.

- Introduction: `https://s23.aiweb2026.site`
- App: `https://s23.aiweb2026.site/app/`
- Backend: Hugging Face Space

현재 Repository의 Frontend 코드에는 Backend URL이 직접 지정되어 있으며,  
재검증 과정에서 환경별 설정으로 분리할 예정입니다.

---

## Current Status

### Confirmed in Repository Code

- React 사용자 입력 및 결과 UI
- FastAPI Backend
- `POST /analyze`
- OpenAI API 호출
- `gpt-4o-mini`
- Prompt 기반 JSON 출력 요청
- `json.loads()` Parsing
- Pydantic Request / Response Validation
- `field_validator`
- Loading 처리
- API 오류 처리
- Frontend → Backend 요청 코드

### Re-validation in Progress

- Local Backend 실행
- Local Frontend 실행
- Frontend ↔ Backend End-to-End 연결
- 실제 OpenAI API 응답 확인
- malformed JSON 처리
- Validation Error 처리
- 직접 구현 범위 재확인

---

## Current Limitations

- 사용자 계정 기능이 없습니다.
- 사용자 기록을 저장하는 Database가 없습니다.
- 테스트 코드가 현재 Repository에서 확인되지 않습니다.
- LLM의 JSON 형식 준수를 Prompt에 의존합니다.
- JSON 형식을 지키지 않으면 Parsing 오류가 발생할 수 있습니다.
- OpenAI API 및 Validation 관련 오류 처리가 세분화되어 있지 않습니다.
- Frontend의 Backend URL이 현재 코드에 직접 지정되어 있습니다.
- Backend CORS 설정은 현재 `http://localhost:5173`만 명시되어 있습니다.
- 의료적 진단이나 치료를 제공하는 서비스가 아닙니다.

---

## Next Steps

1. Local Backend / Frontend 재실행
2. End-to-End 요청 흐름 확인
3. Backend URL 환경변수 분리
4. OpenAI API 오류 처리 개선
5. malformed JSON / Validation Failure 처리 검증
6. Backend API 테스트 추가
7. 실제 실행 결과와 README 동기화

---

## My Role

AI웹융합 과제에서 다음 영역을 구성했습니다.

- 서비스 아이디어 및 사용자 입력 구조
- React 기반 사용자 화면
- FastAPI Backend 구조
- OpenAI API 연결
- LLM 출력 형식 설계
- Pydantic Response Schema
- Frontend와 Backend 연결

현재는 기존 구현을 다시 실행하면서  
각 코드의 역할을 설명하고 직접 수정·검증할 수 있는 상태로 복습하고 있습니다.

---

## Repository

https://github.com/lightleaping/structured-bloom-ai-service
