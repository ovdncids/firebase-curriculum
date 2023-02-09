# Firebase CRUD (Create, Read, Update, Delete)

## Firebase 데이터베이스 REST API
https://firebase.google.com/docs/reference/rest/database

## Markup
users.html
```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <title>CRUD</title>
    <script src="https://unpkg.com/core-js-bundle@3.6.0/minified.js"></script>
    <script src="https://unpkg.com/axios/dist/axios.min.js"></script>
    <script defer src="./js/users.js"></script>
  </head>
  <body>
    <h1>CRUD</h1>
    <hr />
    <div>
      <h2>Read</h2>
      <table>
        <thead>
          <tr>
            <th>Name</th>
            <th>Age</th>
            <th>Modify</th>
          </tr>
        </thead>
        <tbody id="tbody-users"></tbody>
      </table>
      <table style="display: none;">
        <tbody>
          <tr id="tr-template-users">
            <td name="users-name"></td>
            <td><input type="text" placeholder="Age" name="users-age" /></td>
            <td>
              <button name="users-update" onclick="usersUpdate(index)">Update</button>
              <button name="users-delete" onclick="usersDelete(index)">Delete</button>
              <input type="hidden" name="users-key" />
            </td>
          </tr>
        </tbody>
      </table>
    </div>
    <hr />
    <div>
      <h2>Create</h2>
      <input type="text" placeholder="Name" id="user-name" />
      <input type="text" placeholder="Age" id="user-age" />
      <button onclick="usersCreate()">Create</button>
    </div>
  </body>
</html>
```

## Create
./js/users.js
```js
const usersCreate = function() {
  const user = {
    name: document.getElementById('user-name').value,
    age: document.getElementById('user-age').value
  };
  axios.post('https://[PROJECT_ID].firebaseio.com/users.json', user).then(function(response) {
    console.log('Done usersCreate', response.data);
    usersRead();
  });
};
```

## Read
```js
const usersRead = function() {
  axios.get('https://[PROJECT_ID].firebaseio.com/users.json').then(function(response) {
    console.log('Done usersRead', response.data);
    const users = response.data;
    const tbody = document.getElementById('tbody-users');
    tbody.innerHTML = '';
    let index = 0;
    for (let key in users) {
      const user = users[key];
      const tr = document.getElementById('tr-template-users').cloneNode(true);
      tbody.appendChild(tr);
      document.getElementsByName('users-name')[index].innerHTML = user.name;
      document.getElementsByName('users-age')[index].value = user.age;
      document.getElementsByName('users-key')[index].value = key;
      document.getElementsByName('users-update')[index].index = index;
      document.getElementsByName('users-delete')[index].index = index;
      index++;
    }
  });
};
```

## Update
```js
const usersUpdate = function(index) {
  const userUpdate = {}
  userUpdate[document.getElementsByName('users-key')[index].value] = {
    name: document.getElementsByName('users-name')[index].innerHTML,
    age: document.getElementsByName('users-age')[index].value
  };
  axios.patch('https://[PROJECT_ID].firebaseio.com/users.json', userUpdate).then(function(response) {
    console.log('Done usersUpdate', response.data);
    usersRead();
  });
};
```

## Delete
```js
const usersDelete = function(index) {
  const key = document.getElementsByName('users-key')[index].value;
  axios.delete('https://[PROJECT_ID].firebaseio.com/users/' + key + '.json').then(function(response) {
    console.log('Done usersDelete', response.data);
    usersRead();
  });
};
```

## Finally
```js
usersRead();
```
