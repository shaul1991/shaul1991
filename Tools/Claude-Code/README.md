# 🤖 Claude Code

## 📖 개요

Claude Code는 Anthropic의 AI 기반 코딩 어시스턴트로, 터미널에서 직접 사용할 수 있는 강력한 개발 도구입니다.

---

## 🚀 주요 기능

- ✅ **코드 생성 및 편집**: AI가 직접 파일을 읽고 수정
- ✅ **터미널 명령 실행**: Bash, Git, npm 등 자동 실행
- ✅ **MCP 통합**: 200+ 외부 도구 및 서비스 연동
- ✅ **커스텀 확장**: 슬래시 명령어, Hooks, 플러그인
- ✅ **멀티 에이전트**: 전문화된 서브 에이전트 활용

---

## 📚 학습 자료

### 🏠 로컬 LLM 통합

**홈 서버에서 로컬 LLM을 호스팅하고 Claude Code와 연동하는 완전 가이드**

👉 [**로컬 LLM 통합 가이드**](./로컬LLM통합.md)

**다루는 내용**:
- ✅ Ollama 설치 및 AMD GPU(RX 580) 설정
- ✅ DeepSeek-Coder, CodeLlama 등 코딩 특화 모델 추천
- ✅ Claude Code Router로 로컬/클라우드 하이브리드 사용
- ✅ MCP 서버로 홈 서버 도구 통합
- ✅ 성능 벤치마크 및 비용 최적화 전략

**홈 서버 사양**: AMD Ryzen 5 2400G + RX 580 8GB + 32GB RAM

---

## 🔧 기본 사용법

### 설치

```bash
# npm을 통한 설치
npm install -g @anthropic-ai/claude-code

# 실행
claude-code
```

### 주요 명령어

```bash
# 대화형 모드
claude-code

# 단일 프롬프트 실행
claude-code "Refactor this function to use async/await"

# 특정 파일 편집
claude-code --edit src/app.js "Add error handling"

# 슬래시 명령어
/help          # 도움말
/clear         # 대화 기록 초기화
/mcp           # MCP 서버 도구 사용
```

---

## 🎯 고급 활용

### 커스텀 슬래시 명령어

`.claude/commands/` 폴더에 Markdown 파일 생성:

```markdown
<!-- .claude/commands/review.md -->
# 코드 리뷰

다음 파일을 리뷰하고 개선 사항을 제안해주세요:
- 코드 품질
- 성능 최적화
- 보안 취약점
```

사용: `/review`

### Hooks (자동화)

`.claude/settings.json`에서 설정:

```json
{
  "hooks": {
    "Stop": [
      {
        "matcher": "",
        "hooks": [
          {
            "type": "command",
            "command": "git status"
          }
        ]
      }
    ]
  }
}
```

### MCP 서버 연동

`.claude/mcp.json`에서 설정:

```json
{
  "mcpServers": {
    "filesystem": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-filesystem", "/home/user/projects"]
    }
  }
}
```

---

## 💡 실전 팁

### 1. 효율적인 프롬프트 작성

✅ **좋은 예시**:
```
"Add user authentication with JWT to the Express API.
Use bcrypt for password hashing and validate email format."
```

❌ **나쁜 예시**:
```
"Add auth"
```

### 2. 파일 컨텍스트 제공

```
"Refactor the getUserById function in src/controllers/user.js
to use async/await instead of callbacks"
```

### 3. 단계별 작업

복잡한 작업은 단계별로 나누기:
```
1. "First, create a User model with Sequelize"
2. "Now add authentication middleware"
3. "Finally, protect the /api/users routes"
```

---

## 🔗 참고 자료

### 공식 문서

- [Claude Code 공식 문서](https://code.claude.com/docs)
- [Claude Agent SDK](https://platform.claude.com/docs/agent-sdk)
- [Model Context Protocol](https://modelcontextprotocol.io/)

### 커뮤니티

- [GitHub Discussions](https://github.com/anthropics/claude-code/discussions)
- [Discord 커뮤니티](https://discord.gg/anthropic)

---

## 📌 관련 학습 자료

- [🏠 로컬 LLM 통합](./로컬LLM통합.md) - 홈 서버에서 Ollama 호스팅
- [🛠️ Tools 홈으로](../) - 다른 개발 도구들

---

**마지막 업데이트**: 2025-12-28
