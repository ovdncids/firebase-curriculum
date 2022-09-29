# Firebase
https://firebase.google.com

## Firebase CLI
https://firebase.google.com/docs/cli

### 설치
```sh
npm install -g firebase-tools
```

### 로그인
```sh
firebase login
# firebase logout
```

### 프로젝트 생성
https://console.firebase.google.com

### 프로젝트 초기화
```sh
firebase init
  Are you ready to proceed?
    # Y
  Which Firebase CLI features do you want to set up for this folder? Press Space to select features, then Enter to confirm your choices.
    # Database
    # Hosting
  Please select an option:
    # Use an existing project (이미 생성된 프로젝트일 경우)
    # Create a new project (새로 생성한 프로젝트일 경우)
  Select a default Firebase project for this directory:
    # Firebase에서 만들 프로젝트 선택
  It seems like you haven’t initialized Realtime Database in your project yet. Do you want to set it up?
    # 리얼타임 데이터베이스의 초기화 Y
    # us-central1
  What file should be used for Realtime Database Security Rules?
    # 기본 database.rules.json 엔터
  What do you want to use as your public directory?
    # public 경로 선택 (React는 build, Vue.js는 dist)
  Configure as a single-page app (rewrite all urls to /index.html)?
    # single-page app이면 Y, 아니면 N
  Set up automatic builds and deploys with GitHub?
    # N
  File build/index.html already exists. Overwrite?
    # N
```
```sh
# 현재 프로젝트 보기
firebase use

# 선택된 프로젝트 콘솔 및 여러 도움이 되는 URL 페이지에 빠르게 접속하기
firebase open
firebase open hosting:site
```

### 로컬 서버 실행
```sh
# 로컬 서버 실행
firebase serve --only hosting
firebase serve --only hosting --port=5000
firebase serve --only hosting -o 127.0.0.1 --port=5000
```

### 배포
```sh
firebase deploy --only hosting
```

#### SPA
firebase.json
```json
{
  "hosting": {
    "rewrites": [
      {
        "source": "**",
        "destination": "/index.html"
      }
    ]
  }
}
```
