# 딸깍AI 오픈클로 설치 서비스 운영 매뉴얼

내부 문서. 설치 서비스 표준 프로세스 및 최적 설정 가이드.

참고 자료:
- [잔 다르크 8단계 가이드](https://x.com/i/communities/2017879415318007887)
- [OpenClaw 공식 문서](https://docs.openclaw.ai/gateway/configuration)
- [비용 최적화 가이드 (LaoZhang)](https://blog.laozhang.ai/en/posts/openclaw-save-money-practical-guide)
- [보안 강화 가이드 (Nebius)](https://nebius.com/blog/posts/openclaw-security)
- [SlowMist 보안 실천 가이드](https://github.com/slowmist/openclaw-security-practice-guide)
- [DigitalOcean 배포 가이드](https://www.digitalocean.com/community/tutorials/how-to-run-openclaw)

---

## 1. 설치 전 고객 상담 체크리스트

### 필수 확인 사항
- [ ] OS 확인: macOS (Apple Silicon M1+) / Linux (Ubuntu 22.04+) / Windows (WSL2만)
- [ ] 최소 사양: 2 vCPU / 4 GB RAM
- [ ] 인터넷 연결 상태
- [ ] 별도 기기 여부 (보안 권장)
- [ ] 사용 목적 파악: 어떤 업무를 자동화하고 싶은지

### 고객 유형별 권장 방식
| 고객 유형 | 권장 설치 방식 | 예상 시간 |
|-----------|--------------|----------|
| Mac Mini 보유 | 로컬 설치 (bare metal) | 20분 |
| 리눅스 서버 보유 | Docker 설치 | 30분 |
| 장비 없음, 저예산 | 클라우드 VPS (Hetzner CX23 ~$5/월) | 40분 |
| 기업/보안 중시 | Docker + Tailscale | 45분 |

---

## 2. 표준 설치 프로세스

### 2-1. macOS 로컬 설치 (가장 흔한 케이스)

```bash
# 1. Homebrew 설치 (없는 경우)
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# 2. Node.js 22+ 설치
brew install node@22

# 3. OpenClaw 설치
npm i -g openclaw

# 4. 초기 설정 마법사
openclaw onboard

# 5. 건강 검사 및 자동 수정
openclaw doctor --fix

# 6. 보안 감사
openclaw security audit --deep
```

### 2-2. Docker 설치

```bash
# 1. Docker 설치 (없는 경우)
brew install --cask docker  # macOS
# 또는
curl -fsSL https://get.docker.com | sh  # Linux

# 2. OpenClaw Docker 실행
docker run -d \
  --name openclaw \
  --restart unless-stopped \
  -v ~/.openclaw:/root/.openclaw \
  -p 127.0.0.1:18789:18789 \
  openclaw/openclaw:latest

# 3. 설정 및 검증
docker exec -it openclaw openclaw onboard
docker exec -it openclaw openclaw doctor --fix
```

### 2-3. VPS 설치 (Hetzner CX23)

```bash
# 1. Tailscale 설치 (보안 접속)
curl -fsSL https://tailscale.com/install.sh | sh
sudo tailscale up --ssh=true

# 2. Node.js + OpenClaw 설치
curl -fsSL https://deb.nodesource.com/setup_22.x | sudo -E bash -
sudo apt install -y nodejs
npm i -g openclaw

# 3. 게이트웨이 loopback 바인딩 (필수)
# openclaw.json에서 gateway.bind: "loopback" 확인

# 4. systemd 서비스 등록
sudo tee /etc/systemd/system/openclaw.service << 'EOF'
[Unit]
Description=OpenClaw AI Agent
After=network.target

[Service]
Type=simple
User=openclaw
ExecStart=/usr/bin/openclaw gateway start
Restart=always
RestartSec=10

[Install]
WantedBy=multi-user.target
EOF

sudo systemctl enable openclaw
sudo systemctl start openclaw
```

---

## 3. 최적 설정 템플릿 (openclaw.json)

고객에게 적용할 표준 설정. 비용 최적화 + 보안 강화 포함.

```json
{
  "agents": {
    "defaults": {
      "model": {
        "primary": "anthropic/claude-sonnet-4-5",
        "fallbacks": [
          "openai/gpt-5-mini",
          "openrouter/google/gemini-3-flash-preview",
          "openrouter/google/gemini-2.5-flash-lite"
        ]
      }
    },
    "maxConcurrent": 4,
    "subagents": {
      "maxConcurrent": 8
    }
  },
  "memorySearch": {
    "sources": ["memory", "sessions"],
    "experimental": { "sessionMemory": true },
    "provider": "openai",
    "model": "text-embedding-3-small"
  },
  "contextPruning": {
    "mode": "cache-ttl",
    "ttl": "6h",
    "keepLastAssistants": 3
  },
  "compaction": {
    "mode": "default",
    "memoryFlush": {
      "enabled": true,
      "softThresholdTokens": 40000,
      "prompt": "Distill this session to memory/YYYY-MM-DD.md. Focus on decisions, state changes, lessons, blockers. If nothing worth storing: NO_FLUSH",
      "systemPrompt": "Extract only what is worth remembering. No fluff."
    }
  },
  "gateway": {
    "port": 18789,
    "bind": "loopback"
  },
  "tools": {
    "profile": "full",
    "deny": ["exec"]
  }
}
```

### 설정 포인트 설명 (고객에게 안내할 내용)

| 설정 | 이유 | 비용 영향 |
|------|------|----------|
| Sonnet을 기본값으로 | Opus보다 저렴, 대부분 작업에 충분 | 월 $20~40 절약 |
| 제공자 간 폴백 | Claude 쿼터 소진 시 Google/OpenAI로 전환 | 다운타임 방지 |
| 동시성 제한 (4/8) | 재시도 폭주 방지 | 비용 폭탄 방지 |
| 메모리 TTL 6시간 | 오래된 컨텍스트 자동 정리 | 토큰 비용 절감 |
| 40K 토큰 압축 | 긴 세션을 일일 메모리로 증류 | "왜 까먹었지" 방지 |
| exec 차단 | 쉘 명령 실행 방지 | 보안 강화 |
| loopback 바인딩 | 외부 접근 차단 | 보안 필수 |

---

## 4. 비용 안내 (고객용)

### 예상 월 비용

| 구성 | 월 비용 | 적합한 사용자 |
|------|---------|-------------|
| API만 (최소) | $5~10 | 가벼운 사용, 하루 10~20회 |
| 코딩 구독 + API | $45~50 | 일반적 사용, 하루 50~100회 |
| VPS + API | $13~18 | 24/7 상시 운영 |
| 최적화 안 한 경우 | $300~600 | ⚠️ 이렇게 되지 않도록 설정 |

### 비용 폭탄 방지 수칙
1. **비싼 모델(Opus)을 기본값으로 두지 않기** — 특정 에이전트에만 고정
2. **하트비트에 저렴한 모델 사용** — GPT-5 Nano는 하루 $0.005
3. **동시성 제한 필수** — 재시도 폭주 시 하루 $200+ 가능
4. **프롬프트 캐싱 활용** — 입력 토큰 비용 80~90% 절감

---

## 5. 보안 체크리스트

### 필수 (모든 고객에게 적용)
- [ ] 게이트웨이 loopback 바인딩 (`gateway.bind: "loopback"`)
- [ ] 파일 권한 설정 (`chmod 700 ~/.openclaw`, `chmod 600 openclaw.json`)
- [ ] API 키 환경 변수로 관리 (`.env` 파일, `chmod 600`)
- [ ] `openclaw security audit --deep` 통과 (critical 0건)
- [ ] exec 도구 차단 (`tools.deny: ["exec"]`)

### 권장 (맞춤 설정 이상 고객)
- [ ] 별도 사용자 계정 생성
- [ ] Tailscale VPN 설정 (VPS 고객)
- [ ] 방화벽 22번 포트 차단 (VPS)
- [ ] 이메일 인증 화이트리스트 설정
- [ ] 프롬프트 인젝션 방어 규칙 (AGENTS.md)
- [ ] Docker 컨테이너 격리 (가능 시)

### 보안 감사 자동화
```bash
# 설치 완료 후 반드시 실행
openclaw doctor --fix
openclaw security audit --deep

# 게이트웨이 포트 확인 (127.0.0.1만 허용)
netstat -an | grep 18789 | grep LISTEN
```

---

## 6. 업무별 자동화 설정 가이드

### 이메일 자동화 (가장 흔한 요청)
- **도구:** OpenClaw + Gmail 스킬 또는 n8n + SMTP
- **설정:** 하트비트로 30분마다 이메일 확인, 조건에 따라 자동 응답/분류
- **비용:** 하트비트 GPT-5 Nano ($0.005/일), 응답 Sonnet ($0.005/건)

### 데이터 수집/리서치
- **도구:** OpenClaw researcher 에이전트
- **설정:** Kimi 2.5를 기본 모델로, 웹 검색 스킬 활성화
- **비용:** 월 $5~10 수준

### 고객 응대 자동화
- **도구:** OpenClaw + 텔레그램/카카오톡 채널
- **설정:** communicator 에이전트, 스타일 가이드라인 적용
- **비용:** 메시지당 $0.001~0.01

### 경쟁사 모니터링
- **도구:** OpenClaw 브라우저 스킬
- **설정:** 매일 특정 시간에 경쟁사 사이트 크롤링 → 보고서 생성
- **비용:** 월 $3~5

---

## 7. 트러블슈팅

| 증상 | 원인 | 해결 |
|------|------|------|
| Gateway 시작 실패 | 포트 충돌 (9090/18789) | `lsof -i :18789` → 프로세스 종료 |
| "왜 까먹었지" | 메모리 미설정 | memorySearch + compaction 설정 |
| API 키 오류 | 공백/줄바꿈 | `.env` 파일에서 키 앞뒤 공백 제거 |
| 비용 폭탄 | 동시성 미제한 + Opus 기본값 | maxConcurrent 4, Sonnet으로 변경 |
| Node 구문 오류 | Node < 22 | `node --version` 확인 후 업그레이드 |
| 느린 응답 | 무료 티어 대기열 | 유료 API 또는 다른 제공자로 전환 |
| 세션 유실 | 비정상 종료 | `openclaw doctor --fix` 실행 |
| 보안 감사 실패 | 설정 누락 | loopback, 파일 권한, exec 차단 확인 |

---

## 8. 설치 완료 후 핸드오프

### 고객에게 전달할 것
1. **사용법 문서** (30분 교육 시 화면 공유)
2. **openclaw.json 백업** (이메일로 전송)
3. **비용 관리 안내** — "월 $50 이하로 유지하는 법"
4. **문제 발생 시 연락 방법** — Channel Talk / 이메일

### 업셀 포인트
- "혹시 매일 반복하시는 업무가 있으세요?"
- "이메일 자동화나 데이터 수집도 설정해 드릴 수 있어요"
- "월 서포트 구독으로 지속적인 관리도 가능합니다"

---

## 9. 참고: 하지 말아야 할 것

❌ **하드웨어 먼저 사라고 하지 마세요** — 워크플로우를 먼저 파악한 후 필요 시 권장
❌ **Opus를 기본값으로 두지 마세요** — 쿼터 소진 + 비용 폭탄
❌ **자동 모드/블라인드 라우팅 쓰지 마세요** — 디버깅 불가능
❌ **로컬 모델을 만능 해결책으로 제시하지 마세요** — $9,000+ 하드웨어 필요
❌ **24/7 가동을 성급히 시작하지 마세요** — 기본 안정화 먼저
