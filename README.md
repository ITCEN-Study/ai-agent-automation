# 🤖 AI Agent & 업무 자동화 스터디

자유로운 세미나 및 스프린트 형태로 운영되는 스터디 저장소입니다. 
과제라는 부담감을 내려놓고, 각자 학습한 내용과 코드를 누적하여 지속 가능한 성장 기록을 만드는 것을 목표로 합니다.

💡 자세한 스터디 수칙과 지향점은 [STUDYRULE.md](./studyrule.md)을 참고해 주세요.

---

## 📁 디렉토리 구조 (주제 중심)

우리 스터디는 일정한 주차에 얽매이지 않고, **학습하는 기술 및 기능 주제(`topic-`)** 단위로 폴더를 생성하여 이론(`docs`)과 실습 코드(`works`)를 분리해 관리합니다. 아래는 디렉토리 구조 예시 입니다.

```text
📁 ai-agent-automation (Repository Root)
├── 📁 docs/                         # [Docs] 이론 학습, 리서치, 프롬프트, 도구 리뷰
│   ├── 📁 topic-multi-agent/        # 멀티 에이전트 프레임워크 리서치 (CrewAI, AutoGen 등)
│   └── 📁 topic-dev-tools/          # Cursor, v0 등 개발자 AI 도구 활용 팁
│
├── 📁 works/                        # [Works] 실제 구현 소스 코드 및 환경 설정
│   ├── 📁 topic-multi-agent/        # 멀티 에이전트 실제 구현 코드
│   └── 📁 topic-workflow-auto/      # 업무 자동화 스크립트 실습 코드
│
├── .gitignore                       # API Key 및 가상환경 업로드 차단 설정
├── README.md                        # 본 파일 (메인 안내)
└── studyrule.md                     # 스터디 상세 규칙 및 가이드라인