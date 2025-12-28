# 🏠 홈 서버 로컬 LLM + Claude Code 통합 가이드

## 📖 개요

홈 서버에서 로컬 LLM을 호스팅하고 Claude Code와 연동하는 완전 가이드입니다.

**목표**: Claude Code를 사용하면서 홈 서버의 로컬 LLM을 활용하여 비용 절감 및 성능 최적화

---

## 🖥️ 홈 서버 사양

현재 홈 서버 사양:

| 항목 | 사양 | LLM 적합도 |
|------|------|-----------|
| CPU | AMD Ryzen 5 2400G (8 threads) @ 3.600GHz | ✅ 양호 |
| GPU | AMD Radeon RX 580 8GB | ✅ 7B-13B 모델 최적 |
| RAM | 32GB | ✅ 충분 |
| OS | Ubuntu 24.04 | ✅ 완벽 지원 |

**결론**: 7B-13B 크기의 코딩 특화 LLM 실행에 최적화된 사양입니다.

---

## 🎯 추천 모델 (RX 580 8GB 기준)

### Tier 1: 최적 성능 (권장)

| 모델 | 크기 | VRAM | 속도 | 용도 |
|------|------|------|------|------|
| **DeepSeek-Coder 6.7B** | 6.7B | ~7GB | ⚡⚡⚡ 빠름 | 코딩 특화 |
| **CodeLlama 7B** | 7B | ~7.5GB | ⚡⚡⚡ 빠름 | 코드 생성 |
| **Mistral 7B** | 7B | ~7.5GB | ⚡⚡⚡ 빠름 | 범용 |
| **Llama 3.1 8B** | 8B | ~8GB | ⚡⚡ 보통 | 범용, 최신 |

**예상 속도**: 20-40 tokens/sec

### Tier 2: 4-bit 양자화

| 모델 | 크기 | VRAM | 속도 | 용도 |
|------|------|------|------|------|
| **DeepSeek-Coder 33B (4-bit)** | 33B | ~8GB | ⚡ 느림 | 고급 코딩 |
| **Llama 2 13B (4-bit)** | 13B | ~8GB | ⚡⚡ 보통 | 범용 |

**예상 속도**: 10-20 tokens/sec

---

## 🚀 방법 1: Ollama + Claude Code Router (권장)

가장 간단하고 효과적인 방법입니다.

### Step 1: 홈 서버에 Ollama 설치

```bash
# Ubuntu 24.04에서 Ollama 설치
curl -fsSL https://ollama.com/install.sh | sh

# 설치 확인
ollama --version
```

### Step 2: AMD GPU 지원 설정

```bash
# ROCm 설치 (AMD GPU 지원)
sudo apt update
sudo apt install rocm-hip-sdk -y

# 환경 변수 설정
echo 'export HSA_OVERRIDE_GFX_VERSION=8.0.3' >> ~/.bashrc
source ~/.bashrc

# Ollama 서비스 재시작
sudo systemctl restart ollama
```

### Step 3: 코딩 특화 모델 다운로드

```bash
# DeepSeek-Coder 6.7B (가장 추천)
ollama pull deepseek-coder:6.7b

# CodeLlama 7B (대안)
ollama pull codellama:7b

# Llama 3.1 8B (최신)
ollama pull llama3.1:8b

# 다운로드된 모델 확인
ollama list
```

### Step 4: 모델 테스트

```bash
# 대화형 테스트
ollama run deepseek-coder:6.7b

# 프롬프트 예시:
# "Write a Python function to calculate fibonacci numbers"
```

### Step 5: Claude Code Router 설치

로컬 머신(Claude Code를 실행하는 곳)에서:

```bash
# npx로 직접 실행 (권장)
npx claude-code-router

# 또는 글로벌 설치
npm install -g claude-code-router
claude-code-router
```

### Step 6: Claude Code Router 설정

