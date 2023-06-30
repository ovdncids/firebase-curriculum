# React with Firebase

## Firebase Realtime Database 설정
Database 규칙
```json
{
  "rules": {
    "user": {
      ".read": true,
      ".write": true
    }
  }
}
```

## Firebase 설치
```sh
npm install --save firebase
```

index.js
```js
import { initializeApp } from 'firebase/app';
import 'firebase/database';

initializeApp({
  apiKey: "apiKey",
  authDomain: "authDomain",
  databaseURL: "databaseURL",
  projectId: "projectId",
  storageBucket: "storageBucket",
  messagingSenderId: "messagingSenderId"
});
```

## CRUDStore 수정

### create
CRUDStore.js
```js
import firebase from 'firebase/app';

create(spinnerTarget) {
  ...

  const spinner = utils.spinner().spin(spinnerTarget);
  firebase.database().ref('user').push({
    name: this.user.name,
    age: Number(this.user.age),
    createdDate: moment().format()
  }).then(response => {
    console.log(response);
    spinner.stop();
    utils.toastr().success('Created');
  }).catch(error => {
    utils.apiCommonError(error, spinner);
  });
}
```

### read
```js
read() {
  this.usersListener = firebase.database().ref('user');
  this.usersListener.on('value', snapshot => {
    utils.nProgress.start();
    const users = _.map(snapshot.val(), (user, uid) => {
      user.uid = uid;
      return user;
    });
    this.users = _.orderBy(users, ['createdDate'], ['desc']);
    utils.nProgress.done();
  })
}

readOff() {
  if (this.usersListener) {
    this.usersListener.off();
  }
}

```

CRUD.js
```js
componentWillUnmount() {
  const { crudStore } = this.props;
  crudStore.readOff();
}

```

### update
```diff
- const user = this.users[key];
+ const user = {
+   ...this.users[key]
+ };
+ delete user.uid;
```
```js
update(spinnerTarget, key) {
  ...
  const spinner = utils.spinner().spin(spinnerTarget);
  firebase.database().ref(`user/${this.users[key].uid}`).update(user).then(response => {
    console.log(response);
    spinner.stop();
    utils.toastr().success('Updated');
  }).catch(error => {
    utils.apiCommonError(error, spinner);
  });
}

```

### delete
```js
delete() {
  ...
  const spinner = utils.spinner().spin(spinnerTarget);
  firebase.database().ref(`user/${this.users[key].uid}`).remove().then(response => {
    console.log(response);
    spinner.stop();
    utils.toastr().success('Deleted');
  }).catch(error => {
    utils.apiCommonError(error, spinner);
  });
}
```
```diff
- import axios from 'axios';
```
