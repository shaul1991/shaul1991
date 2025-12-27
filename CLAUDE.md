# 📚 기술 스택 추가 가이드

이 문서는 학습한 기술 스택을 체계적으로 정리하는 방법을 안내합니다.

> 💡 **중요**: 이 가이드를 따라 일관성 있는 학습 기록을 만들어 보세요!

---

## 📂 폴더 구조

```
shaul1991/
├── 🎯 Backend/           # 백엔드 개발
│   ├── README.md
│   ├── common/          # 공통 개념
│   ├── [언어명]/        # 예: Python, Java, Node 등
│   └── ...
├── 🚀 DevOps/            # DevOps & 인프라
│   ├── README.md
│   ├── [기술명]/        # 예: Docker, Kubernetes, Linux 등
│   └── ...
├── 💾 Database/          # 데이터베이스
│   ├── README.md
│   ├── [DB명]/          # 예: MySQL-PostgreSQL, MongoDB 등
│   └── ...
├── 🛠️ Tools/             # 개발 도구
│   ├── README.md
│   ├── [도구명]/        # 예: VSCode, Claude-Code, Git 등
│   └── ...
└── 🏗️ Architecture/      # 아키텍처 & 설계
    ├── README.md
    ├── [패턴명]/        # 예: Design-Patterns, Clean-Architecture 등
    └── ...
```

---

## ✅ 새로운 기술 스택 추가 방법

### 1️⃣ 폴더 생성

해당 영역에 새로운 폴더를 생성합니다.

**예시: Backend에 Python 추가**
```bash
mkdir -p Backend/Python
```

### 2️⃣ README.md 생성

각 기술 폴더에 `README.md` 파일을 생성합니다.

**예시: Backend/Python/README.md**
```markdown
# 🐍 Python

## 📖 학습 내용

- Django, FastAPI, Flask
- 비동기 프로그래밍
- 데이터 처리 및 분석

## 🚀 프로젝트

- [프로젝트명](./프로젝트명.md)

## 📚 참고 자료

- [공식 문서](https://www.python.org/)
- [Real Python](https://realpython.com/)
```

### 3️⃣ 상위 README 업데이트

해당 영역의 `README.md`에 링크를 추가합니다.

**예시: Backend/README.md**
```markdown
# 🎯 Backend

백엔드 개발 관련 학습 내용을 정리합니다.

## 언어별 학습

- [🐍 Python](./Python) - Django, FastAPI, Flask
- [☕ Java](./Java) - Spring Boot, JPA
- [🟢 Node.js](./Node) - Express, NestJS

## 공통 개념

- [⚡ 동시성 처리](./common/동시성처리.md)
- [🔗 결합도/응집도](./common/결합도응집도.md)
```

### 4️⃣ 메인 README 업데이트

루트의 `README.md`에 링크를 추가합니다.

**예시: /README.md**
```markdown
### [🎯 Backend](./Backend)
- [🐍 Python](./Backend/Python) - Django, FastAPI, Flask
- [☕ Java](./Backend/Java) - Spring Boot, JPA
- [공통 개념](./Backend#공통-개념) - 동시성 처리, 결합도/응집도 등
```

### 5️⃣ Git 커밋

```bash
git add -A
git commit -m "Add Python learning documentation"
git push
```

---

## 📝 상세 문서 작성 방법

### 📌 개념 정리 문서

**파일 위치**: `[영역]/[기술]/[개념명].md`

**예시: Backend/Python/비동기프로그래밍.md**
```markdown
# ⚡ 비동기 프로그래밍

## 📖 개요

비동기 프로그래밍의 기본 개념과 Python에서의 구현 방법

## 🔧 asyncio

### 기본 사용법

\`\`\`python
import asyncio

async def main():
    print('Hello')
    await asyncio.sleep(1)
    print('World')

asyncio.run(main())
\`\`\`

### 주요 특징

- ✅ 단일 스레드에서 동시성 처리
- ✅ I/O 바운드 작업에 효율적
- ⚠️ CPU 바운드 작업에는 부적합

## 💡 실전 팁

- `async/await` 키워드 사용
- 블로킹 함수는 `asyncio.to_thread()`로 감싸기
- 에러 처리 반드시 포함

## 📚 참고 자료

- [asyncio 공식 문서](https://docs.python.org/3/library/asyncio.html)
- [Real Python - Async IO](https://realpython.com/async-io-python/)
```

### 🚀 프로젝트 문서

**파일 위치**: `[영역]/[기술]/projects/[프로젝트명].md`

**예시: Backend/Python/projects/rest-api.md**
```markdown
# 🌐 REST API 프로젝트

## 📋 프로젝트 개요

FastAPI를 사용한 RESTful API 구현

## 🛠️ 기술 스택

- FastAPI
- PostgreSQL
- Docker
- Redis (캐싱)

## ✨ 주요 기능

1. 🔐 사용자 인증 (JWT)
2. 📝 CRUD 작업
3. 📄 페이지네이션
4. 🔍 검색 및 필터링

## 💡 배운 점

- ✅ FastAPI의 자동 문서화 기능 활용
- ✅ Pydantic 모델을 통한 데이터 검증
- ✅ 비동기 데이터베이스 처리로 성능 개선
- ⚠️ N+1 쿼리 문제 해결 방법

## 🔗 관련 링크

- [GitHub Repository](#)
- [API 문서](#)
```

