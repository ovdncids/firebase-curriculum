# Firebase Realtime Database

## Firebase Polyfill for IE11
```html
<script src="https://cdn.polyfill.io/v2/polyfill.min.js"></script>
<script src="https://unpkg.com/core-js-bundle@3.6.0/minified.js"></script>
```

## Html
index.html
```html
<button onclick="firebaseUpdate()">Update</button>
<button onclick="firebaseRemove()">Remove</button>
<button onclick="firebaseOn()">On</button>
<button onclick="firebaseOff()">Off</button>
<button onclick="firebaseOnce()">Once</button>
```

## Javascript
./js/index.js
```js
const firebaseUpdate = function() {
  const members = {
    member1: '홍길동',
    member2: '심춘향'
  };
  firebase.database().ref('members').update(members);
  console.warn('수정 완료');
};

const firebaseRemove = function() {
  firebase.database().ref('members/member1').remove();
  console.warn('삭제 완료');
};

const firebaseOn = function() {
  const f = function(data) {
    console.log(data.val());
    console.warn('알람');
  }
  window.firebaseAlarm = firebase.database().ref('members');
  window.firebaseAlarm.on('value', f);
  console.warn('구독 완료');
};

const firebaseOff = function() {
  window.firebaseAlarm.off();
  console.error('구독 취소');
};

const firebaseOnce = function() {
  const f = function(data) {
    console.log(data.val());
    console.warn('일회성 조회');
  }
  firebase.database().ref('members').once('value', f);
};
firebaseOn();
```
