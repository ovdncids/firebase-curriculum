# Firebase Login Phone (Firebase 9)

## Firebase Console에서 Auth 활성화
* Authentication > Sign-in method > 전화
* 테스트용 전화번호: `+1 650-555-1234`
* 테스트용 인증코드: `123456`

## reCAPTCHA 라이브러리
* https://developers.google.com/recaptcha/docs/display

public/index.html
```html
<script src="https://www.google.com/recaptcha/api.js" async defer></script>
```

## Vue.js
* https://firebase.google.com/docs/auth/web/phone-auth?authuser=0&hl=ko#web-version-9_1
* https://firebase.google.com/docs/auth/web/manage-users?hl=ko

src/main.vue
```js
import { initializeApp } from "firebase/app";

initializeApp({
  apiKey: '...',
  ...
})
```

src/App.vue
```vue
<template>
  <div>
    <div>{{displayName}}</div>
    <div>
      <h2>Google Logout</h2>
      <button @click="googleLogout()">Google Logout</button>
    </div>
    <div>
      <h2>Phone Signin</h2>
      <button @click="phoneSignin()">Phone Signin</button>
    </div>
    <div id="recaptcha-container"></div>
    <div>
      <h2>Phone Signin Confirm</h2>
      <form @submit="$event.preventDefault(); phoneSigninConfirm()">
        <input type="code" v-model="phoneSigninCode">
        <input type="submit" value="Phone Signin Confirm">
      </form>
    </div>
  </div>
</template>

<script>
import { getAuth, onAuthStateChanged, RecaptchaVerifier, signInWithPhoneNumber, updateProfile, updateEmail, signOut } from 'firebase/auth'

export default {
  data: () => ({
    auth: null,
    confirmationResult: null,
    phoneSigninCode: '',
    displayName: null,
    widgetId: null
  }),
  methods: {
    phoneSignin: function() {
      // 이미 reCAPTCHA 위젯 그려진 경우 지우기
      if (this.widgetId !== null) {
        document.getElementById('recaptcha-container').innerHTML = ''
      }

      // reCAPTCHA 위젯 준비
      const recaptchaVerifier = new RecaptchaVerifier('recaptcha-container', {
        size: 'normal',
        callback: () => {
          signInWithPhoneNumber(this.auth, '+1 650-555-1234', recaptchaVerifier)
          // signInWithPhoneNumber(this.auth, '+82 010-0000-0000', recaptchaVerifier)
          .then(confirmationResult => {
            console.log('정상 SMS 전송')
            this.confirmationResult = confirmationResult
          }).catch(error => {
            // 에러가 발생하면 reCAPTCHA 위젯 다시 그리기
            console.error('reCAPTCHA 위젯 에러', error)
            window.grecaptcha.reset(this.widgetId)
          })
        },
        'expired-callback': () => {
          // 만료 되면 reCAPTCHA 위젯 다시 그리기
          console.error('reCAPTCHA 위젯 만료')
          window.grecaptcha.reset(this.widgetId)
        }
      }, this.auth)

      // reCAPTCHA 위젯 호출
      recaptchaVerifier.render().then(widgetId => {
        console.log('reCAPTCHA 위젯 시작')
        this.widgetId = widgetId
      })
    },
    phoneSigninConfirm: function() {
      this.confirmationResult.confirm(this.phoneSigninCode).then(result => {
        console.log('전화번호 로그인 완료', result.user)
        // 최초 로그인인 경우 이름과 이메일 변경
        if (!result.user.displayName) {
          // 회원 이름 변경
          updateProfile(this.auth.currentUser, {
            displayName: '테스트 유저'
          }).then(() => {
            // 이메일 변경
            updateEmail(this.auth.currentUser, 'test@test.com').catch(error => {
              console.error('현 사이트에 동일한 이메일 있음', error)
            })
            this.displayName = '테스트 유저'
          })
        }
      }).catch(error => {
        console.error(error)
      })
    },
    googleLogout: function() {
      signOut(this.auth)
    }
  },
  created() {
    this.auth = getAuth()
    onAuthStateChanged(this.auth, user => {
      console.log('로그인 상태 변경', user)
      if (user) {
        this.displayName = user.displayName + ' 반가워요!'
      } else {
        this.displayName = ''
      }
    })
  }
}
</script>
```