---

## 🔧 공통 개념 추가 방법

공통 개념은 `Backend/common/`, `DevOps/common/` 등에 추가합니다.

**예시: Backend/common/SOLID원칙.md**
```markdown
# 🏛️ SOLID 원칙

## 📖 개요

객체지향 설계의 5가지 기본 원칙

## 원칙

### 1️⃣ SRP (Single Responsibility Principle)
**단일 책임 원칙**
- 하나의 클래스는 하나의 책임만 가져야 함
- ✅ 변경 이유가 하나여야 함
- ❌ 여러 책임을 가진 God Object 지양

### 2️⃣ OCP (Open-Closed Principle)
**개방-폐쇄 원칙**
- 확장에는 열려있고, 수정에는 닫혀있어야 함
- ✅ 인터페이스/추상 클래스 활용
- ❌ 기존 코드 수정 최소화

### 3️⃣ LSP (Liskov Substitution Principle)
**리스코프 치환 원칙**
- 자식 클래스는 부모 클래스를 대체할 수 있어야 함

### 4️⃣ ISP (Interface Segregation Principle)
**인터페이스 분리 원칙**
- 클라이언트는 사용하지 않는 인터페이스에 의존하면 안 됨

### 5️⃣ DIP (Dependency Inversion Principle)
**의존성 역전 원칙**
- 추상화에 의존해야 하며, 구체화에 의존하면 안 됨

## 💡 실전 적용

- 코드 리뷰 시 SOLID 원칙 체크
- 리팩토링 시 원칙 준수 여부 확인
```

---

## 🔢 학습 순서 관리 방법

일부 주제는 학습 순서가 중요하고, 일부는 순서가 필요 없습니다. 상황에 맞게 처리하세요.

### 📁 파일명 규칙

#### ✅ 학습 순서가 **필요 없는** 경우 (기본)

파일명에 번호를 붙이지 않습니다.

```
자료구조/
├── README.md
├── 배열.md
├── 연결리스트.md
├── 스택.md
├── 큐.md
└── 해시테이블.md
```

**README.md에서 권장 학습 순서를 안내합니다.**

```markdown
# 자료구조

## 📚 학습 항목

| 주제 | 난이도 | 분류 | 설명 |
|------|--------|------|------|
| [시간복잡도](./시간복잡도.md) | ⭐ | 기초개념 | **선행 필수** |
| [공간복잡도](./공간복잡도.md) | ⭐ | 기초개념 | **선행 필수** |
| [배열](./배열.md) | ⭐ | 선형자료구조 | 추천: 시간복잡도 학습 후 |
| [연결리스트](./연결리스트.md) | ⭐⭐ | 선형자료구조 | 추천: 배열 학습 후 |
| [스택](./스택.md) | ⭐⭐ | 선형자료구조 | 순서 무관 |
| [큐](./큐.md) | ⭐⭐ | 선형자료구조 | 순서 무관 |
| [해시테이블](./해시테이블.md) | ⭐⭐⭐ | 고급 | 순서 무관 |

## 🎯 권장 학습 순서

1. **필수 선행 학습**
   - 시간복잡도 (먼저!)
   - 공간복잡도

2. **선형 자료구조** (권장 순서: 배열 → 연결리스트 → 스택/큐)
   - 배열
   - 연결리스트
   - 스택, 큐 (순서 무관)

3. **고급 자료구조** (순서 무관)
   - 해시테이블
   - 트리
   - 그래프
```

#### ✅ 학습 순서가 **엄격한** 경우 (예외)

튜토리얼이나 단계별 실습처럼 반드시 순서를 따라야 하는 경우에만 번호를 사용합니다.

```
Python-Tutorial/
├── README.md
├── 01-설치와환경설정.md
├── 02-기본문법.md
├── 03-함수.md
├── 04-클래스.md
└── 05-모듈과패키지.md
```

### 🏷️ Tags를 활용한 분류

각 문서 상단에 메타데이터를 추가하여 분류합니다.

**예시: 자료구조/배열.md**
```markdown
---
tags: [선형자료구조, 기본]
difficulty: 초급
prerequisite: [시간복잡도]
related: [연결리스트, 동적배열]
---

# 배열

## 📖 개요
...
```

**예시: 자료구조/해시테이블.md**
```markdown
---
tags: [고급자료구조, 해싱]
difficulty: 중급
prerequisite: [배열, 시간복잡도]
related: [딕셔너리, 맵]
---

# 해시테이블

## 📖 개요
...
```

### 📊 Tags 필드 설명

| 필드 | 설명 | 예시 |
|------|------|------|
| `tags` | 주제 분류 태그 | `[선형자료구조, 기본]` |
| `difficulty` | 난이도 | `초급`, `중급`, `고급` |
| `prerequisite` | 선행 학습 항목 | `[시간복잡도, 배열]` |
| `related` | 연관 주제 | `[스택, 큐]` |

