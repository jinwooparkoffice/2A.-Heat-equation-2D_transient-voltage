# Fly.io 웹 브라우저로 배포하기 (CLI 없이)

이 가이드는 Fly.io CLI를 설치하지 않고 웹 브라우저만으로 배포하는 방법을 설명합니다.

## 사전 준비

1. ✅ Fly.io 계정 가입 완료 (이미 완료하셨습니다!)
2. GitHub 계정 및 저장소 필요
3. 프로젝트 코드를 GitHub에 업로드

---

## 방법 1: Fly.io 웹 UI에서 GitHub 연동 배포 (추천)

### Step 1: GitHub에 코드 업로드

1. **GitHub 저장소 생성**
   - GitHub.com에 로그인
   - 우측 상단 `+` → `New repository` 클릭
   - Repository name: 예) `heat-equation-backend`
   - Public 또는 Private 선택
   - `Create repository` 클릭

2. **로컬 코드를 GitHub에 푸시**
   
   터미널에서 다음 명령어 실행:
   
   ```bash
   # 프로젝트 디렉토리로 이동
   cd "/Users/jinwoo/Library/CloudStorage/GoogleDrive-jinwoo.park.office@gmail.com/다른 컴퓨터/내 노트북/Documents/Cursor/2. Heat equation 2D"
   
   # Git 초기화 (아직 안 했다면)
   git init
   
   # 모든 파일 추가
   git add .
   
   # 커밋
   git commit -m "Initial commit for Fly.io deployment"
   
   # GitHub 저장소 연결 (your-username과 repository-name을 실제 값으로 변경)
   git remote add origin https://github.com/your-username/your-repository-name.git
   
   # 메인 브랜치로 푸시
   git branch -M main
   git push -u origin main
   ```
   
   **또는 GitHub Desktop이나 웹 UI를 사용**할 수도 있습니다.

### Step 2: Fly.io에서 앱 생성 및 GitHub 연동

1. **Fly.io 대시보드 접속**
   - https://fly.io/dashboard 에 로그인

2. **새 앱 생성**
   - `Create New App` 또는 `New App` 버튼 클릭
   - **App Name**: 원하는 이름 입력 (예: `heat-equation-backend`)
   - **Region**: `sin` (Singapore) 선택 또는 원하는 리전 선택
   - **Launch App** 클릭

3. **GitHub 연동**
   - 앱 생성 후 `Settings` 탭으로 이동
   - 왼쪽 메뉴에서 `Source` 또는 `GitHub` 찾기
   - `Connect GitHub` 또는 `Link GitHub Repository` 클릭
   - GitHub 인증 진행
   - 저장소 선택: 위에서 만든 저장소 선택
   - 브랜치 선택: `main` 선택

4. **빌드 설정 확인**
   - `Build & Deploy` 섹션에서:
     - **Dockerfile**: `Dockerfile` (프로젝트 루트에 있음)
     - **Docker Build Context**: `.` (루트 디렉토리)
     - **Build Command**: (비워두거나 기본값 사용)
     - **Start Command**: (Dockerfile의 CMD 사용)

5. **환경 변수 설정 (선택사항)**
   - `Secrets` 또는 `Environment Variables` 탭에서:
     - `FLASK_ENV=production` (필요시)
     - `PORT=8080` (필요시, fly.toml에서 이미 설정됨)

6. **배포 실행**
   - `Deploy` 버튼 클릭 또는
   - GitHub에 푸시하면 자동 배포 (자동 배포가 활성화된 경우)

### Step 3: 배포 확인

1. **배포 상태 확인**
   - Fly.io 대시보드의 `Monitoring` 또는 `Metrics` 탭에서 확인
   - 또는 앱 URL로 직접 접속: `https://your-app-name.fly.dev`

2. **헬스 체크**
   - 브라우저에서 `https://your-app-name.fly.dev/` 접속
   - 다음 응답이 보이면 성공:
   ```json
   {"status":"ok","message":"Flask backend is running"}
   ```

---

## 방법 2: GitHub Actions를 사용한 자동 배포

GitHub Actions를 사용하면 코드를 푸시할 때마다 자동으로 배포됩니다.

### Step 1: GitHub Actions 워크플로우 파일 생성

프로젝트 루트에 다음 파일을 생성합니다:

`.github/workflows/fly-deploy.yml` 파일 생성

(이 파일은 별도로 생성해드리겠습니다)

### Step 2: Fly.io API 토큰 생성

