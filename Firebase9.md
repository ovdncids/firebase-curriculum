# Firebase 9 for Vue.js (Firebase Login Phone, Realtime Database)

## Firebase Login Phone
### Firebase Console에서 Auth 활성화
* Authentication > Sign-in method > 전화
* 테스트용 전화번호: `+1 650-555-1234`
* 테스트용 인증코드: `123456`

### reCAPTCHA 라이브러리
* https://developers.google.com/recaptcha/docs/display

public/index.html
```html
<script src="https://www.google.com/recaptcha/api.js" async defer></script>
```

### Firebase Login Phone
* https://firebase.google.com/docs/auth/web/phone-auth?authuser=0&hl=ko#web-version-9_1
* https://firebase.google.com/docs/auth/web/manage-users?hl=ko

src/main.js
```js
import { initializeApp } from 'firebase/app'

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

## Firebase 익명 로그인, 로그아웃
src/App.vue
```js
<button @click="signInAnonymously()">익명 로그인</button>
<button @click="signOut()">로그아웃</button>

import { getAuth, signInAnonymously, signOut } from 'firebase/auth'

export default {
  data: () => ({
    auth: null
  }),
  methods: {
    signInAnonymously: function() {
      signInAnonymously(this.auth).then((user) => {
        console.log(user)
      }).catch((error) => {
        console.error(error)
      });
    },
    signOut: function() {
      signOut(this.auth)
    }
  },
  created() {
    this.auth = getAuth()
  }
}
```

## Realtime Database
src/main.js
```js
import { initializeApp } from 'firebase/app'
import { getDatabase } from 'firebase/database'

const firebaseInitializeApp = initializeApp({
  apiKey: '...',
  ...
})
getDatabase(firebaseInitializeApp)
```

src/App.vue
```vue
<template>
  <div>
    <button @click="firebaseCreateSet()">Create Set (모두 삭제하고 생성)</button>
    <button @click="firebaseCreatePush()">Create Push (고유 키를 생성하며 데이터 생성)</button>
    <button @click="firebaseRead()">Read</button>
    <button @click="firebaseDelete()">Delete</button>
    <button @click="firebaseUpdate()">Update (해당 부분만 수정)</button>
  </div>
</template>

<script>
import { getAuth } from 'firebase/auth'
import { getDatabase, ref, set, push, query, onValue, orderByChild, equalTo, off, remove, update } from 'firebase/database'

export default {
  data: () => ({
    auth: null,
    db: null
  }),
  methods: {
    firebaseCreateSet: function() {
      set(ref(this.db, `users`), {
        key1: {
          name: '홍길동',
          age: '39'
        },
        key2: {
          name: '이순신',
          age: '33'
        },
        key3: {
          name: '홍명보',
          age: '44'
        },
        key4: {
          name: '박지삼',
          age: '22'
        },
        [this.auth.currentUser?.uid]: {
          name: '권명순',
          age: '10'
        },
      })
      console.log('firebaseCreateSet')
    },
    firebaseCreatePush: function() {
      push(ref(this.db, `users`), {
        name: '춘향이',
        age: '16'
      })
      console.log('firebaseCreatePush')
    },
    firebaseRead: function() {
      // subscribe은 ref(this.db, `users/${this.auth.currentUser?.uid}`) 하나만 리턴 되는 것만 사용 한다.
      // const dbRef = query(ref(this.db, 'users')) // 여러게 리턴
      // const dbRef = query(ref(this.db, 'users'), orderByChild(`followers/${this.auth.currentUser?.uid}`)) // 여러게 리턴
      const dbRef = query(ref(this.db, 'users'), orderByChild('name'), equalTo('춘향이')) // 여러게 리턴
      onValue(dbRef, (snapshot) => {
        console.log(snapshot.size)
        snapshot.forEach((childSnapshot) => {
          console.log(childSnapshot.key, childSnapshot.val())
        })
        off(dbRef)
      }, {
        onlyOnce: false
      })
      console.log('firebaseRead')
    },
    firebaseDelete: function() {
      const key = 'key1'
      remove(ref(this.db, `users/${key}`))
      console.log('firebaseDelete')
    },
    firebaseUpdate: function() {
      const key = 'key2'
      update(ref(this.db, `users/${key}/`), {
        name: '충무공',
        address: '광화문'
      })
      console.log('firebaseUpdate')
    }
  },
  created() {
    this.auth = getAuth()
    this.db = getDatabase()
  }
}
</script>
```

<!--
## FCM (Firebase Cloud Message)
src/main.js
```js
import { initializeApp } from 'firebase/app'
import { getMessaging } from 'firebase/messaging'

const firebaseInitializeApp = initializeApp({
  apiKey: '...',
  ...
})
getMessaging(firebaseInitializeApp)
```

* [FCM Registration API 등록](https://console.cloud.google.com/apis/library/fcmregistrations.googleapis.com?authuser=4&hl=ko)

### Firebase 설정
```sh
프로젝트 설정 > 클라우드 메시징 > 웹 구성 > Generate key pair > 키 쌍 생성됨
```
-->
