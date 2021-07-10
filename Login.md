# Firebase Login

## Firebase Console에서 Auth 활성화
* Authentication > Sign-in method > 이메일/비밀번호
* Authentication > Sign-in method > Google

## Javascript
firebaseLogin.html
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Firebase Login</title>
  <script defer src="/__/firebase/8.2.1/firebase-app.js"></script>
  <script defer src="/__/firebase/8.2.1/firebase-auth.js"></script>
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
    <h2>Email Signup</h2>
    <form onsubmit="emailSignup(this); return false;">
      <input type="email" name="signup-email">
      <input type="password" name="signup-password">
      <input type="submit" value="Email Signup">
    </form>
  </div>
  <div>
    <h2>Email Signin</h2>
    <form onsubmit="emailSignin(this); return false;">
      <input type="email" name="signin-email">
      <input type="password" name="signin-password">
      <input type="submit" value="Email Signin">
    </form>
  </div>
</body>
</html>
```

firebaseLogin.js
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
}

const googleLogin = function() {
  const provider = new firebase.auth.GoogleAuthProvider();
  firebase.auth().signInWithPopup(provider);
};

const emailSignup = function(form) {
  const email = form['signup-email'].value
  const password = form['signup-password'].value
  firebase.auth().createUserWithEmailAndPassword(email, password).catch((error) => {
    console.error(error);
    alert(error.message);
  });
};

const emailSignin = function(form) {
  const email = form['signin-email'].value
  const password = form['signin-password'].value
  firebase.auth().signInWithEmailAndPassword(email, password).catch((error) => {
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
import * as firebase from 'firebase/app'
import 'firebase/auth'
firebase.initializeApp({
  ...
});
```
