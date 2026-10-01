# Structured Bloom
### Structured AI Web Service

**React, FastAPI, OpenAI API, and Pydantic Validation**

<p>
  <img src="https://img.shields.io/badge/Status-Re--validation%20in%20Progress-EAA12B?style=flat-square" alt="Re-validation in Progress">
  <img src="https://img.shields.io/badge/FastAPI-Backend-21AFC4?style=flat-square&logo=fastapi&logoColor=white" alt="FastAPI">
  <img src="https://img.shields.io/badge/React-Frontend-3561D8?style=flat-square&logo=react&logoColor=white" alt="React">
  <img src="https://img.shields.io/badge/OpenAI-API-151F32?style=flat-square" alt="OpenAI API">
</p>

> 사용자의 현재 상태와 사용 가능한 시간을 입력받아
> OpenAI API가 구조화된 분석·활동 추천을 생성하고,
> **FastAPI와 Pydantic으로 응답 형식을 검증한 뒤 React UI에 연결하는**
> AI 웹 서비스 프로젝트입니다.
>
> **Status: Re-validation in Progress**  
> 기존 구현을 다시 실행하면서 요청·응답 흐름과 직접 수정·설명 가능한 범위를 재검증하고 있습니다.

---

## Why This Project

피곤하거나 머리가 복잡한 상황에서는
현재 상태를 스스로 정리하기도 어렵고,
막연히 “쉬어야겠다”라고 생각해도 지금 무엇을 하면 좋을지 결정하기 어려울 수 있습니다.

Structured Bloom은 사용자가 현재 상태를 자유롭게 입력하고
사용 가능한 시간을 선택하면,

```text
현재 상태 입력
→ 감정·에너지·상황 정리
→ 회복 방향 결정
→ 시간에 맞는 작은 활동 추천
→ 바로 시작할 첫 행동 제시
```

까지 하나의 흐름으로 연결하는 것을 목표로 했습니다.

단순히 긴 조언을 생성하는 대신,
현재 상황에서 실행할 수 있는 작은 행동을 구체적으로 제안하고
감정·상황·활동·추천 이유를 일정한 구조로 보여주도록 구성했습니다.

이를 서비스 화면에 안정적으로 연결하기 위해
LLM에는 정해진 JSON 구조의 응답을 요청하고,
Backend에서 Pydantic으로 응답 형식을 검증한 뒤
React UI가 각 결과를 정해진 영역에 표시하도록 구현했습니다.

즉, 이 프로젝트의 핵심은
**사용자의 모호한 현재 상태를 구조화하고,
지금 실행할 수 있는 작은 선택으로 연결하는 AI 서비스 흐름을 구현하는 것**입니다.

---

## Project Overview

| 항목 | 내용 |
|---|---|
| **형태** | AI웹융합 과제 |
| **목표** | 사용자 상태를 구조화하고 실행 가능한 작은 활동을 추천하는 AI 웹 서비스 구현 |
| **Frontend** | React, JavaScript, CSS, Vite |
| **Backend** | Python, FastAPI |
| **LLM** | OpenAI API (`gpt-4o-mini`) |
| **Validation** | Pydantic |
| **현재 상태** | 기존 구현 코드 재검증 진행 중 |
| **현재 공개 범위** | React UI, FastAPI API, OpenAI 호출, JSON Parsing, Pydantic Schema |
| **미확인 범위** | 현재 환경에서의 End-to-End 재실행 및 테스트 결과 |

---

## Problem → Implementation → Result

| Problem | Implementation | Current Result |
|---|---|---|
| LLM 응답 형식이 매번 달라질 수 있음 | Prompt에서 JSON 출력 형식 지정 | Backend에서 JSON 문자열을 구조화 데이터로 변환 |
| 응답 필드가 예상 형식과 다를 수 있음 | `AnalyzeResponse` Pydantic Schema 적용 | 허용된 필드와 값 형식 검증 |
| 일부 표현 차이로 Validation이 실패할 수 있음 | `field_validator`로 `중간 → 보통` 정규화 | `burden` 값의 일부 표현 차이 보정 |
| 사용자 입력이 비어 있거나 너무 길 수 있음 | Frontend 빈 입력 확인 + Backend `Field` Validation | 입력 경계 조건 구성 |
| API 호출 중 사용자 경험이 끊길 수 있음 | Loading 상태와 오류 Alert 처리 | 요청 상태를 UI에서 구분 |
| LLM 결과를 화면에서 일관되게 보여줘야 함 | 정해진 응답 필드를 React Component에 연결 | 감정·활동·추천 이유 등을 정해진 UI에 Rendering |

