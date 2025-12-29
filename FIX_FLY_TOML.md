# fly.toml 수정 및 배포 가이드

Fly.io 웹 UI에서 fly.toml 파일을 직접 편집할 수 없는 경우의 해결 방법입니다.

## 현재 문제
- Fly.io 웹 UI에서 fly.toml이 읽기 전용으로 표시됨
- 헬스 체크 설정(`[[http_service.checks]]`)이 없어서 배포 실패

## 해결 방법

### 방법 1: GitHub에서 수정 후 Fly.io에서 수동 배포 (추천)

#### Step 1: 로컬에서 fly.toml 확인 및 수정

로컬 파일(`fly.toml`)이 이미 올바르게 수정되어 있는지 확인:

```bash
cat fly.toml
```

다음 내용이 포함되어 있어야 합니다:
- `[env]` 섹션
- `[[http_service.checks]]` 섹션

#### Step 2: GitHub에 푸시

```bash
git add fly.toml
git commit -m "Add health check configuration to fly.toml"
git push origin main
```

#### Step 3: Fly.io에서 수동 배포

1. **Fly.io 대시보드 접속**
   - https://fly.io/dashboard
   - `jouleheatingsimulation-2d` 앱 선택

2. **Deploy 탭으로 이동**
   - 왼쪽 메뉴에서 `Activity` 또는 상단에 `Deploy` 버튼 찾기
   - 또는 직접 URL: `https://fly.io/apps/jouleheatingsimulation-2d/deploy`

3. **수동 배포 실행**
   - "Deploy" 버튼 클릭
   - 또는 "Deploy latest" 버튼 클릭
   - GitHub 저장소의 최신 커밋에서 배포 시작

### 방법 2: Fly.io CLI 사용 (터미널에서)

만약 로컬에 flyctl이 설치되어 있다면:

```bash
# 로컬 fly.toml 수정 확인
cat fly.toml

# 직접 배포
flyctl deploy
```

### 방법 3: Fly.io Secrets를 통한 환경 변수 설정

헬스 체크는 fly.toml에서 설정해야 하지만, 환경 변수는 Secrets로 설정 가능:

1. Fly.io 대시보드 → 앱 → `Secrets` 탭
2. 다음 환경 변수 추가:
   - `PORT` = `8080`
   - `FLASK_ENV` = `production`
   - `PYTHONUNBUFFERED` = `1`

하지만 **헬스 체크 설정은 fly.toml 파일에만 추가 가능**하므로, GitHub 푸시 후 수동 배포가 필요합니다.

---

## 확인해야 할 사항

### 1. GitHub 저장소에 fly.toml이 올바르게 푸시되었는지 확인

GitHub 저장소 페이지에서:
- `fly.toml` 파일 열기
- 다음 내용이 있는지 확인:

```toml
  [[http_service.checks]]
    interval = "15s"
    timeout = "10s"
    grace_period = "30s"
    method = "GET"
    path = "/"
    protocol = "http"
```

### 2. Fly.io의 GitHub 연동 확인

Fly.io 대시보드 → 앱 → `Settings` → `Source` 또는 `GitHub`:
- GitHub 저장소가 연결되어 있는지 확인
- 자동 배포가 활성화되어 있는지 확인

### 3. 배포 후 로그 확인

배포 완료 후:
1. `Logs & Errors` 탭 확인
2. 앱이 정상적으로 시작되었는지 확인
3. 헬스 체크가 통과하는지 확인

---

## 최종 확인

배포가 완료되면:

1. **브라우저에서 접속:**
   ```
   https://jouleheatingsimulation-2d.fly.dev/
   ```

2. **예상 응답:**
   ```json
   {"status":"ok","message":"Flask backend is running"}
   ```

3. **API 테스트:**
   ```
   https://jouleheatingsimulation-2d.fly.dev/api/progress/test
   ```

---

## 문제가 계속되면

1. **로그 확인:**
   - Fly.io 대시보드 → `Logs & Errors`
   - 오류 메시지 확인

2. **앱 재시작:**
   - `Activity` 탭 → `Restart` 버튼

3. **메모리 확인:**
   - `Settings` → `Machine Size`
   - 현재 1GB로 설정되어 있음 (충분함)