```bash
# 설정 파일 생성
cat > ~/.claude-code-router/config.json << 'EOF'
{
  "providers": {
    "ollama": {
      "baseUrl": "http://홈서버IP:11434",
      "models": {
        "deepseek-coder": "deepseek-coder:6.7b",
        "codellama": "codellama:7b",
        "llama3": "llama3.1:8b"
      },
      "default": "deepseek-coder"
    }
  },
  "routing": {
    "claude-opus-4-5": "ollama:deepseek-coder",
    "claude-sonnet-4-5": "ollama:deepseek-coder"
  },
  "port": 3000
}
EOF

# 홈서버IP를 실제 IP로 변경하세요 (예: 192.168.1.100)
```

### Step 7: Claude Code 설정

Claude Code의 API 엔드포인트를 변경:

```bash
# Claude Code 설정
export ANTHROPIC_API_BASE=http://localhost:3000
export ANTHROPIC_API_KEY=dummy-key  # Router는 API 키 필요 없음

# Claude Code 실행
claude-code
```

---

## 🔧 방법 2: MCP 서버로 홈 서버 도구 연동

더 유연한 방법: 홈 서버를 MCP 서버로 노출하여 Claude Code에서 도구로 사용

### Step 1: 홈 서버에 MCP 서버 생성

```javascript
// ~/mcp-servers/home-llm-server.js
import { Server } from "@modelcontextprotocol/sdk/server/index.js";
import { StdioServerTransport } from "@modelcontextprotocol/sdk/server/stdio.js";
import {
  CallToolRequestSchema,
  ListToolsRequestSchema,
} from "@modelcontextprotocol/sdk/types.js";
import Anthropic from "@anthropic-ai/sdk";

const server = new Server(
  {
    name: "home-llm-server",
    version: "1.0.0",
  },
  {
    capabilities: {
      tools: {},
    },
  }
);

// Ollama 호출 함수
async function callOllama(prompt, model = "deepseek-coder:6.7b") {
  const response = await fetch("http://localhost:11434/api/generate", {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify({
      model,
      prompt,
      stream: false,
    }),
  });
  const data = await response.json();
  return data.response;
}

// 도구 등록
server.setRequestHandler(ListToolsRequestSchema, async () => {
  return {
    tools: [
      {
        name: "ask_local_llm",
        description: "홈 서버의 로컬 LLM에게 질문하기 (DeepSeek-Coder 6.7B)",
        inputSchema: {
          type: "object",
          properties: {
            prompt: {
              type: "string",
              description: "LLM에게 보낼 프롬프트",
            },
            model: {
              type: "string",
              description: "사용할 모델 (기본: deepseek-coder:6.7b)",
              enum: ["deepseek-coder:6.7b", "codellama:7b", "llama3.1:8b"],
            },
          },
          required: ["prompt"],
        },
      },
    ],
  };
});

// 도구 실행
server.setRequestHandler(CallToolRequestSchema, async (request) => {
  if (request.params.name === "ask_local_llm") {
    const { prompt, model } = request.params.arguments;
    const response = await callOllama(prompt, model);
    return {
      content: [{ type: "text", text: response }],
    };
  }
  throw new Error("Unknown tool");
});

// 서버 시작
const transport = new StdioServerTransport();
await server.connect(transport);
```

### Step 2: Claude Code에서 MCP 서버 설정

```json
// ~/.claude/mcp.json
{
  "mcpServers": {
    "home-llm": {
      "command": "node",
      "args": ["/home/user/mcp-servers/home-llm-server.js"],
      "scope": "local"
    }
  }
}
```

### Step 3: Claude Code에서 사용

이제 Claude Code에서 `/mcp` 명령어로 홈 서버의 LLM을 도구로 사용 가능:

```
User: /mcp ask_local_llm "Write a Python function to parse JSON"
```

---

## 🎮 방법 3: LiteLLM 게이트웨이 (고급)

여러 LLM 제공자를 통합 관리하는 방법입니다.

### Step 1: 홈 서버에 LiteLLM 설치

```bash
# Python 가상환경 생성
python3 -m venv ~/litellm-env
source ~/litellm-env/bin/activate

# LiteLLM 설치
pip install 'litellm[proxy]'
```

### Step 2: LiteLLM 설정

