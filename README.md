# AI Tour 2026 – Copilot Promotion Landing Page

QR 코드를 통해 접속하는 Microsoft Copilot 홍보 뉴스레터 랜딩 페이지입니다.

## Architecture

```
QR Code
   ↓
https://<front-door-endpoint>/r/newsletter
   ↓
Azure Front Door (Premium + WAF)
   └── /* → Azure Static Web Apps (HTML landing)
```

## Azure Resources

| Resource | Name | SKU |
|----------|------|-----|
| Resource Group | `rg-aitour-newsletter` | — |
| Static Web App | `swa-copilot-promo` | Standard |
| Front Door | `afd-aitour-global` | Premium |
| WAF Policy | `wafpolicyaitour` | Prevention mode |

## URLs

| Purpose | URL |
|---------|-----|
| SWA Direct | `https://mango-bush-01203ee0f.2.azurestaticapps.net` |
| Front Door | `https://ep-aitour-abf2fubhgfftdufa.b02.azurefd.net` |
| QR Redirect | `/r/newsletter` → 302 → `/index.html` |
| QR Redirect (UTM) | `/r/aitour` → 302 → `/index.html?utm_source=qr&utm_campaign=aitour` |

## Project Structure

```
ai-tour-copilot-landing/
├── index.html                    # Main landing page
├── staticwebapp.config.json      # SWA routing, redirects, security headers
├── css/
│   └── (reserved for future styles)
├── images/
│   └── (reserved for future assets)
├── .github/
│   └── workflows/
│       └── azure-swa-deploy.yml  # GitHub Actions CI/CD
├── .gitignore
└── README.md
```

## Local Development

```bash
# SWA CLI로 로컬 실행
npx @azure/static-web-apps-cli start . --port 4280
```

브라우저에서 `http://localhost:4280` 접속

## Deployment

`main` 브랜치에 push하면 GitHub Actions가 자동으로 Azure Static Web Apps에 배포합니다.

### Manual Deploy

```bash
DEPLOY_TOKEN=$(az staticwebapp secrets list --name swa-copilot-promo --resource-group rg-aitour-newsletter --query "properties.apiKey" -o tsv)
swa deploy . --deployment-token $DEPLOY_TOKEN --env production
```

## Security Headers

- **HSTS**: `max-age=31536000; includeSubDomains; preload`
- **CSP**: `default-src 'self'` + Google Fonts
- **X-Content-Type-Options**: `nosniff`
- **X-Frame-Options**: `DENY`
- **Referrer-Policy**: `strict-origin-when-cross-origin`
- **Permissions-Policy**: camera, microphone, geolocation blocked

## License

Copyright © 2026 Microsoft Corporation. All Rights Reserved.
