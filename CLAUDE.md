# 기술 스택 추가 가이드

이 문서는 학습한 기술 스택을 체계적으로 정리하는 방법을 안내합니다.

## 📂 폴더 구조

```
shaul1991/
├── Backend/
│   ├── README.md
│   ├── common/          # 공통 개념
│   ├── [언어명]/        # 예: Python, Java, Node 등
│   └── ...
├── DevOps/
│   ├── README.md
│   ├── [기술명]/        # 예: Docker, Kubernetes, Linux 등
│   └── ...
├── Database/
│   ├── README.md
│   ├── [DB명]/          # 예: MySQL-PostgreSQL, MongoDB 등
│   └── ...
├── Tools/
│   ├── README.md
│   ├── [도구명]/        # 예: VSCode, Claude-Code, Git 등
│   └── ...
└── Architecture/
    ├── README.md
    ├── [패턴명]/        # 예: Design-Patterns, Clean-Architecture 등
    └── ...
```

## ✅ 새로운 기술 스택 추가 방법

### 1. 폴더 생성

해당 영역에 새로운 폴더를 생성합니다.

**예시: Backend에 Python 추가**
```bash
mkdir -p Backend/Python
```

### 2. README.md 생성

각 기술 폴더에 `README.md` 파일을 생성합니다.

**예시: Backend/Python/README.md**
```markdown
# Python

## 학습 내용

- Django, FastAPI, Flask
- 비동기 프로그래밍
- 데이터 처리 및 분석

## 프로젝트

- [프로젝트명](./프로젝트명.md)

## 참고 자료

- [공식 문서](https://www.python.org/)
```

### 3. 상위 README 업데이트

해당 영역의 `README.md`에 링크를 추가합니다.

**예시: Backend/README.md**
```markdown
# Backend

백엔드 개발 관련 학습 내용을 정리합니다.

## 언어별 학습

- [Python](./Python) - Django, FastAPI, Flask
- [Java](./Java) - Spring Boot, JPA

## 공통 개념

- [동시성 처리](./common/동시성처리.md)
- [결합도/응집도](./common/결합도응집도.md)
```

### 4. 메인 README 업데이트

루트의 `README.md`에 링크를 추가합니다.

**예시: /README.md**
```markdown
### [Backend](./Backend)
- [Python](./Backend/Python) - Django, FastAPI, Flask
- [Java](./Backend/Java) - Spring Boot, JPA
- [공통 개념](./Backend#공통-개념) - 동시성 처리, 결합도/응집도 등
```

### 5. Git 커밋

```bash
git add -A
git commit -m "Add Python learning documentation"
git push
```

## 📝 상세 문서 작성 방법

### 개념 정리 문서

**파일 위치**: `[영역]/[기술]/[개념명].md`

**예시: Backend/Python/비동기프로그래밍.md**
```markdown
# 비동기 프로그래밍

## 개요

비동기 프로그래밍의 기본 개념과 Python에서의 구현 방법

## asyncio

### 기본 사용법

\`\`\`python
import asyncio

async def main():
    print('Hello')
    await asyncio.sleep(1)
    print('World')

asyncio.run(main())
\`\`\`

## 참고 자료

- [asyncio 공식 문서](https://docs.python.org/3/library/asyncio.html)
```

### 프로젝트 문서

**파일 위치**: `[영역]/[기술]/projects/[프로젝트명].md`

**예시: Backend/Python/projects/rest-api.md**
```markdown
# REST API 프로젝트

## 프로젝트 개요

FastAPI를 사용한 RESTful API 구현

## 기술 스택

- FastAPI
- PostgreSQL
- Docker

## 주요 기능

1. 사용자 인증
2. CRUD 작업
3. 페이지네이션

## 배운 점

- FastAPI의 자동 문서화 기능
- Pydantic 모델 활용
- 비동기 데이터베이스 처리
```

## 🔧 공통 개념 추가 방법

공통 개념은 `Backend/common/`, `DevOps/common/` 등에 추가합니다.

**예시: Backend/common/SOLID원칙.md**
```markdown
# SOLID 원칙

## 개요

객체지향 설계의 5가지 기본 원칙

## 원칙

### 1. SRP (Single Responsibility Principle)
단일 책임 원칙

### 2. OCP (Open-Closed Principle)
개방-폐쇄 원칙

...
```

## 📋 체크리스트

새로운 기술 스택을 추가할 때 확인하세요:

- [ ] 해당 영역에 폴더 생성
- [ ] 폴더 내 README.md 작성
- [ ] 상위 README.md에 링크 추가
- [ ] 메인 README.md에 링크 추가
- [ ] Git 커밋 및 푸시

## 💡 팁

### 파일명 규칙

- 한글 사용 가능: `동시성처리.md`
- 영문 사용: `concurrency.md`
- 띄어쓰기 대신 하이픈 사용: `design-patterns.md`

### 문서 구조화

1. **개념**: 이론 및 기본 개념
2. **사용법**: 실제 사용 예시
3. **프로젝트**: 실습 프로젝트
4. **참고 자료**: 추가 학습 자료

### 링크 작성

- 상대 경로 사용: `./Python/README.md`
- 앵커 링크: `#공통-개념`
- 외부 링크: `https://example.com`

## 📌 예시 구조

완성된 Backend 폴더 예시:

```
Backend/
├── README.md
├── common/
│   ├── 동시성처리.md
│   ├── 결합도응집도.md
│   └── SOLID원칙.md
├── Python/
│   ├── README.md
│   ├── 비동기프로그래밍.md
│   └── projects/
│       └── rest-api.md
├── Java/
│   ├── README.md
│   ├── JPA.md
│   └── projects/
│       └── spring-boot-app.md
└── Node/
    ├── README.md
    └── express-middleware.md
```
