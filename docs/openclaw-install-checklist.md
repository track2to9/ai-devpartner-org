# OpenClaw 설치 체크리스트 (내부 운영용)

딸깍AI 설치 서비스 표준 프로세스.

---

## 사전 확인

- [ ] 고객 OS 확인: macOS (Apple Silicon M1+) / Linux (Ubuntu 22.04+) / Windows (WSL2)
- [ ] 최소 사양 확인: 2 vCPU / 4 GB RAM (Docker) 또는 Apple Silicon Mac Mini M1+
- [ ] 원격 접속 도구 준비: Zoom / Google Meet / AnyDesk
- [ ] 고객에게 관리자 권한 확인 요청
- [ ] 별도 기기 권장 안내 (보안: 일상 PC와 분리)

## 설치 전 환경 준비

### macOS
- [ ] Homebrew 설치: `/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"`
- [ ] Node.js 22+ 설치: `brew install node@22`
- [ ] Git 설치 확인: `git --version`
- [ ] Docker Desktop 설치 (Docker 방식 선택 시)

### Linux (Ubuntu)
- [ ] 패키지 업데이트: `sudo apt update && sudo apt upgrade -y`
- [ ] Node.js 22+ 설치: `curl -fsSL https://deb.nodesource.com/setup_22.x | sudo -E bash - && sudo apt install -y nodejs`
- [ ] Git 설치: `sudo apt install -y git`
- [ ] Docker 설치 (Docker 방식 선택 시)

### Windows (WSL2)
- [ ] WSL2 활성화 확인
- [ ] Ubuntu 배포판 설치
- [ ] wsl.conf 설정 확인 (npm 파일 권한 문제 방지)
- [ ] 이후 Linux 절차 동일

## OpenClaw 설치

- [ ] OpenClaw 저장소 클론
- [ ] 의존성 설치: `npm install`
- [ ] 환경 변수 설정 (.env 파일)
  - [ ] API 키 입력 (Claude API / OpenAI API)
  - [ ] 키 값에 공백/줄바꿈 없는지 확인
- [ ] Gateway 시작 및 포트 확인 (기본: 9090, 18789)
  - [ ] 포트 충돌 확인: `lsof -i :9090`
- [ ] `openclaw doctor --fix` 실행 (자동 문제 해결)
- [ ] 기본 동작 테스트

## 보안 설정

- [ ] 별도 사용자 계정 생성 (선택)
- [ ] 데이터 접근 범위 제한 설정
- [ ] 방화벽 설정 (외부 접근 차단)
- [ ] API 키 보관 안내 (환경 변수, .env 파일 권한)

## 맞춤 설정 (CUSTOM 이상)

- [ ] 고객 업무 파악: "어떤 반복 업무가 있으세요?"
- [ ] 자주 사용하는 앱 연동 설정
- [ ] 워크플로우 설정 (업무별)
- [ ] 테스트 실행 및 결과 확인

## 이메일 자동화 설정 (해당 시)

- [ ] n8n 설치 (Docker 또는 클라우드)
- [ ] SMTP / SendGrid 연동
- [ ] 고객 목록 임포트
- [ ] 이메일 템플릿 작성
- [ ] 스케줄 설정 (주간/월간)
- [ ] 테스트 발송 및 확인

## 핸드오프

- [ ] 기본 사용법 교육 (30분)
- [ ] 사용법 문서 전달
- [ ] 문제 발생 시 연락 방법 안내 (Channel Talk / 이메일)
- [ ] A/S 기간 안내 (요금제별 상이)
- [ ] 고객 만족도 확인 및 후기 요청

## 업셀 체크

- [ ] "혹시 주기적으로 반복하시는 업무가 있으세요?" 질문
- [ ] 자동화 가능 여부 진단
- [ ] 업무 진단 리포트 제안 (10만원)
- [ ] 자동화 구축 견적 안내 (30~100만원)

## 트러블슈팅 참고

| 문제 | 원인 | 해결 |
|------|------|------|
| Gateway 시작 실패 | 포트 충돌 | `lsof -i :9090` 후 프로세스 종료 |
| API 키 오류 | 공백/줄바꿈 | .env 파일에서 키 값 앞뒤 공백 제거 |
| Node 구문 오류 | Node 버전 < 22 | `node --version` 확인 후 업그레이드 |
| Docker 시작 느림 | 리소스 부족 | 최소 2 vCPU / 4GB RAM 확인 |
| 스킬 설치 실패 | 패키지 버전 충돌 | `openclaw doctor --fix` 실행 |
| PID 파일 충돌 | 비정상 종료 | stale PID 파일 삭제 후 재시작 |