1. Fly.io 대시보드 접속
2. `Account Settings` → `Access Tokens` 또는 `Tokens`
3. `Create Token` 클릭
4. 토큰 이름 입력 (예: `github-actions-deploy`)
5. 토큰 생성 후 **반드시 복사해두세요** (다시 볼 수 없습니다!)

### Step 3: GitHub Secrets 설정

1. GitHub 저장소로 이동
2. `Settings` → `Secrets and variables` → `Actions`
3. `New repository secret` 클릭
4. 다음 시크릿 추가:
   - **Name**: `FLY_API_TOKEN`
   - **Value**: 위에서 생성한 Fly.io API 토큰
5. `Add secret` 클릭

### Step 4: 코드 푸시

코드를 푸시하면 자동으로 배포가 시작됩니다:

```bash
git add .
git commit -m "Add GitHub Actions workflow"
git push
```

### Step 5: 배포 상태 확인

- GitHub 저장소의 `Actions` 탭에서 배포 진행 상황 확인
- Fly.io 대시보드에서 앱 상태 확인

---

## 방법 3: 온라인 터미널 사용 (GitPod, CodeSandbox 등)

만약 로컬에 Git이 없거나 터미널 사용이 어려운 경우:

1. **GitPod 사용**
   - https://gitpod.io 접속
   - GitHub 저장소 URL 입력
   - 브라우저에서 터미널 열림
   - Fly.io CLI 설치 및 배포

2. **GitHub Codespaces 사용**
   - GitHub 저장소에서 `Code` → `Codespaces` → `Create codespace`
   - 브라우저에서 VS Code 환경 제공
   - 터미널에서 Fly.io CLI 사용

---

## 문제 해결

### 배포 실패 시

1. **Fly.io 대시보드에서 로그 확인**
   - 앱 선택 → `Logs` 탭
   - 빌드 오류나 런타임 오류 확인

2. **GitHub Actions 로그 확인** (방법 2 사용 시)
   - GitHub 저장소 → `Actions` 탭
   - 실패한 워크플로우 클릭
   - 로그 확인

3. **일반적인 오류들**

   **Dockerfile을 찾을 수 없음:**
   - `Dockerfile`이 프로젝트 루트에 있는지 확인
   - GitHub에 푸시되었는지 확인

   **포트 오류:**
   - `fly.toml`의 `internal_port`가 8080인지 확인
   - `app.py`가 PORT 환경 변수를 읽는지 확인

   **메모리 부족:**
   - Fly.io 대시보드 → 앱 → `Settings` → `Machine Size`
   - 메모리를 512MB 이상으로 증가

### 앱이 시작되지 않을 때

1. **환경 변수 확인**
   - Fly.io 대시보드 → 앱 → `Secrets`
   - 필요한 환경 변수가 설정되어 있는지 확인

2. **헬스 체크 확인**
   - `fly.toml`의 헬스 체크 경로가 `/`인지 확인
   - 앱이 실제로 해당 경로에서 응답하는지 확인

---

## 추가 팁

### 자동 배포 설정

Fly.io 웹 UI에서:
1. 앱 → `Settings` → `Source`
2. `Auto Deploy` 옵션 활성화
3. 연결된 브랜치에 푸시하면 자동으로 배포됨

### 커스텀 도메인 연결

1. Fly.io 대시보드 → 앱 → `Settings` → `Domains`
2. `Add Domain` 클릭
3. 도메인 이름 입력
4. DNS 설정 안내에 따라 도메인 등록 기관에서 설정

### 모니터링 설정

1. Fly.io 대시보드 → 앱 → `Monitoring`
2. 메트릭, 로그, 알림 설정 가능

---

## 다음 단계

배포가 완료되면:

1. **프론트엔드 연동**
   - 프론트엔드 코드에서 API URL을 배포된 Fly.io URL로 변경
   - 예: `https://your-app-name.fly.dev/api/simulate`

2. **환경 변수 설정**
   - 프로덕션 환경에 필요한 환경 변수 설정
   - Fly.io 대시보드 → `Secrets`에서 관리

3. **비용 확인**
   - Fly.io 대시보드 → `Billing`에서 사용량 확인
   - 무료 티어 범위 내에서 운영 가능

---

## 참고 자료

- [Fly.io 공식 문서](https://fly.io/docs/)
- [Fly.io GitHub 연동 가이드](https://fly.io/docs/app-guides/continuous-deployment-with-github/)
- [Fly.io 웹 대시보드](https://fly.io/dashboard)


