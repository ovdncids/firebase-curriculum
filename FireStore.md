# FireStore Database (Cloud FireStore)

## CRUD
* https://firebase.google.com/docs/firestore/manage-data/add-data?hl=ko&authuser=0
* https://firebase.google.com/docs/firestore/manage-data/delete-data?hl=ko&authuser=0
```js
import { initializeApp } from 'firebase/app';
import {
  getFirestore, addDoc, collection,
  updateDoc, doc, serverTimestamp,
  arrayRemove, arrayUnion, increment,
  deleteField, deleteDoc, getDocs
} from 'firebase/firestore';

const app = initializeApp({
  ...
});

const db = getFirestore(app);

const docRef = await addDoc(collection(db, 'users'), {
  name: '홍길동',
  age: 39
});
await addDoc(collection(db, 'users'), {
  name: '김삼순',
  age: 33
});
await updateDoc(doc(db, 'users', docRef.id), {
  timestamp: serverTimestamp()
})
await updateDoc(doc(db, 'users', docRef.id), {
  name: arrayRemove('홍길동') // name: '' 이렇게 변경됨
})
await updateDoc(doc(db, 'users', docRef.id), {
  name: arrayUnion('홍길동', '이순신') // name: ['홍길동', '이순신']
})
await updateDoc(doc(db, 'users', docRef.id), {
  age: increment(10) // age: 49
})
await updateDoc(doc(db, 'users', docRef.id), {
  age: deleteField() // age 필드 삭제
})
await deleteDoc(doc(db, 'users', docRef.id));

const querySnapshot = await getDocs(collection(db, 'users'));
querySnapshot.forEach((doc) => {
  console.log(`${doc.id} => ${JSON.stringify(doc.data())}`);
});
```

## 정렬, 검색
* https://firebase.google.com/docs/firestore/query-data/order-limit-data?hl=ko&authuser=0

## 구독, 리슨
* https://firebase.google.com/docs/firestore/query-data/listen?hl=ko&authuser=0