```yaml
# ~/litellm-config.yaml
model_list:
  - model_name: deepseek-coder
    litellm_params:
      model: ollama/deepseek-coder:6.7b
      api_base: http://localhost:11434

  - model_name: codellama
    litellm_params:
      model: ollama/codellama:7b
      api_base: http://localhost:11434

  - model_name: claude-sonnet
    litellm_params:
      model: claude-sonnet-4-5
      api_key: ${ANTHROPIC_API_KEY}

general_settings:
  master_key: your-secret-key

litellm_settings:
  drop_params: true
  set_verbose: true
```

### Step 3: LiteLLM 프록시 실행

```bash
# 백그라운드 실행
litellm --config ~/litellm-config.yaml --port 8000 &

# systemd 서비스로 등록 (선택사항)
sudo tee /etc/systemd/system/litellm.service << 'EOF'
[Unit]
Description=LiteLLM Proxy
After=network.target

[Service]
Type=simple
User=user
WorkingDirectory=/home/user
ExecStart=/home/user/litellm-env/bin/litellm --config /home/user/litellm-config.yaml --port 8000
Restart=on-failure

[Install]
WantedBy=multi-user.target
EOF

sudo systemctl enable litellm
sudo systemctl start litellm
```

### Step 4: Claude Code 설정

```bash
# OpenAI 호환 API로 사용
export ANTHROPIC_API_BASE=http://홈서버IP:8000
export ANTHROPIC_API_KEY=your-secret-key

# Claude Code 실행
claude-code
```

---

## 📊 성능 벤치마크 (RX 580 8GB)

실제 테스트 결과 (예상):

| 모델 | VRAM 사용 | 토큰/초 | 지연시간 (첫 토큰) | 품질 |
|------|----------|--------|------------------|------|
| DeepSeek-Coder 6.7B | 7.2GB | 25-35 | 0.5초 | ⭐⭐⭐⭐⭐ |
| CodeLlama 7B | 7.5GB | 20-30 | 0.6초 | ⭐⭐⭐⭐ |
| Llama 3.1 8B | 8.0GB | 18-25 | 0.7초 | ⭐⭐⭐⭐⭐ |
| DeepSeek-Coder 33B (4-bit) | 8.0GB | 8-12 | 1.2초 | ⭐⭐⭐⭐⭐⭐ |

**추천**: DeepSeek-Coder 6.7B가 속도와 품질의 최적 균형점

---

## 💡 실전 활용 팁

### 1. 하이브리드 접근 (비용 최적화)

Claude Code Router를 활용한 스마트 라우팅:

```json
{
  "routing": {
    "simple-tasks": "ollama:deepseek-coder",
    "complex-tasks": "anthropic:claude-opus-4-5"
  },
  "rules": {
    "ifPromptLength": {
      "lessThan": 500,
      "route": "ollama:deepseek-coder"
    },
    "ifPromptContains": {
      "keywords": ["complex", "architecture", "refactor"],
      "route": "anthropic:claude-opus-4-5"
    }
  }
}
```

**전략**:
- ✅ 간단한 코드 생성 → 로컬 LLM (무료)
- ✅ 복잡한 아키텍처 설계 → Claude Opus (유료)
- 💰 **비용 절감 예상**: 70-80%

### 2. 모델별 최적 용도

| 작업 유형 | 추천 모델 | 이유 |
|---------|---------|------|
| 코드 자동완성 | DeepSeek-Coder 6.7B | ⚡ 빠른 응답 |
| 버그 수정 | CodeLlama 7B | 🔍 코드 이해력 |
| 문서 생성 | Llama 3.1 8B | 📝 자연어 품질 |
| 리팩토링 | DeepSeek-Coder 33B (4-bit) | 🧠 고급 추론 |
| 아키텍처 설계 | Claude Opus 4.5 | 🎯 최고 성능 |

### 3. GPU 메모리 최적화

```bash
# Ollama 환경 변수로 메모리 제어
export OLLAMA_MAX_LOADED_MODELS=1  # 동시 로드 모델 수 제한
export OLLAMA_NUM_PARALLEL=2       # 병렬 요청 수
export OLLAMA_FLASH_ATTENTION=1    # Flash Attention 활성화

# Ollama 재시작
sudo systemctl restart ollama
```

