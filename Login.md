# Firebase Login

## Firebase Console에서 Auth 활성화
* Authentication > Sign-in method > 이메일/비밀번호
* Authentication > Sign-in method > Google
* ❕ 비활성화인 경우 `caught (in promise) FirebaseError: Firebase: Error (auth/configuration-not-found).`
* ❕ `firebase serve` 실행해야 `firebase 라이브러리`를 읽을 수 있다.
```sh
firebase serve --only hosting
```

## Javascript
firebaseLogin.html
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Firebase Login</title>
  <script defer src="/__/firebase/9.20.0/firebase-app-compat.js"></script>
  <script defer src="/__/firebase/9.20.0/firebase-auth-compat.js"></script>
  <script defer src="/__/firebase/init.js?useEmulator=true"></script>
  <script defer src="./js/firebaseLogin.js"></script>
</head>
<body>
  <div id="login-display"></div>
  <div>
    <h2>Google Logout</h2>
    <button onclick="googleLogout()">Google Logout</button>
  </div>
  <div>
    <h2>Google Login</h2>
    <button onclick="googleLogin()">Google Login</button>
  </div>
  <div>
    <h2>Email Sign up</h2>
    <form onsubmit="emailSignUp(this); return false;">
      <input type="email" name="signUp-email">
      <input type="password" name="signUp-password">
      <input type="submit" value="Email Signup">
    </form>
  </div>
  <div>
    <h2>Email Sign in</h2>
    <form onsubmit="emailSignIn(this); return false;">
      <input type="email" name="signIn-email">
      <input type="password" name="signIn-password">
      <input type="submit" value="Email Signin">
    </form>
  </div>
</body>
</html>
```

js/firebaseLogin.js
```js
firebase.auth().onAuthStateChanged(function(firebaseUser) {
  console.log(firebaseUser);
  if (firebaseUser) {
    document.getElementById('login-display').innerHTML = firebaseUser.email + ' 반가워요!';
  } else {
    document.getElementById('login-display').innerHTML = '';
  }
});

const googleLogout = function() {
  firebase.auth().signOut();
};

const googleLogin = function() {
  const provider = new firebase.auth.GoogleAuthProvider();
  firebase.auth().signInWithPopup(provider);
};

const emailSignUp = function(form) {
  const email = form['signUp-email'].value
  const password = form['signUp-password'].value
  firebase.auth().createUserWithEmailAndPassword(email, password).catch(function(error) {
    console.error(error);
    alert(error.message);
  });
};

const emailSignIn = function(form) {
  const email = form['signIn-email'].value
  const password = form['signIn-password'].value
  firebase.auth().signInWithEmailAndPassword(email, password).catch(function(error) {
    console.error(error);
    alert(error.message);
  });
};
```

## React or Vue.js
```
npm install firebase
```
```js
// version 9
import firebase from 'firebase/compat/app'
import 'firebase/compat/auth'
firebase.initializeApp({
  ...
});

// old version
// import firebase from 'firebase/app'
// import 'firebase/auth'

// version 9 another version
// import firebase from '@firebase/app-compat'
```

## Login Promise for React
FirebaseStore.js

```js
// Firebase 설정을 스토어에서 한다.
export default class FirebaseStore {
  firebaseUser = null;
  firebaseLoginPromise = null;
  constructor() {
    this.firebaseLoginPromise = new Promise(resolve => {
      firebase.auth().onAuthStateChanged(firebaseUser => {
        if (firebaseUser) {
          this.firebaseUser = firebaseUser;
          resolve();
        }
      });
    });
  }
}
```

component.js
```js
useEffect(() => {
  firebaseLoginPromise.then(() => {
    // 비지니스 로직
  });
}, [firebaseLoginPromise]);
```

### Logout
```js
if (firebaseUser) {
  this.firebaseUser = firebaseUser;
  resolve();
} else if (this.firebaseUser) {
  // 로그인 되어 있다면 로그아웃 실행
  alert('로그아웃 되었습니다.');
  window.location.href = '/';
}
```
