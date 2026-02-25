# 🧠 CampusMind: RAG 기반 지능형 학습 보조 시스템

**설계: 이충환**

> **🔗 배포 링크: [https://ragtutor-9chlee9.streamlit.app/](https://ragtutor-9chlee9.streamlit.app/)**
---

## 📖 프로젝트 소개

**CampusMind**는 RAG(Retrieval-Augmented Generation) 아키텍처를 기반으로 한 지능형 학습 보조 시스템입니다.  
사용자가 업로드한 강의 자료(PDF, PPT, Word, 이미지, 음성, 영상)를 자동으로 분석하여 다양한 학습 기능을 제공합니다.

### 아키텍처
```
문서 업로드 → 청크 분할 → 임베딩(text-embedding-3-small) → FAISS 벡터 DB 저장
질의 입력   → 벡터 검색  → 관련 청크 검색(MMR)            → GPT-4o 답변 생성
```

---

## ✨ 주요 기능

| 기능 | 설명 |
|------|------|
| 📝 **핵심 요약 및 용어 정리** | 강의 자료를 자동 요약하고 핵심 용어를 정리 |
| 🎨 **구조 시각화** | 마인드맵 / 스파이더 다이어그램으로 내용 구조화 (Graphviz) |
| 🃏 **플래시 카드** | Q&A 형식의 암기 카드 자동 생성 |
| 🧩 **퀴즈** | 4지선다 객관식 퀴즈 자동 생성 |
| 🎧 **오디오 브리핑** | 핵심 요약을 TTS로 음성 변환 (OpenAI TTS-1) |
| 💬 **AI 조교** | RAG 기반 대화형 질의응답 (대화 맥락 유지) |

---

## 📁 지원 파일 형식

| 분류 | 형식 |
|------|------|
| 문서 | PDF, Word (.docx, .doc) |
| 프레젠테이션 | PowerPoint (.pptx, .ppt) |
| 이미지 | JPG, PNG, JPEG (GPT-4o Vision) |
| 오디오 | MP3, WAV, M4A (Whisper 전사) |
| 영상 | MP4, AVI, MOV (MoviePy → Whisper 전사) |

---

## 🚀 로컬 실행 가이드

### 0. 소스 코드 다운로드

```bash
git clone https://github.com/chlee19990109-cloud/RAG_tutor.git
cd RAG_tutor
```

### 1. 가상환경 생성

```bash
# 기본 코드
conda create -n [환경이름] python=[버전]

# 이 프로젝트를 위한 설치
conda create -n rag_tutor python=3.10
```

### 2. 가상환경 활성화 및 확인

```bash
# 활성화
conda activate rag_tutor

# 파이썬 버전 확인
python --version
# Python 3.10.x 가 출력되어야 합니다
# 만약 3.10 버전이 아니면 가상환경 비활성화 후 1번으로 돌아가 새 가상환경을 생성하세요
```

### 3. 필수 추가 명령어 (참고)

```bash
# 생성된 환경 목록 확인
conda env list

# 가상환경 비활성화
conda deactivate

# 가상환경 삭제
conda remove -n rag_tutor --all
```

### 4. 가상환경 활성화 후 인터프리터 설정 (VS Code)

```
Ctrl + Shift + P → "Python: Select Interpreter" 검색 → 앞에서 생성한 가상환경 선택
```

### 5. 필요한 라이브러리 설치

```bash
pip install -r requirements.txt
```

> **⚠️ 참고**: `graphviz` 시각화 기능을 사용하려면 [Graphviz 공식 사이트](https://graphviz.org/download/)에서 시스템 패키지도 별도 설치해야 합니다.
### 6. OpenAI API 키 준비

앱 실행 후 사이드바에서 OpenAI API 키를 직접 입력합니다.  
API 키는 [OpenAI Platform](https://platform.openai.com/api-keys)에서 발급받을 수 있습니다.

### 7. 앱 실행

```bash
streamlit run tutor_final.py
```

브라우저에서 `http://localhost:8501` 이 자동으로 열립니다.

---

## 📦 기술 스택

| 분류 | 라이브러리 |
|------|-----------|
| **웹 프레임워크** | Streamlit ≥ 1.35.0 |
| **LLM** | LangChain 0.3.0, langchain-openai 0.2.0 |
| **AI 모델** | GPT-4o, text-embedding-3-small, Whisper-1, TTS-1 |
| **벡터 DB** | FAISS (faiss-cpu) |
| **문서 처리** | PyPDF, python-pptx, docx2txt |
| **시각화** | Graphviz |
| **영상 처리** | MoviePy (선택적) |
| **토큰 계산** | tiktoken |

---

## 💸 API 비용 추적

앱 내 사이드바에서 OpenAI API 사용 비용을 실시간으로 추적할 수 있습니다.

| 모델 | 과금 기준 | 단가 |
|------|----------|------|
| GPT-4o | 토큰 수 | 입력 $2.50 / 출력 $10.00 (1M 토큰당) |
| TTS-1 | 글자 수 | $15.00 / 1M 글자 |
| Whisper-1 | 재생 시간 | $0.006 / 분 |
| text-embedding-3-small | 토큰 수 | $0.02 / 1M 토큰 |

> ※ 요금은 변경될 수 있으므로 [OpenAI 공식 요금표](https://platform.openai.com/pricing) 참고
---

## 🌐 언어 지원

한국어 / English (사이드바에서 전환 가능)