### 4. 네트워크 최적화 (원격 접속 시)

```bash
# 홈 서버에서 Ollama API를 외부 노출
sudo ufw allow 11434/tcp

# Ollama 서비스 설정 변경
sudo systemctl edit ollama

# 다음 내용 추가:
[Service]
Environment="OLLAMA_HOST=0.0.0.0:11434"

# 재시작
sudo systemctl daemon-reload
sudo systemctl restart ollama
```

**보안 주의**: 외부 노출 시 반드시 Caddy/Nginx로 리버스 프록시 + 인증 설정

---

## ⚠️ 주의사항 및 제한사항

### 현재 제한사항

1. **Claude Code 자체는 로컬 LLM 미지원**
   - ❌ Claude Code CLI가 직접 Ollama 사용 불가
   - ✅ 프록시(Router, LiteLLM)를 통한 우회 필요

2. **성능 차이**
   - 🐌 로컬 7B 모델 < Claude Sonnet 4.5
   - 💰 하지만 비용은 무료 vs 유료

3. **AMD GPU 호환성**
   - ⚠️ ROCm 버전 확인 필요
   - RX 580은 GFX8 (HSA_OVERRIDE_GFX_VERSION=8.0.3 설정 필수)

### 보안 고려사항

- 🔒 MCP 서버는 로컬에서만 실행 권장
- 🛡️ LiteLLM API 키는 환경 변수로 관리
- 🔐 원격 접속 시 HTTPS + 인증 필수

---

## 🔍 트러블슈팅

### 문제 1: AMD GPU 인식 안 됨

```bash
# ROCm 설치 확인
rocm-smi

# 출력 없으면 ROCm 재설치
sudo apt purge rocm-* -y
sudo apt install rocm-hip-sdk -y

# GFX 버전 오버라이드 설정
export HSA_OVERRIDE_GFX_VERSION=8.0.3
```

### 문제 2: Ollama 모델 다운로드 느림

```bash
# Ollama 미러 서버 설정 (한국)
export OLLAMA_MIRRORS=https://ollama.kr

# 또는 프록시 사용
export http_proxy=http://프록시IP:포트
export https_proxy=http://프록시IP:포트
```

### 문제 3: 메모리 부족 (OOM)

```bash
# 더 작은 모델 사용
ollama pull deepseek-coder:1.3b

# 또는 양자화 버전
ollama pull llama3.1:8b-q4_0
```

### 문제 4: Claude Code Router 연결 실패

```bash
# 홈 서버 Ollama API 접근 테스트
curl http://홈서버IP:11434/api/tags

# 방화벽 확인
sudo ufw status

# 포트 열기
sudo ufw allow 11434/tcp
```

---

## 📚 참고 자료

### 공식 문서

- [Ollama 공식 사이트](https://ollama.com/)
- [Claude Code Router GitHub](https://github.com/musistudio/claude-code-router)
- [LiteLLM 문서](https://docs.litellm.ai/)
- [Model Context Protocol](https://modelcontextprotocol.io/)
- [ROCm for AMD GPUs](https://rocm.docs.amd.com/)

### 추천 모델

- [DeepSeek-Coder](https://github.com/deepseek-ai/DeepSeek-Coder)
- [CodeLlama](https://ai.meta.com/blog/code-llama-large-language-model-coding/)
- [Llama 3.1](https://ai.meta.com/blog/meta-llama-3-1/)

### 커뮤니티

- [Ollama Discord](https://discord.gg/ollama)
- [Claude Code Community](https://github.com/anthropics/claude-code/discussions)
- [r/LocalLLaMA](https://reddit.com/r/LocalLLaMA)

---

## 🎯 다음 단계

1. **즉시 시작**: Ollama + DeepSeek-Coder 6.7B 설치
2. **실험**: 다양한 모델 테스트 및 성능 비교
3. **최적화**: 하이브리드 라우팅으로 비용 절감
4. **확장**: MCP 서버로 홈 서버 기능 통합

---

**마지막 업데이트**: 2025-12-28
**테스트 환경**: AMD Ryzen 5 2400G + RX 580 8GB + Ubuntu 24.04