---

## System Overview

현재 Repository 코드의 흐름은 다음과 같습니다.

```text
User Input
↓
React
↓
POST /analyze
↓
FastAPI
↓
OpenAI Responses API
↓
response.output_text
↓
json.loads()
↓
Pydantic AnalyzeResponse
↓
JSON API Response
↓
React Result UI
```

현재 Frontend는 사용자 상태와 사용 가능한 시간을 전달합니다.

```json
{
  "text": "오늘 너무 지치고 머리가 복잡해.",
  "available_time": "20min"
}
```

Backend는 OpenAI 응답을 JSON으로 변환한 뒤
`AnalyzeResponse`를 통해 구조를 검증합니다.

---

## My Role

AI웹융합 과제에서 다음 영역을 구성했습니다.

- 서비스 아이디어와 사용자 입력 구조
- React 기반 사용자 화면
- FastAPI Backend 구조
- OpenAI API 연결
- LLM JSON 출력 형식 설계
- Pydantic Request / Response Schema
- Frontend와 Backend 연결
- 결과 화면 Rendering 구조

현재는 기존 구현을 다시 실행하면서
각 코드의 역할을 직접 설명하고 수정·검증할 수 있는 상태로 재확인하고 있습니다.

---

## Technical Details

<details open>
<summary><b>01 | Request Validation</b></summary>

<br>

현재 Request Schema:

```python
class AnalyzeRequest(BaseModel):
    text: str = Field(..., min_length=1, max_length=800)
    available_time: Optional[str] = None
```

사용자의 현재 상태는 1자 이상 800자 이하로 제한합니다.

Frontend에서도 빈 문자열을 먼저 확인하지만,
Backend에서도 별도의 Validation을 수행합니다.

</details>

<details>
<summary><b>02 | OpenAI API Flow</b></summary>

<br>

Backend의 `POST /analyze`는 다음 흐름으로 동작합니다.

```text
AnalyzeRequest
→ user_context 생성
→ OpenAI Responses API
→ response.output_text
→ json.loads()
→ AnalyzeResponse
→ API Response
```

현재 호출 모델:

```text
gpt-4o-mini
```

Prompt에서는 Markdown이나 설명 문장이 아니라
하나의 JSON 객체만 반환하도록 요청합니다.

현재 구현은 OpenAI의 Structured Output Schema 기능을 직접 사용하는 구조가 아니라,
**Prompt로 JSON 형식을 요청한 뒤 Pydantic으로 후검증하는 방식**입니다.

</details>

<details>
<summary><b>03 | Structured Response</b></summary>

<br>

현재 주요 Response 필드:

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

일부 필드는 `Literal`을 이용해 허용값을 제한합니다.

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

`burden`이 `"중간"`으로 반환될 경우
`field_validator`에서 `"보통"`으로 정규화합니다.

</details>

<details>
<summary><b>04 | Frontend Flow</b></summary>

<br>

React에서는 다음 상태를 관리합니다.

```text
text
availableTime
result
loading
```

요청 흐름:

```text
사용자 입력
→ analyzeMood()
→ POST /analyze
→ Response 확인
→ JSON Parsing
→ setResult()
→ Result UI Rendering
```

현재 구현된 UI 처리:

- 빈 입력 확인
- 사용 가능 시간 선택
- 요청 중 Loading 상태
- API 오류 Alert
- 감정·에너지·상황 표시
- 추천 활동과 단계 표시
- 첫 행동과 추천 이유 표시
- `color_theme`, `flower_theme` 기반 화면 Class 변경

</details>

<details>
<summary><b>05 | Error Handling</b></summary>

<br>

Backend에서는 현재 두 범주의 오류를 처리합니다.

