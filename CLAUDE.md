# 1억 RPG — 프로젝트 컨텍스트

게임형 저축 웹앱. 저금할수록 캐릭터가 성장하고 1억을 모으면 억만장자가 된다.

## 파일 구조

```
index.html          — 앱 전체 (HTML + CSS + JS 단일 파일)
assets/             — 캐릭터 이미지, PWA 아이콘
manifest.json       — PWA 설정
sw.js               — Service Worker (캐시 관리)
vercel.json         — Vercel 보안 헤더 설정
100m-rpg-v2-plan.md — v2 기획안
금융.md              — 초기 기획안
```

## 기술 스택

- Vanilla JS (ES6+) + CSS3 + HTML5 단일 파일
- localStorage (회원 데이터, 이메일별 분리)
- SubtleCrypto SHA-256 비밀번호 해싱 (email+pw+앱명 salt)
- Canvas API (월별 차트)
- 외부 라이브러리 없음 (Google Fonts CDN 제외)

## 배포

- GitHub: https://github.com/yun530/money
- Vercel: https://money-r0zdcamm2-yun530s-projects.vercel.app
- GitHub push → Vercel 자동 배포

## 핵심 데이터 구조

```js
localStorage['rpg_users'] = {
  "email": {
    name, email, pw,        // pw = SHA-256(email:pw:1억rpg)
    total, exp,
    completedMissions: [],  // 퀘스트 ID 배열
    transactions: [{ id, amount, memo, date, month }],
    initDone: true          // 초기 잔액 입력 팝업 완료 여부
  }
}
localStorage['rpg_session'] = "email"
localStorage['rpg_attempts'] = { "email": { count, lockedUntil } }
```

## 레벨 시스템

| Lv | 누적 금액 | 칭호 |
|----|---------|------|
| 1 | 0 | 누더기 거지 |
| 2 | 100만 | 평범한 시민 |
| 3 | 300만 | 직장인 |
| 4 | 500만 | 대리 |
| 5 | 1,000만 | 과장 |
| 6 | 2,000만 | 팀장 |
| 7 | 3,000만 | 임원 |
| 8 | 5,000만 | 재벌 2세 |
| 9 | 7,000만 | 회장님 |
| 10 | 1억 | 억만장자 |

## 주요 구현 결정사항

- 날짜: `toISOString()` 금지, 로컬 날짜 직접 계산 (한국 UTC+9 자정 버그)
- 예상 달성일: 전체 평균 아닌 **최근 30일 합계 / 30** 기준
- streak7 퀘스트: `calcCurrentStreak` 아닌 `calcMaxStreak` 기준
- 저금 삭제 시 퀘스트 재검증 (completedMissions 롤백)
- 로그인 5회 실패 → 30초 잠금
- Service Worker 캐시 버전: 코드 수정 시 `sw.js`의 CACHE 버전 반드시 올릴 것

## 새 컴퓨터 셋업

```bash
# 1. 레포 클론
git clone https://github.com/yun530/money.git
cd money

# 2. 스킬 복원
mkdir -p ~/.claude/skills/plan-develop ~/.claude/skills/plan-review
cp .claude/skills/plan-develop/SKILL.md ~/.claude/skills/plan-develop/SKILL.md
cp .claude/skills/plan-review/SKILL.md ~/.claude/skills/plan-review/SKILL.md

# 3. 메모리 복원 (경로는 실제 홈 디렉토리로 조정)
mkdir -p ~/.claude/projects/-Users-[username]---/memory
cp .claude/memory/* ~/.claude/projects/-Users-[username]---/memory/

# 4. Vercel (배포 필요시)
npm install -g vercel && vercel login
```
