# Fly.io 배포 문제 해결 가이드

## 현재 문제: "Request failed" 오류

배포는 성공했지만 헬스 체크가 실패하는 경우입니다.

---

## 해결 방법

### 1. Fly.io 웹 대시보드에서 로그 확인

1. **Fly.io 대시보드 접속**
   - https://fly.io/dashboard
   - `jouleheatingsimulation-2d` 앱 선택

2. **로그 확인**
   - 왼쪽 메뉴에서 `Logs & Errors` 클릭
   - 또는 `Monitoring` → `Logs` 탭
   - 최근 로그를 확인하여 오류 메시지 찾기

3. **일반적인 오류들**

   **포트 오류:**
   ```
   OSError: [Errno 98] Address already in use
   ```
   → 해결: `app.py`가 PORT 환경 변수를 올바르게 읽는지 확인

   **의존성 오류:**
   ```
   ModuleNotFoundError: No module named 'xxx'
   ```
   → 해결: `requirements.txt`에 모든 패키지가 포함되어 있는지 확인

   **메모리 부족:**
   ```
   killed
   ```
   → 해결: 메모리 증가 필요

---

### 2. 앱 재시작

Fly.io 웹 대시보드에서:

1. 앱 선택 → `Activity` 탭
2. `Restart` 버튼 클릭
3. 또는 최신 배포를 다시 실행

---

### 3. 헬스 체크 설정 확인

`fly.toml` 파일이 올바른지 확인:

- ✅ `internal_port = 8080` (Fly.io가 사용하는 포트)
- ✅ `PORT = "8080"` (환경 변수)
- ✅ 헬스 체크 경로: `path = "/"` (app.py의 `/` 엔드포인트와 일치)

---

### 4. 수동으로 앱 테스트

브라우저에서 직접 접속:

1. `https://jouleheatingsimulation-2d.fly.dev/` 접속
2. 다음 응답이 보여야 함:
   ```json
   {"status":"ok","message":"Flask backend is running"}
   ```

만약 접속이 안 되면:
- 로그에서 실제 오류 확인
- 앱이 시작되지 않았을 가능성

---

### 5. 환경 변수 확인

Fly.io 웹 대시보드에서:

1. 앱 선택 → `Secrets` 탭
2. 다음 환경 변수가 있는지 확인:
   - `PORT=8080` (자동 설정됨)
   - `FLASK_ENV=production` (선택사항)
   - `PYTHONUNBUFFERED=1` (선택사항)

---

### 6. 메모리 증가

메모리 부족으로 인한 문제일 수 있습니다:

1. Fly.io 대시보드 → 앱 → `Settings` → `Machine Size`
2. 메모리를 512MB → 1024MB로 증가
3. 다시 배포

---

### 7. GitHub에서 코드 확인

GitHub 저장소에서 다음 파일들이 올바르게 포함되어 있는지 확인:

- ✅ `Dockerfile`
- ✅ `fly.toml`
- ✅ `requirements.txt`
- ✅ `app.py`
- ✅ `.dockerignore`

---

### 8. 재배포

문제를 수정한 후:

1. **GitHub에 푸시** (자동 배포가 활성화된 경우)
   ```bash
   git add .
   git commit -m "Fix deployment issues"
   git push
   ```

2. **또는 Fly.io 웹 UI에서 수동 배포**
   - Fly.io 대시보드 → 앱 → `Deploy` 탭
   - `Deploy` 버튼 클릭

---

## 일반적인 문제와 해결책

### 문제 1: 앱이 시작되지 않음

**증상:** 로그에 앱 시작 메시지가 없음

**해결:**
- `app.py`의 `if __name__ == '__main__':` 블록이 올바른지 확인
- 포트가 8080인지 확인
- `host='0.0.0.0'`으로 설정되어 있는지 확인

### 문제 2: 포트 충돌

**증상:** "Address already in use" 오류

**해결:**
- `app.py`가 `PORT` 환경 변수를 올바르게 읽는지 확인
- `fly.toml`의 `internal_port`와 일치하는지 확인

### 문제 3: 의존성 설치 실패

**증상:** 빌드 단계에서 실패

**해결:**
- `requirements.txt`의 모든 패키지가 올바른지 확인
- Python 버전 호환성 확인 (3.11 사용 중)

### 문제 4: 헬스 체크 타임아웃

**증상:** "Request failed" 오류

**해결:**
- 헬스 체크 타임아웃 증가 (이미 `fly.toml`에 적용됨)
- 앱이 실제로 `/` 경로에서 응답하는지 확인
- 로그에서 앱 시작 시간 확인

---

## 다음 단계

문제를 해결한 후:

1. ✅ 앱이 정상적으로 시작되는지 확인
2. ✅ 헬스 체크가 통과하는지 확인
3. ✅ API 엔드포인트 테스트:
   ```bash
   curl https://jouleheatingsimulation-2d.fly.dev/api/progress/test
   ```

---

## 도움이 필요하신가요?

문제가 계속되면:
1. Fly.io 대시보드의 로그를 복사해서 확인
2. 구체적인 오류 메시지를 알려주시면 더 정확한 해결책을 제시할 수 있습니다