```text
JSONDecodeError
→ AI 응답 JSON 변환 실패

기타 Exception
→ 분석 중 오류
```

현재 오류 처리는 기본 수준이며,
OpenAI API 오류, Pydantic Validation 오류 등을 별도 유형으로 세분화하지는 않았습니다.

Frontend에서는 HTTP Response가 성공 상태가 아니면
Backend의 `detail` 값을 이용해 오류 메시지를 표시합니다.

</details>

---

## Run and Verify

현재 Repository 기준 실행 방법은 다음과 같습니다.

### Backend

```bash
cd backend
pip install -r requirements.txt
uvicorn main:app --reload
```

환경변수:

```text
OPENAI_API_KEY=your_api_key
```

`.env`는 GitHub에 포함하지 않습니다.

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

현재 이 명령과 전체 End-to-End 흐름은
**Re-validation in Progress** 상태입니다.

직접 재실행이 완료되기 전까지
현재 환경에서 정상 동작을 재검증했다고 표시하지 않습니다.

---

## Project Structure

```text
structured-bloom-ai-service/
├── backend/
│   ├── main.py
│   └── requirements.txt
├── frontend/
│   ├── public/
│   ├── src/
│   │   ├── App.jsx
│   │   └── ...
│   ├── package.json
│   └── vite.config.js
├── app/
├── contents.md
├── index.html
├── style.css
├── screenshot.png
├── .gitignore
└── README.md
```

---

## Current Scope and Limitations

### Current Scope

Repository 코드에서 현재 확인되는 범위:

- React 사용자 입력 및 결과 UI
- FastAPI Backend
- `GET /`
- `POST /analyze`
- OpenAI Responses API 호출
- `gpt-4o-mini`
- Prompt 기반 JSON 출력 요청
- `json.loads()` Parsing
- Pydantic Request / Response Validation
- `field_validator`
- Loading 처리
- Frontend API 오류 처리

### Limitations

- 현재 Repository에는 자동 테스트 코드가 확인되지 않습니다.
- 현재 환경에서 Backend / Frontend End-to-End 재실행은 아직 완료되지 않았습니다.
- 실제 OpenAI API 응답에 대한 재검증이 진행 중입니다.
- JSON 형식 준수를 Prompt에 의존합니다.
- malformed JSON은 Parsing 오류로 처리됩니다.
- OpenAI API와 Validation 오류가 세분화되어 있지 않습니다.
- Frontend의 Backend URL이 코드에 직접 지정되어 있습니다.
- `API_BASE_URL` 변수가 선언되어 있지만 현재 `fetch()`에서는 직접 사용되지 않습니다.
- Backend CORS 허용 Origin은 현재 `http://localhost:5173`으로 제한되어 있습니다.
- 사용자 계정 기능이 없습니다.
- 사용자 기록을 저장하는 Database가 없습니다.
- 의료적 진단이나 치료를 제공하는 서비스가 아닙니다.

### Next Steps

```text
Backend 직접 재실행
→ POST /analyze 요청 확인
→ OpenAI 실제 응답 확인
→ Frontend 직접 재실행
→ Frontend ↔ Backend End-to-End 확인
→ Backend URL 환경변수 분리
→ 오류 처리 세분화
→ API 테스트 추가
→ README 실행 결과 갱신
```

---

## What This Project Demonstrates

현재 Repository 코드 기준으로 다음 구현 경험을 확인할 수 있습니다.

- React와 FastAPI를 연결한 AI 웹 서비스 구성 경험
- OpenAI API를 Backend에서 호출한 경험
- 자연어 입력을 구조화된 JSON 응답으로 변환하도록 Prompt를 설계한 경험
- Pydantic을 이용한 Request / Response Validation 경험
- `Literal`과 `field_validator`를 이용한 응답 범위 제한 경험
- LLM 응답과 Frontend Rendering 사이에 검증 계층을 둔 경험
- Loading 및 API 오류 상태를 UI에서 처리한 경험
- 구현된 코드와 아직 재검증되지 않은 범위를 구분하여 관리한 경험

---

## Contact

- Developer: 김수진
- GitHub: https://github.com/lightleaping
- Email: workingskyroad@gmail.com
