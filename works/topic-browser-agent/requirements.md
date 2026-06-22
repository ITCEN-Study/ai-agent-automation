# 🛠️ [1주차 Works] `browser-use` 오픈소스 라이브러리 실전 연동 가이드

이 명세서는 `browser-use` 라이브러리를 사용해 자율 웹 브라우징 에이전트를 성공적으로 구동하기 위한 **가상환경 셋업, 필수 의존성, 파이썬 코드 구조 설계, 비동기(Async) 실행 흐름**에 관한 상세 가이드입니다. 

---

## 🎯 과제 목표
* Python 가상환경을 구축하고 브라우저 제어 바이너리를 완벽히 설정합니다.
* LangChain 인터페이스와 `browser-use` SDK를 결합한 파이썬 스크립트(`main.py`)를 개발합니다.
* 에이전트가 브라우저 창을 띄워(눈에 보이게) 구글 검색 및 정보 수집 태스크를 완수하고 결과를 텍스트 파일로 저장하도록 구현합니다.

---

## 📋 1단계: 개발 환경 구성 & 패키지 설치 가이드

파이썬 환경은 전역 패키지 간 충돌을 방지하기 위해 가상환경 격리가 필수적입니다. 아래 단계를 터미널에서 순서대로 진행해 주세요.

### 1) 가상환경 생성 및 활성화
`works/topic-browser-agent/` 디렉토리 안에서 실행합니다.
```powershell
# 1. 가상환경 (.venv) 생성
python -m venv .venv

# 2. Windows PowerShell 기준 활성화 (스크립트 실행)
.\.venv\Scripts\Activate.ps1
```
> 💡 **Windows 스크립트 실행 오류 발생 시 (Execution Policy)**
> 파워쉘에서 활성화 스크립트 실행이 차단될 경우, 터미널에 `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process`를 입력해 권한을 일시 부여한 후 다시 활성화 스크립트를 돌리세요.

### 2) 필수 패키지 설치
가상환경이 켜진 상태(`(.venv)` 표시 확인)에서 필요한 의존성 라이브러리를 설치합니다.
```bash
# browser-use 및 OpenAI LangChain 연동용 라이브러리 설치
pip install browser-use langchain-openai python-dotenv

# Playwright용 브라우저 바이너리(Chromium 등) 설치
playwright install
```

---

## 📋 2단계: 프로젝트 설정 파일 구성

### 1) `.env` 파일 작성
`works/topic-browser-agent/` 경로에 `.env` 파일을 생성하고 OpenAI API Key를 적어줍니다.
```env
OPENAI_API_KEY=your_openai_api_key_here
```

### 2) `.gitignore` 설정 (보안)
API Key가 깃허브에 절대 업로드되지 않도록, 해당 디렉토리에 `.gitignore` 파일을 만들거나 루트 `.gitignore`를 점검하여 다음 폴더/파일을 예외 처리하세요.
```text
.venv/
.env
__pycache__/
*.txt
```

---

## 📋 3단계: `main.py` 아키텍처 및 의사 코드(Pseudo Code) 설계

`browser-use`는 파이썬의 **비동기(asyncio)** 기반으로 작동하는 라이브러리입니다. 스크립트 작성 시 아래의 코드 흐름 및 구조(의사 코드)를 참조하여 직접 완성해 보세요.

### 1) 핵심 임포트(Import) 명세
코드 구현 시 다음 패키지들을 결합하여 사용해야 합니다:
* `asyncio`: 파이썬 비동기 이벤트 루프 구동용
* `dotenv`: `.env` 로드용
* `langchain_openai`: `ChatOpenAI` 모델 객체 생성용
* `browser_use`: `Agent`, `Browser`, `BrowserConfig` 클래스 로드용

### 2) 비동기 코드 구조 가이드 (의사 코드)
```python
# 1. 환경 변수 로드 (.env 설정값 반영)

# 2. 비동기 메인 함수 정의 (async def main():)
    # 2-1. 브라우저 설정 객체 생성 (BrowserConfig)
    #      (AI가 조종하는 모습을 모니터링하기 위해 headless=False 옵션 적용)
    
    # 2-2. 브라우저 인스턴스 생성 (Browser)
    
    # 2-3. 두뇌 역할을 할 LLM 모델 인스턴스 선언 (ChatOpenAI)
    #      (model='gpt-4o-mini' 추천)
    
    # 2-4. 에이전트 인스턴스 선언 (Agent)
    #      - task: "구글에서 'AI 에이전트' 검색 후 상위 3개 블로그 글의 제목과 링크를 수집해서 로컬 result.txt에 기록해줘."
    #      - llm: 생성한 ChatOpenAI 인스턴스
    #      - browser: 생성한 Browser 인스턴스
    
    # 2-5. 에이전트 자율 루프 실행 및 대기 (await agent.run())
    
    # 2-6. 최종 획득 결과 콘솔 출력 및 파일 저장 검증

# 3. 비동기 이벤트 루프 실행 (asyncio.run(main()))
```

---

## 🔍 4단계: 동작 검증 및 트러블슈팅

1. **에이전트 실행**:
   ```bash
   python main.py
   ```
2. **동작 관찰**: 브라우저 창이 자동으로 열리고, 구글 검색창에 타이핑을 하거나 스크롤이 내려가는 등 AI가 자율적으로 마우스를 제어하는 비주얼을 확인하세요.
3. **결과물 검증**: 실행이 끝난 후 `works/topic-browser-agent/` 디렉토리에 AI가 긁어온 텍스트가 담긴 `result.txt` 파일이 정상적으로 남았는지 확인합니다.
4. **리뷰 요청**: 작동이 성공했다면 작성한 `main.py` 파일의 소스코드를 띄우고 저에게 코드 검수를 요청해 주세요!
