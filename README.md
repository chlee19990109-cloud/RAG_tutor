# 🧠 CampusMind: RAG 기반 지능형 학습 보조 시스템

**설계: 이충환**

> **🔗 배포 링크: [https://ragtutor-9chlee9.streamlit.app/](https://ragtutor-9chlee9.streamlit.app/)**
---

## 📖 프로젝트 소개

**CampusMind**는 RAG(Retrieval-Augmented Generation) 아키텍처를 기반으로 한 지능형 학습 보조 시스템입니다.  
사용자가 업로드한 강의 자료(PDF, PPT, Word, 이미지, 음성, 영상)를 자동으로 분석하여 다양한 학습 기능을 제공합니다.

### 아키텍처

#### 솔루션 아키텍처
![솔루션 아키텍처](솔루션%20아키텍처.PNG)

**기반 논문 아키텍처 원리**  
본 프로젝트는 RAG(Retrieval-Augmented Generation) 논문에서 제시한 핵심 구조를 바탕으로 구현되었습니다. 외부 문서 모음에서 관련 지식을 검색하는 정보 검색 모듈(Retriever)과 이를 바탕으로 답변을 도출하는 언어 모델(Generator)을 결합하여, 환각(Hallucination)을 억제하고 사용자 제공 문서 기반의 정확도 높은 답변을 생성하는 원리를 따릅니다.

**데이터 흐름**
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
| 이미지 | JPG, PNG, JPEG (GPT-4o Vision API) |
| 오디오 | MP3, WAV, M4A (Whisper 전사) |
| 영상 | MP4, AVI, MOV (MoviePy → Whisper 전사) |

---

## ☁️ 배포 및 시스템 환경 (Environment Setup)

본 프로젝트는 **Streamlit Cloud** 환경 배포에 최적화되어 있으며, 로컬 구동 시 동일한 결과를 보장하기 위해 아래의 구성 요건을 준수해야 합니다.

*   **운영 체제 (OS)**: Windows, macOS, Linux 지원
*   **Python 버전**: `3.10` (필수 권장. Streamlit 및 의존성 라이브러리 간 호환성 확보)
*   **핵심 의존성 라이브러리 (Dependencies)**:
    *   `streamlit`: 웹 프레임워크 플랫폼 및 UI 렌더링
    *   `langchain` / `langchain-openai`: LLM 체인 구축 및 검색 시스템 구조화
    *   `openai`: Whisper, TTS, Vision 등 기능 확장을 위한 공식 SDK 직접 호출
    *   `faiss-cpu`: 벡터 데이터베이스 구축용 로컬 라이브러리
    *   `moviepy`, `python-pptx`, `PyPDF2` 등 데이터 파싱 전처리 도구
*   **필수 시스템 패키지**:
    *   **Graphviz**: 원활한 마인드맵 및 스파이더 다이어그램(시각화) 구조 생성 시, 시스템 레벨 바이너리 설치가 추가로 필요합니다.
    *   *(선택)* FFmpeg: `moviepy`를 이용한 영상(음성) 추출 처리 시 간간이 요구될 수 있습니다.

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

# 배포 환경과 동일한 파이썬 버전인 3.10으로 프로젝트 환경 설치를 권장합니다.
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

## 📦 기술 스택 및 구현 상세 (Technical Details)

`tutor_final.py` 본문을 중심으로 구성된 핵심 기술 구현 논리와 파이프라인 특징은 다음과 같습니다.

### 1. 프론트엔드 최적화 및 상태 관리 (Streamlit)
*   **세션 상태 (Session State)**: `st.session_state`를 활용하여 페이지 전환이나 새로고침이 발생하더라도 생성된 변수 데이터, 비용 누적 내역, AI 채팅 이력을 지속적으로 보존합니다.
*   **멀티 링구얼 아키텍처 지원**: `UI` 변수에 한국어와 영어 문자열을 매핑한 딕셔너리 구조를 취하여 번역 및 LLM의 출력 언어 제어를 사이드바에서 유동적으로 지시합니다.
*   **캐싱을 통한 API 호출 축소 (`@st.cache_resource`)**:
    *   동일한 구조의 OpenAI 클라이언트 모델 인스턴스 반복 생성을 억제합니다.
    *   사용자가 업로드한 파일 묶음을 MD5 알고리즘(`hashlib.md5(f.getvalue()).hexdigest()`)으로 해싱 검증하여, 파일 변동이 없는 재실행 시 FAISS 데이터베이스 구축 단계를 생략하고 기존 데이터를 캐시 반환합니다.

### 2. 멀티모달 파일 처리 및 모듈식 추출 파이프라인
*   확장자에 따른 독립된 텍스트 추출 모듈을 가동하여 다양한 리소스 형태를 포괄합니다. 
    *   **PDF / Word (.pdf, .docx)**: `PyPDFLoader` 및 `Docx2txtLoader` 등 LangChain Loader 연계.
    *   **PowerPoint (.pptx)**: `python-pptx` 라이브러리로 슬라이드 Shape 객체 내 텍스트만 추출.
    *   **오디오 (.mp3, .wav)**: OpenAI Whisper API를 통해 텍스트로 고정 전사.
    *   **영상 (.mp4, .mov)**: `moviepy`로 영상에서 오디오 트랙(.mp3) 임시 추출 후 Whisper 전사 도입 (메모리 누수 방지 로직 적용).
    *   **이미지 (.jpg, .png)**: LangChain 모듈 대신, 안정성과 정밀함을 위해 이미지 파일을 Base64 문자열로 인코딩한 후 OpenAI GPT-4o Vision SDK로 직접 전송.

### 3. LLM 추론 엔진 결합 (LangChain & OpenAI API)
*   **연속 대화형 검색 (ConversationalRetrievalChain)**: 과거 맥락을 유지하여 사용자 질의에 답변할 수 있도록 챗 히스토리를 전달합니다. 이를 통해 단순히 문서를 읽는 기능에서 'AI 도우미' 형태로 발전합니다.
*   **제한적이고 견고한 출력 포맷 유도**: 플래시 카드 및 퀴즈 등 정형화된 응답을 요구할 때, 시스템 프롬프트(System Message) 수준에서 `response_format={"type": "json_object"}` 방식을 강제함으로서, Python의 json 모듈 파싱(`json.loads`) 시 발생하는 포맷 누락 오류 가능성을 원천 차단했습니다.

### 4. 청크 분할 및 벡터 유사성 검색 (FAISS)
*   **텍스트 스플리팅**: 긴 길이의 통합본을 단락→문장→단어 순으로 경계를 자르는 `RecursiveCharacterTextSplitter`로 분해합니다 (`chunk_size=1500`, `chunk_overlap=200`). 문맥의 훼손을 최소화하여 인접한 의미의 분할 지점을 채택합니다.
*   **다양성 기반 검색 (MMR 방침)**: 검색 쿼리에 대해 단순히 유사도(Cosine Similarity)가 높은 청크만 연결할 경우 중복된 문맥만 참조하는 문제를 막아내기 위해 MMR(Maximal Marginal Relevance) 검색 방식을 채택(`k=20`, `fetch_k=50`)했습니다.
*   페이스북 기초의 로컬 경량화 DB 모델인 **FAISS (CPU Ver.)** 내부에서 `text-embedding-3-small` 임베딩에 의존합니다.

### 5. 구조 시각화 처리 (Graphviz 연계)
*   사용자 주제 모드(전원 범위 포괄 또는 개별 논제 집중) 지시에 따라 그래프 렌더링 방식이 동적으로 제어됩니다.
*   **마인드맵**의 경우 `dot` 엔진 알고리즘을 사용하여 계층적인 트리(Network) 구조를 확보하고, **스파이더 다이어그램**은 `neato` 알고리즘 기반으로 상호 결합 및 복합적 방사형 구조를 전유하도록 DOT 언어 파이프라인에서 구성됩니다.

---

## 💸 API 비용 추적 시스템

앱 내 사이드바에서 OpenAI API 사용 비용 정보를 실시간으로 계산하여 화면에 송출합니다 (세션 기반).

| 모델 | 과금 기준 | 단가 | 활용 |
|------|----------|------|------|
| **GPT-4o** | 토큰 수 | 입력 $2.50 / 출력 $10.00 (1M 토큰당) | 요약, 퀴즈, 플래시카드, 대화 생성 |
| **TTS-1** | 글자 수 | $15.00 / 1M 글자 | 추출된 오디오 브리핑 생성 |
| **Whisper-1** | 재생 시간 | $0.006 / 분 | 동영상(MoviePy), 오디오 전사 |
| **text-embedding-3-small** | 토큰 수 | $0.02 / 1M 토큰 | FAISS 벡터 저장 임베딩 생성 |

> ※ 모델 요금은 변경될 수 있으므로 [OpenAI 공식 요금표](https://platform.openai.com/pricing)를 참고하시기 바랍니다.

---

## 🌐 언어 지원

한국어 / English (사이드바에서 글로벌 전환 가능)
