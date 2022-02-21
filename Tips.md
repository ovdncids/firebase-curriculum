# Tips

## UID 생성
```js
db.ref().push().key;
```

## DB에서 값을 받아서 연산 후 값을 업데이트
```js
db.ref(’data’).transaction(value => value + 1);
```

## Firebase 시간 받기
```js
admin.database.ServerValue.TIMESTAMP;
```
