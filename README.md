# MCP 침입 탐지 시스템

MCP 서버를 위한 고급 침입 탐지 시스템 대시보드입니다. 이 애플리케이션은 실시간으로 네트워크 활동을 모니터링하고 잠재적인 보안 위협을 감지하여 시각적으로 표시합니다.

## 기능

- 실시간 보안 경고 및 알림
- 네트워크 트래픽 모니터링 및 시각화
- 지역별 트래픽 분석
- 시스템 서비스 상태 모니터링 및 제어
- 사용자 지정 보안 설정
- 자동 대응 기능 (위협 IP 자동 차단 등)

## 기술 스택

- Vue 3 + Composition API
- Chart.js를 이용한 데이터 시각화
- 반응형 디자인

## 설치 및 실행

```bash
# 의존성 설치
npm install

# 개발 서버 실행
npm run dev

# 프로덕션용 빌드
npm run build
```

## 스크린샷

![MCP 침입 탐지 시스템 스크린샷](./screenshot.png)

## 라이센스

MIT

## 파일 구조
- `src/App.vue` - 메인 애플리케이션 컴포넌트
- `src/main.js` - JavaScript 진입점
- `src/main.ts` - TypeScript 진입점
- `src/components/` - 컴포넌트 디렉토리 