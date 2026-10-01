# Structured Bloom Frontend

Structured Bloom의 React 기반 Frontend입니다.

사용자가 현재 상태를 자유로운 문장으로 입력하고 사용 가능한 시간을 선택하면,
Backend의 `/analyze` API로 요청을 보내고 구조화된 분석·추천 결과를 화면에 표시합니다.

---

## Current Flow

```text
User Input
→ React State
→ analyzeMood()
→ POST /analyze
→ JSON Response
→ setResult()
→ Result UI
```

---

## Main Features

- 현재 상태 자유 텍스트 입력
- 사용 가능한 시간 선택
- Backend `/analyze` API 호출
- 요청 중 Loading 상태 처리
- API 오류 메시지 처리
- 감정·에너지·상황 결과 표시
- 추천 활동과 실행 단계 표시
- 첫 행동과 추천 이유 표시
- 응답의 `color_theme`, `flower_theme` 값을 이용한 화면 구성

---

## Main State

`App.jsx`에서는 다음 상태를 관리합니다.

```text
text
availableTime
result
loading
```

- `text`: 사용자가 입력한 현재 상태
- `availableTime`: 선택한 사용 가능 시간
- `result`: Backend가 반환한 분석 결과
- `loading`: API 요청 진행 여부

---

## API Request

Frontend는 JSON 형식으로 Backend에 요청합니다.

```json
{
  "text": "오늘 너무 지치고 머리가 복잡해.",
  "available_time": "20min"
}
```

현재 구현에서는 Backend 주소가 `App.jsx`에 직접 지정되어 있습니다.

재검증 과정에서 Local / Deployment 환경별 API 주소를 분리할 예정입니다.

---

## Tech Stack

- React
- JavaScript
- CSS
- Vite

---

## Run

```bash
npm install
npm run dev
```

기본 개발 주소:

```text
http://localhost:5173
```

---

## Current Status

**Re-validation in Progress**

현재 Repository에서 Frontend 구현 코드는 확인되며,
다음 항목을 다시 실행·검증할 예정입니다.

- Local Frontend 실행
- Backend와의 연결
- 정상 응답 Rendering
- API Error 처리
- 환경별 Backend URL 분리

전체 프로젝트 설명은 상위 `README.md`를 참고합니다.
