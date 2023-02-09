## Firebase Functions
```sh
# 로컬 functions 실행
firebase functions:shell --port=5001
  # 서버의 버전은 Node.js v6.11.5이라고 하는데 신경 쓰지 않아도 된다.
# 종료
.exit
Ctrl + c
```

### Express 프로젝트 가지고 오기
/functions 디렉토리에 Express 프로젝트를 복사
```diff
# package.json 파일은 빼고 복사
- package.json
# index.js 파일은 덮어씌우기
+ index.js
```
```sh
npm install body-parser express express-fileupload lodash moment path yamljs swagger-ui-express
```

### Firebase Admin SDK
https://console.firebase.google.com/project/vue-study-odc/settings/serviceaccounts/adminsdk

콘솔 -> 프로젝트 설정 -> 서비스 계정 -> Firebase Admin SDK -> Node.js -> 새 비공개 키 생성

생성된 파일을 /functions/config 디렉토리로 이동 후 serviceAccountKey.json으로 파일명 수정

#### index.js에 Firebase Admin SDK 추가
```js
const admin = require('firebase-admin');
const serviceAccount = require('./config/serviceAccountKey');
admin.initializeApp({
  credential: admin.credential.cert(serviceAccount),
  databaseURL: 'https://...'
});
const functions = require('firebase-functions');
```
```diff
- const port = 8081;
- app.listen(port, function () {
-   console.log('Express server listening on port ' + port);
- });
+ exports.httpsOnRequest = functions.https.onRequest(app);
```

#### functions 실행
```sh
firebase serve --only functions --port=5001
```

#### 확인
http://localhost:5001/.../.../httpsOnRequest/api/v1/user

### hosting과 연동
furebase.json
```json
  "hosting": {
    ...,
    "rewrites": [
      {
        "source": "/api/**",
        "function": "httpsOnRequest"
      },
      {
        "source": "**",
        "destination": "/index.html"
      }
    ]
```

#### hosting과 functions 동시 실행
```sh
firebase serve
```

### 배포
```sh
firebase deploy --only functions
```
