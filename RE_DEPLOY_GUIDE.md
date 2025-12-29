# Fly.io 머신 삭제 후 재배포 가이드

머신을 삭제한 후 다시 배포하는 방법입니다.

## 해결 방법

### 방법 1: Overview 페이지에서 배포 (가장 간단)

1. **Fly.io 대시보드 접속**
   - https://fly.io/dashboard
   - `jouleheatingsimulation-2d` 앱 선택

2. **Overview 페이지로 이동**
   - 왼쪽 메뉴에서 `Overview` 클릭
   - 또는 직접 URL: `https://fly.io/apps/jouleheatingsimulation-2d`

3. **Deploy 버튼 찾기**
   - 페이지 상단에 `Deploy` 또는 `Deploy latest` 버튼이 있을 수 있음
   - 또는 `Deploy App` 버튼
   - 또는 상단 오른쪽에 액션 버튼 (⋮ 메뉴)에서 배포 옵션

4. **배포 실행**
   - `Deploy` 버튼 클릭
   - GitHub 저장소의 최신 커밋에서 배포 시작

### 방법 2: Activity 페이지에서 재배포

1. **Activity 페이지로 이동**
   - 왼쪽 메뉴에서 `Activity` 클릭
   - 또는 URL: `https://fly.io/apps/jouleheatingsimulation-2d/activity`

2. **이전 배포 찾기**
   - 이전 배포 기록이 표시됨
   - 각 배포 항목 옆에 `Deploy` 또는 `Redeploy` 버튼이 있을 수 있음

3. **최신 배포 재실행**
   - 가장 최근 배포를 찾아서 `Deploy` 버튼 클릭

### 방법 3: GitHub 연동으로 자동 배포 트리거

1. **GitHub 저장소 확인**
   - 최신 `fly.toml`과 코드가 GitHub에 푸시되어 있는지 확인

2. **빈 커밋으로 재배포 트리거**
   ```bash
   git commit --allow-empty -m "Trigger redeploy"
   git push origin main
   ```

3. **Fly.io 자동 배포 확인**
   - Activity 페이지에서 새로운 배포가 시작되는지 확인

### 방법 4: 직접 URL로 배포 페이지 접근

다음 URL들을 시도해보세요:

1. **배포 페이지:**
   ```
   https://fly.io/apps/jouleheatingsimulation-2d/deploy
   ```

2. **또는:**
   ```
   https://fly.io/apps/jouleheatingsimulation-2d/deployments/new
   ```

### 방법 5: Machines 페이지에서 새 머신 생성

1. **Machines 페이지로 이동**
   - 왼쪽 메뉴에서 `Machines` 클릭

2. **"Add machine" 또는 "+" 버튼 찾기**
   - 페이지 상단에 새 머신 추가 버튼이 있을 수 있음
   - 또는 `Create machine` 버튼

3. **머신 생성**
   - 버튼 클릭 후 배포 시작

### 방법 6: Settings에서 재배포 옵션

1. **Settings 페이지로 이동**
   - 왼쪽 메뉴에서 `Settings` 클릭

2. **배포 관련 옵션 찾기**
   - "Redeploy" 또는 "Deploy" 섹션 찾기
   - 버튼이 있을 수 있음

## 가장 확실한 방법: Overview 페이지 확인

Overview 페이지가 가장 일반적으로 배포 버튼이 있는 위치입니다:

1. `Overview` 탭 클릭
2. 페이지를 스크롤하며 다음을 찾기:
   - 상단에 큰 `Deploy` 버튼
   - `Deploy App` 버튼
   - `Deploy latest` 버튼
   - `Redeploy` 버튼
   - 상단 오른쪽의 액션 메뉴 (⋮ 또는 ⚙️)

## 머신이 없을 때 배포가 자동으로 생성됨

중요: 머신이 없어도 배포를 실행하면 Fly.io가 자동으로 새 머신을 생성합니다!

- 배포를 시작하면 머신이 자동으로 생성됨
- `fly.toml` 설정에 따라 머신 크기와 개수가 결정됨
- 현재 설정: `shared-cpu-1x`, 1024MB 메모리

## 배포 후 확인

배포가 시작되면:

1. **Activity 페이지에서 진행 상황 확인**
   - 배포 단계들이 표시됨
   - 빌드 → 배포 → 헬스 체크

2. **Machines 페이지에서 새 머신 확인**
   - 배포 완료 후 새 머신이 생성됨
   - 상태가 "Started" 또는 "Running"인지 확인

3. **로그 확인**
   - `Logs & Errors` 탭에서 앱 시작 로그 확인

4. **앱 접속 테스트**
   - `https://jouleheatingsimulation-2d.fly.dev/` 접속
   - 정상 응답 확인

## 문제가 계속되면

1. **브라우저 캐시 삭제**
   - 페이지를 새로고침 (Cmd+Shift+R 또는 Ctrl+Shift+R)

2. **다른 브라우저나 시크릿 모드로 접속**

3. **Fly.io 지원팀에 문의**
   - 또는 Fly.io Discord 커뮤니티에서 도움 요청


