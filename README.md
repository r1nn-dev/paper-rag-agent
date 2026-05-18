# paper-rag-agent
 
> 실제 개발 기간: 2026.02.13 ~ 2026.04.08   
> 업로드 날짜: 2026.05.18   
> 최종 업데이트: 2026.05. 
 
논문 PDF를 업로드하면 텍스트/표/그림/캡션/참고문헌을 함께 분석하고,   
LangGraph 멀티 에이전트로 요약부터 Q&A까지 제공하는 멀티모달 RAG 시스템.
 
---
 
## 주요 기능
 
| 기능 | 설명 |
|------|------|
| 논문 요약 | 전체 내용을 구조화된 형태로 요약 |
| 핵심 기여점 정리 | 논문의 contribution 자동 추출 |
| 한계점 정리 | limitation 섹션 분석 |
| 재현성 체크 | 실험 재현 가능성 평가 |
| 비교 분석 | 유사 논문과의 방법론 비교 |
| RAG 기반 논문 Q&A | 논문 내용 기반 자연어 질의응답 |
| OCR / Vision 요약 | 그림·캡션 분석 및 시각적 내용 요약 |
| 유사 논문 추천 | arXiv MCP 기반 유사 논문 자동 검색 및 추천 + 키워드 재검색 |

 
## 시스템 구조
 
```
PDF 업로드
    │
    ▼
PDF 파싱 (PyMuPDF / pdfplumber)
├── 텍스트 / 캡션 추출
├── 표 구조화
└── 그림 영역 감지
    │
    ▼
Chunking → Embedding → Chroma 인덱싱
    │
    ▼
LangGraph 멀티 에이전트 오케스트레이션
├── 요약 에이전트
├── QA 에이전트 (RAG)
├── Vision 에이전트 (OCR + OpenAI Vision)
└── 추천 에이전트 (arXiv MCP)
    │
    ▼
FastAPI → 사용자 응답
```
 
 
## 기술 스택
 
| 영역 | 기술 |
|------|------|
| Backend | `FastAPI` |
| Orchestration | `LangGraph` |
| Frontend | `HTML + CSS + Vanilla JS` |
| PDF 파싱 (텍스트/캡션) | `PyMuPDF` |
| PDF 파싱 (표) | `pdfplumber` |
| VectorDB (RAG) | `Chroma` (`langchain-chroma`) |
| OCR | `pytesseract` (로컬 Tesseract 엔진 필요) |
| Vision | `OpenAI Vision` 모델 |
| MCP 연동 | `arXiv` |
 
 
## 프로젝트 구조
 
```text
paper-rag-agent/
  app/
    main.py         # FastAPI 엔트리포인트, API 라우트
    graph.py        # LangGraph 멀티 에이전트 오케스트레이션
    pdf_utils.py    # PDF 텍스트/표/캡션/참고문헌 추출
    rag.py          # Chroma 인덱싱 + RAG 질의응답
    vision_utils.py # 그림 OCR + Vision 요약
    arxiv_mcp.py    # arXiv 검색 유틸 (유사 논문 추천)
    llm_utils.py    # .env 로드 + LLM 클라이언트 생성
    models.py       # 데이터 모델 (dataclass)
  templates/
    index.html      # 웹 화면
  static/
    style.css
    app.js
  uploads/          # 업로드된 PDF 저장 (gitignore)
  vector_store/     # 문서별 Chroma 인덱스 (gitignore)
  requirements.txt
  .env.example
```
 
 
## 실행 방법
 
```bash
# 1. 가상환경 생성 및 활성화
python -m venv .venv
.\.venv\Scripts\Activate    # Windows
source venv/bin/activate   # macOS/Linux
 
# 2. 의존성 설치
pip install -r requirements.txt
 
# 3. 환경변수 설정
cp .env.example .env
# .env에 OPENAI_API_KEY 입력
 
# 4. 서버 실행
uvicorn app.main:app --reload
```
 
> OCR 기능 사용 시 로컬에 [Tesseract](https://github.com/tesseract-ocr/tesseract) 엔진 설치 필요
 

## 커밋 컨벤션
 
| 타입 | 설명 |
|------|------|
| `feat` | 새로운 기능 추가 |
| `fix` | 버그 수정 |
| `refactor` | 기능 변경 없는 코드 구조 개선 |
| `docs` | README 등 문서 수정 |
| `chore` | 환경설정, 패키지 등 기타 변경 |
