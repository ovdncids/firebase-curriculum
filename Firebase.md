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
  # ? Which Firebase CLI features do you want to setup for this folder? Press Space to select features, then Enter to confirm your choices. (Press <space> to select)
    # Database
    # Functions
    # Hosting
  # ? Select a default Firebase project for this directory: (Use arrow keys)
    # 생성한 프로젝트
  # ? What file should be used for Database Rules? (database.rules.json)
    # 엔터
  # ? What language would you like to use to write Cloud Functions? (Use arrow keys)
    # JavaScript
  # ? Do you want to use ESLint to catch probable bugs and enforce style? (y/N)
    # N
  # ? Do you want to install dependencies with npm now? (Y/n)
    # Y
  # ? What do you want to use as your public directory? (public)
    # 엔터
  # ? Configure as a single-page app (rewrite all urls to /index.html)? (y/N)
    # N

# 현재 프로젝트 보기
firebase use
# 콘솔에 등록 되어 있는 프로젝트 보기
firebase list
# 선택된 프로젝트 콘솔 및 여러 도움이 되는 URL 페이지에 빠르게 접속하기
firebase open
firebase open hosting:site
```
```sh
# git 초기화
git init
```

### 로컬 서버 실행
```sh
# 로컬 서버 실행
firebase serve --only hosting
firebase serve --only hosting --port=5000
```

## Firebase Hosting
### Vue.js 프로젝트 가지고 오기
/vue 디렉토리에 Vue.js 프로젝트를 복사

/firebase.json
```diff
- "public": "public",
+ "public": "vue/dist",
```

/vue
```sh
npm install
npm run serve
npm run build
```

/public 디렉토리 삭제

### 배포
```sh
firebase deploy --only hosting
```
