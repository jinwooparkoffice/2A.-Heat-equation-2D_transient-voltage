# Heat Equation 2D Simulation

2D 열전도 방정식 시뮬레이션 프로젝트입니다. React 프론트엔드와 Flask 백엔드로 구성되어 있습니다.

## 설치 방법 (Installation)

### 1. Node.js 패키지 설치
프론트엔드 및 실행 스크립트 관리를 위한 패키지를 설치합니다.
```bash
pnpm install
```

### 2. Python 패키지 설치
백엔드 실행을 위한 패키지를 설치합니다.
```bash
# 가상환경 사용을 권장합니다 (선택사항)
# python -m venv venv
# Windows: .\venv\Scripts\activate
# Mac/Linux: source venv/bin/activate

pip install -r requirements.txt
```

## 실행 방법 (Run)

### Windows
백엔드와 프론트엔드를 동시에 실행합니다.
```bash
pnpm dev:all
```

### Mac / Linux
백엔드와 프론트엔드를 동시에 실행합니다.
```bash
pnpm dev:all:mac
```

## 기술 스택
- **Frontend**: React, Vite
- **Backend**: Flask, NumPy, SciPy (Finite Volume Method Solver)

## 프로젝트 구조
- `app.py`: Flask 백엔드 서버 및 수치해석 로직
- `src/`: React 프론트엔드 소스 코드
- `requirements.txt`: Python 의존성 목록
