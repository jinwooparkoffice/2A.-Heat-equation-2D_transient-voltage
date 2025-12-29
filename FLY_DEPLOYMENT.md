# Fly.io 배포 가이드

이 문서는 Heat Equation 2D 백엔드를 Fly.io에 배포하는 단계별 가이드를 제공합니다.

## 사전 준비

### 1. Fly.io 계정 생성

1. [Fly.io 웹사이트](https://fly.io)에 접속하여 계정을 생성합니다.
2. 이메일 인증을 완료합니다.

### 2. Fly CLI 설치

**macOS (Homebrew 사용):**
```bash
brew install flyctl
```

**macOS (수동 설치):**
```bash
curl -L https://fly.io/install.sh | sh
```

**Linux:**
```bash
curl -L https://fly.io/install.sh | sh
```

**Windows:**
```powershell
powershell -Command "iwr https://fly.io/install.ps1 -useb | iex"
```

설치 후 터미널을 다시 시작하거나 PATH에 추가:
```bash
export PATH="$HOME/.fly/bin:$PATH"
```

### 3. Fly.io 로그인

터미널에서 다음 명령어를 실행하여 로그인합니다:

```bash
flyctl auth login
```

브라우저가 열리면 로그인하여 인증을 완료합니다.

## 배포 단계

### Step 1: 앱 초기화 (최초 1회만)

프로젝트 루트 디렉토리에서 실행:

```bash
flyctl launch
```

이 명령어를 실행하면 다음과 같은 질문들이 나옵니다:

- **App name?** 
  - 원하는 앱 이름을 입력하거나 엔터를 눌러 자동 생성된 이름 사용
  - 예: `heat-equation-backend` (중복되지 않는 고유한 이름이어야 함)

- **Region?**
  - 서울 근처: `sin` (Singapore) 선택 권장
  - 또는 다른 지역 선택 가능

- **Postgres?** 
  - `n` (이 프로젝트는 데이터베이스가 필요 없음)

- **Redis?** 
  - `n` (필요시 `y`)

- **Deploy now?**
  - `n` (먼저 설정을 확인하고 나중에 배포)

**중요:** `fly.toml` 파일이 이미 있으므로 기존 설정을 사용할지 물어보면 `y`를 입력하세요.

### Step 2: fly.toml 파일 확인 및 수정

`fly.toml` 파일의 다음 항목을 확인하고 필요시 수정합니다:

```toml
app = "heat-equation-backend"  # Step 1에서 입력한 앱 이름과 일치해야 함
primary_region = "sin"  # 원하는 리전으로 변경 가능
```

### Step 3: 앱 배포

배포를 실행합니다:

```bash
flyctl deploy
```

배포 과정에서:
- Docker 이미지가 빌드됩니다
- 의존성이 설치됩니다
- 앱이 Fly.io에 배포됩니다

배포가 완료되면 다음과 같은 메시지가 표시됩니다:
```
Deployed successfully!
App is available at https://heat-equation-backend.fly.dev
```

### Step 4: 앱 상태 확인

배포된 앱의 상태를 확인합니다:

```bash
flyctl status
```

앱이 실행 중인지 확인:

```bash
flyctl logs
```

### Step 5: 앱 URL 확인

앱의 URL을 확인합니다:

```bash
flyctl info
```

또는 Fly.io 대시보드에서 확인: https://fly.io/dashboard

## 추가 명령어

### 로그 확인

실시간 로그 보기:
```bash
flyctl logs
```

### SSH 접속

앱 컨테이너에 SSH 접속:
```bash
flyctl ssh console
```

### 환경 변수 설정

환경 변수 추가:
```bash
flyctl secrets set KEY=value
```

예:
```bash
flyctl secrets set FLASK_DEBUG=False
```

### 앱 재시작

앱 재시작:
```bash
flyctl apps restart <app-name>
```

### 앱 스케일링

메모리 조정:
```bash
flyctl scale vm shared-cpu-1x --memory 1024
```

CPU 조정:
```bash
flyctl scale vm shared-cpu-2x
```

### 앱 삭제

앱 완전 삭제:
```bash
flyctl apps destroy <app-name>
```

## 배포 후 확인

배포가 완료되면 다음을 확인하세요:

1. **Health Check:**
   ```bash
   curl https://<your-app-name>.fly.dev/
   ```
   
   예상 응답:
   ```json
   {"status":"ok","message":"Flask backend is running"}
   ```

2. **API 엔드포인트 테스트:**
   ```bash
   curl https://<your-app-name>.fly.dev/api/progress/test-session-id
   ```

## 문제 해결

### 배포 실패 시

1. **빌드 로그 확인:**
   ```bash
   flyctl logs --build
   ```

2. **로컬에서 Docker 이미지 테스트:**
   ```bash
   docker build -t heat-equation-backend .
   docker run -p 8080:8080 heat-equation-backend
   ```

3. **의존성 문제 확인:**
   - `requirements.txt`의 모든 패키지가 올바른지 확인
   - 특정 버전이 필요한 경우 명시적으로 버전 지정

### 앱이 시작되지 않을 때

1. **로그 확인:**
   ```bash
   flyctl logs
   ```

2. **환경 변수 확인:**
   ```bash
   flyctl secrets list
   ```

3. **포트 확인:**
   - `app.py`에서 `PORT` 환경 변수를 올바르게 사용하는지 확인
   - `fly.toml`의 `internal_port`가 일치하는지 확인

### 메모리 부족 오류

메모리 증가:
```bash
flyctl scale vm shared-cpu-1x --memory 1024
```

## 비용

Fly.io는 **무료 티어**를 제공합니다:
- 3개의 shared-cpu-1x VM (256MB RAM)
- 160GB 아웃바운드 데이터 전송

이 프로젝트는 512MB RAM을 사용하므로 무료 티어 범위 내에서 운영 가능합니다.

## 프론트엔드 연동

배포된 백엔드 URL을 프론트엔드에 설정:

```javascript
// src/App.jsx 또는 환경 설정 파일에서
const API_URL = process.env.REACT_APP_API_URL || 'https://your-app-name.fly.dev';
```

환경 변수로 설정:
```bash
# .env 파일
REACT_APP_API_URL=https://your-app-name.fly.dev
```

## 다음 단계

1. **커스텀 도메인 설정:**
   ```bash
   flyctl certs add yourdomain.com
   ```

2. **모니터링 설정:**
   - Fly.io 대시보드에서 메트릭 확인
   - 알림 설정

3. **CI/CD 설정:**
   - GitHub Actions 등을 사용하여 자동 배포 설정

## 참고 자료

- [Fly.io 공식 문서](https://fly.io/docs/)
- [Fly.io Python 가이드](https://fly.io/docs/languages-and-frameworks/python/)
- [Fly.io CLI 명령어](https://fly.io/docs/flyctl/)


