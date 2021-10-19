# FireStore Database (Cloud FireStore)
* https://firebase.google.com/docs/firestore/query-data/index-overview?authuser=0
```js
import firebase from 'firebase/app';
import 'firebase/firestore';

async function getItems() {
  const snapshot = await firebase.firestore().collection('items').where('uid', '==', '유저 아이디').get();
  snapshot.size;
  snapshot.forEach((doc) => {
    console.log(doc.data());
  });
}
```
