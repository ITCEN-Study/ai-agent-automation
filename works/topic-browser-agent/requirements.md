# 🛠️ [1주차 Works] 초미니 자율 웹 브라우징 에이전트 구현

이 실습은 `Playwright`와 `OpenAI API`를 사용하여, 사용자가 한글 자연어로 명령을 내리면 AI가 스스로 브라우저를 조종해 구글 검색을 수행하고 최종적으로 타겟 텍스트를 파일로 저장해 주는 **초미니 자율 웹 브라우저 에이전트**를 직접 구현하는 과제입니다.

---

## 🎯 과제 목표
* `Playwright`를 기동하여 백엔드에서 헤드리스(혹은 화면이 보이는) 브라우저 인스턴스를 제어합니다.
* AI에게 **[브라우저 화면 캡처본 / HTML 노드 정보]**를 주고, AI가 다음으로 취해야 할 행동(Action)을 파악하여 JSON 명령어로 반환하게 합니다.
* AI의 명령에 맞춰 마우스 클릭, 텍스트 입력 등의 동작을 수행하고 목표 완수 시까지 이 과정을 **Agentic Loop**로 반복합니다.

---

## 📋 세부 구현 요구사항

### 1. 개발 환경 구성
* `works/topic-browser-agent/` 디렉토리에 Node.js 프로젝트를 초기화합니다.
* 필수 패키지인 `playwright` (브라우저 자동화), `openai` (AI 분석), `dotenv` (환경 변수)를 추가하세요.

### 2. 브라우저 에이전트 코어 루프 설계 (`src/agent.js`)
* 다음 가이드라인을 토대로 자율 작동하는 에이전트 루프 함수를 설계하세요:
  1. **초기화**: Playwright로 Chromium 브라우저를 띄우고 빈 페이지를 생성합니다. (테스트를 위해 `headless: false` 옵션을 권장합니다.)
  2. **루프 진입**: 최대 시도 횟수(예: 10회)를 두고 다음 단계를 반복 실행합니다.
     - **관찰(Observe)**: 현재 페이지의 핵심 HTML 코드나 스크린샷 데이터를 로드합니다.
     - **LLM 추론(Analyze)**: LLM에게 현재 페이지 상태와 사용자의 최종 목표(예: "Next.js 15 릴리즈 기사 내용 저장")를 알려주고, **다음에 취해야 할 행동**을 정의된 JSON 규격으로 반환받습니다.
     - **행동(Action)**: 반환받은 JSON 명령에 따라 Playwright API를 실행합니다.
       * 예시 행동: `{"action": "goto", "url": "https://google.com"}`
       * 예시 행동: `{"action": "fill", "selector": "input[name='q']", "text": "Next.js 15 release"}`
       * 예시 행동: `{"action": "click", "selector": "input[type='submit']"}`
       * 예시 행동: `{"action": "finish", "resultText": "추출한 본문 텍스트..."}`
  3. **완료 및 결과 저장**: `finish` 행동이 내려지면 긁어온 텍스트를 `result.txt` 파일로 저장하고 루프를 탈출한 뒤 브라우저를 종료합니다.

### 3. LLM 스키마 설계 및 프롬프트 통제
* LLM이 무조건 지정한 JSON 포맷으로만 응답하도록 OpenAI의 **Structured Outputs** 기능을 적용하거나 스키마 가이드를 확실히 프롬프트에 제공하세요.
* **출력 스키마 예시**:
  ```json
  {
    "thought": "다음 단계로 구글 검색창에 Next.js 15 release를 입력해야 합니다.",
    "action": "goto | fill | click | finish",
    "selector": "CSS 선택자 문자열 (goto/finish 시에는 생략 가능)",
    "text": "입력할 문자열 (fill 시 필수)",
    "url": "이동할 URL (goto 시 필수)",
    "resultText": "최종 결과 데이터 (finish 시 필수)"
  }
  ```

### 4. 콘솔 상태 로깅
* AI가 한 단계씩 행동을 결정하고 마우스를 움직일 때마다 콘솔창에 진행 상황과 추론 생각(`thought`)을 자세히 로깅해 화면에 표시하세요.

---

## 🚶‍♂️ 실습 진행 단계 가이드

1. `works/topic-browser-agent/` 디렉토리로 이동하여 `npm init -y`를 치고 프로젝트를 초기화합니다.
2. `npm install playwright openai dotenv`를 실행하여 환경을 셋업합니다. (Playwright가 처음이라면 `npx playwright install` 명령을 통해 브라우저 바이너리를 추가로 설치해야 합니다.)
3. `src/agent.js`와 실행 엔트리포인트를 직접 설계해 작성해 나가며 에이전트 루프의 예외 처리를 디버깅합니다.
4. "구글에서 AI 에이전트를 검색해 상위 블로그 링크를 들어가 본문을 메모장으로 긁어오라"는 테스트 명령을 던져보고, AI가 실제로 검색 ➡️ 링크 클릭 ➡️ 텍스트 추출 ➡️ 저장을 자율적으로 수행하는지 검증합니다.
5. 테스트가 완료되면 AI(Antigravity)에게 코드를 공유해 리뷰를 요청하세요.