### 💡 언제 어떤 방식을 사용할까?

| 상황 | 방식 | 예시 |
|------|------|------|
| 개념 학습 (순서 권장만) | 번호 없음 + README 안내 | 자료구조, 디자인패턴 |
| 독립적인 주제들 | 번호 없음 + Tags | API 문서, 도구 사용법 |
| 단계별 실습/튜토리얼 | 번호 있음 | 프레임워크 튜토리얼 |
| 공식 문서 번역 | 원본 구조 따름 | - |

---

## 📋 체크리스트

새로운 기술 스택을 추가할 때 확인하세요:

- [ ] 📁 해당 영역에 폴더 생성
- [ ] 📄 폴더 내 README.md 작성 (이모티콘 포함)
- [ ] 🔗 상위 README.md에 링크 추가
- [ ] 📝 메인 README.md에 링크 추가
- [ ] 💾 Git 커밋 및 푸시
- [ ] ✅ 문서 가독성 검토

---

## 💡 작성 팁

### 📛 파일명 규칙

| 타입 | 예시 | 권장 |
|------|------|------|
| 한글 | `동시성처리.md` | ✅ 가독성 좋음 |
| 영문 | `concurrency.md` | ✅ 호환성 좋음 |
| 띄어쓰기 | `design patterns.md` | ❌ 하이픈 사용 권장 |
| 하이픈 | `design-patterns.md` | ✅ 추천 |

### 🎨 이모티콘 활용 가이드

| 카테고리 | 추천 이모티콘 | 용도 |
|---------|-------------|------|
| 언어 | 🐍🟢☕🦀 | Python, Node, Java, Rust |
| 데이터베이스 | 💾🗄️📊 | DB, Storage, Analytics |
| 도구 | 🛠️⚙️🔧 | Tools, Settings, Config |
| 보안 | 🔐🔒🛡️ | Auth, Security, Protection |
| 성능 | ⚡🚀💨 | Performance, Speed, Optimization |
| 경고 | ⚠️❌⛔ | Warning, Error, Stop |
| 체크 | ✅✔️👍 | Success, Done, Good |
| 정보 | 💡📌📝 | Tip, Note, Document |

### 📖 문서 구조화

1. **📖 개념**: 이론 및 기본 개념 설명
2. **🔧 사용법**: 실제 사용 예시 및 코드
3. **🚀 프로젝트**: 실습 프로젝트 및 실전 적용
4. **💡 팁**: 주의사항 및 베스트 프랙티스
5. **📚 참고 자료**: 추가 학습 자료 및 링크

### 🔗 링크 작성 규칙

- ✅ 상대 경로 사용: `./Python/README.md`
- ✅ 앵커 링크 활용: `#공통-개념`
- ✅ 외부 링크 명시: `[공식 문서](https://example.com)`
- ⚠️ 깨진 링크 주기적 점검

---

## 📌 완성된 폴더 예시

```
Backend/
├── README.md
├── common/
│   ├── 🔗 결합도응집도.md
│   ├── ⚡ 동시성처리.md
│   └── 🏛️ SOLID원칙.md
├── Python/
│   ├── README.md
│   ├── 🐍 기본문법.md
│   ├── ⚡ 비동기프로그래밍.md
│   └── projects/
│       ├── 🌐 rest-api.md
│       └── 🤖 chatbot.md
├── Java/
│   ├── README.md
│   ├── ☕ Spring-Boot.md
│   ├── 💾 JPA.md
│   └── projects/
│       └── 🛒 e-commerce.md
└── Node/
    ├── README.md
    ├── 🟢 Express.md
    └── ⚡ 이벤트루프.md
```

---

## 🎯 문서 품질 기준

### ✅ 좋은 문서의 특징

- 📖 **명확한 제목**: 내용을 한눈에 파악 가능
- 🎯 **구체적인 예시**: 실제 코드와 사용 사례 포함
- 💡 **실용적인 팁**: 실전에서 바로 적용 가능한 내용
- 🔗 **풍부한 참고자료**: 추가 학습을 위한 링크 제공
- ⚠️ **주의사항 명시**: 함정과 주의점 미리 안내

### ❌ 피해야 할 사항

- 너무 추상적인 설명만 나열
- 예시 코드 없이 이론만 작성
- 참고자료 링크 누락
- 일관성 없는 포맷 사용
- 오래된 정보 방치

---

## 🤝 기여 가이드

### 📝 문서 개선 시

1. 오타나 오류 발견 시 즉시 수정
2. 새로운 내용 추가 시 예시 포함
3. 중요한 변경사항은 커밋 메시지에 명시
4. 정기적인 문서 리뷰 및 업데이트

### 💬 피드백

- 개선사항이나 제안사항은 이슈로 등록
- 더 나은 구조나 포맷 아이디어 환영

---

> 📌 **참고**: 이 가이드는 지속적으로 개선됩니다. 더 나은 방법을 발견하면 업데이트하세요!

**마지막 업데이트**: 2025-12-27
