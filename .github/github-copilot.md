# Monthly Copilot — 프로젝트 요약

## 개요

**Monthly Copilot**은 Microsoft Copilot 관련 업데이트를 "스낵처럼 가볍게" 전달하는 월간 뉴스레터 랜딩 페이지입니다.  
QR 코드 또는 URL로 접속하며, 6개의 콘텐츠 카드와 뉴스레터 구독 기능을 제공합니다.

- **컨셉**: 스낵 먹듯 가볍게 읽는 Copilot 웹진 (5분 분량)
- **대상**: Microsoft 365 Copilot 사용자 및 관심자
- **언어**: 한국어 (ko)

## 기술 스택

| 구분 | 기술 |
|------|------|
| Frontend | HTML/CSS 단일 페이지 (다크 테마, 그라디언트 배경, 카드 애니메이션) |
| Hosting | Azure Static Web Apps (Standard) |
| CDN/WAF | Azure Front Door (Premium) + WAF Policy (Prevention) |
| 이메일 | Azure Logic App (Consumption) — HTTP POST 트리거 → 환영 메일 발송 |
| CI/CD | GitHub Actions — `main` push 시 자동 배포 |
| 폰트 | Google Fonts (Segoe UI) |

## Azure 리소스

| 리소스 | 이름 | SKU |
|--------|------|-----|
| Resource Group | `rg-aitour-newsletter` | — |
| Static Web App | `swa-copilot-promo` | Standard |
| Front Door | `afd-aitour-global` | Premium |
| WAF Policy | `wafpolicyaitour` | Prevention mode |
| Logic App | newsletter email workflow | Consumption |

## URL 구조

| 용도 | URL |
|------|-----|
| Front Door 엔드포인트 | `https://ep-aitour-abf2fubhgfftdufa.b02.azurefd.net` |
| SWA 직접 접속 | `https://mango-bush-01203ee0f.2.azurestaticapps.net` |
| QR 리다이렉트 | `/r/aitour` → 302 → `/index.html?utm_source=qr&utm_campaign=aitour` |
| 뉴스레터 리다이렉트 | `/r/newsletter` → 302 → `/index.html` |

## 프로젝트 구조

```
ai-tour-copilot-landing/
├── index.html                    # 메인 랜딩 페이지
├── staticwebapp.config.json      # SWA 라우팅, 리다이렉트, 보안 헤더 (CSP, HSTS 등)
├── logic-app-arm-payload.json    # Logic App ARM 배포 페이로드
├── logic-app-definition.json     # Logic App 워크플로우 정의
├── images/
│   ├── logo.png                  # Copilot 로고 (히어로 타이틀에 사용)
│   ├── QRcode.png                # 랜딩 페이지 접속 QR 코드
│   ├── M365_logo.png             # M365 로고
│   ├── Microsoft-logo_rgb_c-wht.png
│   └── Microsoft365-*.png        # OG/Hero 이미지 (16x9, 1x1, 9x16)
├── css/                          # 스타일시트 (예정)
├── .github/
│   ├── github-copilot.md         # 이 파일 (프로젝트 요약)
│   └── workflows/
│       └── azure-swa-deploy.yml  # GitHub Actions CI/CD 파이프라인
└── README.md
```

## 페이지 구성 (index.html)

### 헤더
- 🍪 Monthly Copilot 로고 + "snack-size reads" + MAR. 2026 배지

### 히어로 섹션
- **Eyebrow 배지**: "이번 달은 '가볍게 한입' 모드" (heartbeat 애니메이션)
- **타이틀**: "스낵처럼 가볍게, 🪟 Copilot 업데이트 한 입에!" (gradient-text)
- **CTA**: 랜덤 한입 추천 버튼 + 뉴스레터 구독 버튼
- **Stats**: 6 Articles / 5 min / 260 Calories

### 콘텐츠 카드 (6개)

#### 이번 달 추천 (Editor's Picks)
1. **AI Tour에서 주목할 Copilot 세션** — 스페셜
2. **최애의 코파일럿** — 정기 코너
3. **에이전톤 2026 우승 사례** — 정기 코너

#### 정기 코너
4. **Copilot의 실수 모음집** — 정기 코너
5. **우리의 Built-in 에이전트** — 정기 코너
6. **알립니다** — 정기 코너

> 모든 카드는 `https://microsoft.github.io/mwkorea/ignite/ignite2025/`로 링크

### 뉴스레터 구독
- 이메일 입력 → Logic App POST → 환영 메일 자동 발송

### 푸터
- © 2026 Monthly Copilot · All bytes reserved.

## 보안 헤더 (staticwebapp.config.json)

- **HSTS**: `max-age=31536000; includeSubDomains; preload`
- **CSP**: `default-src 'self'` + Google Fonts + Logic App connect-src
- **X-Frame-Options**: `DENY`
- **X-Content-Type-Options**: `nosniff`
- **Referrer-Policy**: `strict-origin-when-cross-origin`
- **Permissions-Policy**: camera, microphone, geolocation 차단

## CI/CD 파이프라인

- **트리거**: `main` 브랜치 push 또는 PR
- **워크플로우**: `azure-swa-deploy.yml`
- **배포 방식**: `Azure/static-web-apps-deploy@v1` (skip_app_build: true)
- **시크릿**: `AZURE_STATIC_WEB_APPS_API_TOKEN`

## 주요 CSS 특징

- 다크 테마 (`--bg: #0F0F1A`)
- 그라디언트 배경 애니메이션 (`bgShift`)
- 카드 hover 시 scale + glow 효과
- heartbeat 애니메이션 (eyebrow 배지)
- 반응형: 모바일(≤640px) h1 `1.7rem`, 로고 크기 동기화
- `clamp()` 기반 유동적 폰트 크기
