# Firebase Login Email Link

## Firebase Console에서 Auth 활성화
* Authentication > Sign-in method > 이메일/비밀번호 > 이메일 링크(비밀번호가 없는 로그인)

## Javascript
### 이메일 링크 보내기
```js
const email = window.prompt('Please provide your email');
const actionCodeSettings = {
  url: window.location.origin + '/email',
  handleCodeInApp: true
};
firebase.auth().sendSignInLinkToEmail(email, actionCodeSettings).then(function() {
  window.localStorage.setItem('emailForSignIn', email);
});
```

### 이메일에서 링크 누룬 후
#### 이메일 링크 로그인
```js
if (firebase.auth().isSignInWithEmailLink(window.location.href)) {
  const email = window.localStorage.getItem('emailForSignIn');
  firebase.auth().signInWithEmailLink(email, window.location.href).catch(function(error) {
    console.error(error);
    alert(error.message);
  });
}
```

#### 이메일 가입하기
```js
if (firebase.auth().isSignInWithEmailLink(window.location.href)) {
  const email = window.localStorage.getItem('emailForSignIn');
  const password = window.prompt('Please provide your password');
  firebase.auth().createUserWithEmailAndPassword(email, password).catch(function(error) {
    console.error(error);
    alert(error.message);
  });
}
```
