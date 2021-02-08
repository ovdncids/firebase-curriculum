# CRUD (Create, Read, Update, Delete)

## Firebase 데이터베이스 REST API
https://firebase.google.com/docs/reference/rest/database

## Markup
members.html
```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <title>CRUD</title>
    <script src="https://unpkg.com/core-js-bundle@3.6.0/minified.js"></script>
    <script src="https://unpkg.com/axios/dist/axios.min.js"></script>
    <script defer src="./js/members.js"></script>
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
        <tbody id="tbody-members"></tbody>
      </table>
      <table style="display: none;">
        <tbody id="tbody-template-members">
          <tr>
            <td name="members-name"></td>
            <td><input type="text" placeholder="Age" name="members-age" /></td>
            <td>
              <button name="button-members-update" onclick="membersUpdate(event)">Update</button>
              <button name="button-members-delete" onclick="membersDelete(event)">Delete</button>
              <input type="hidden" name="members-key" />
            </td>
          </tr>
        </tbody>
      </table>
    </div>
    <hr />
    <div>
      <h2>Create</h2>
      <input type="text" placeholder="Name" id="member-name" />
      <input type="text" placeholder="Age" id="member-age" />
      <button onclick="membersCreate()">Create</button>
    </div>
  </body>
</html>
```

## Create
./js/members.js
```js
const membersCreate = function() {
  const member = {
    name: document.getElementById('member-name').value,
    age: document.getElementById('member-age').value
  };
  axios.post('https://[PROJECT_ID].firebaseio.com/members.json', member).then(function(response) {
    console.log('Done membersCreate', response.data);
    membersRead();
  });
};
```

## Read
```js
const membersRead = function() {
  axios.get('https://[PROJECT_ID].firebaseio.com/members.json').then(function(response) {
    console.log('Done membersRead', response.data);
    const members = response.data;
    const tbody = document.getElementById('tbody-members');
    const tbodyTemplate = document.getElementById('tbody-template-members');
    while (tbody.children.length) {
      tbody.removeChild(tbody.children[0]);
    }
    let index = 0;
    for (let key in members) {
      const member = members[key];
      const tr = tbodyTemplate.children[0].cloneNode(true);
      tbody.appendChild(tr);
      document.getElementsByName('members-name')[index].innerHTML = member.name;
      document.getElementsByName('members-age')[index].value = member.age;
      document.getElementsByName('members-key')[index].value = key;
      document.getElementsByName('button-members-update')[index].index = index;
      document.getElementsByName('button-members-delete')[index].index = index;
      index++;
    }
  });
};
```

## Update
```js
const membersUpdate = function(event) {
  const index = event.currentTarget.index;
  const member = {}
  member[document.getElementsByName('members-key')[index].value] = {
    name: document.getElementsByName('members-name')[index].innerHTML,
    age: document.getElementsByName('members-age')[index].value
  };
  axios.patch('https://[PROJECT_ID].firebaseio.com/members.json', member).then(function(response) {
    console.log('Done membersUpdate', response.data);
    membersRead();
  });
};
```

## Delete
```js
const membersDelete = function(event) {
  const index = event.currentTarget.index;
  const key = document.getElementsByName('members-key')[index].value;
  axios.delete('https://[PROJECT_ID].firebaseio.com/members/' + key + '.json').then(function(response) {
    console.log('Done membersDelete', response.data);
    membersRead();
  });
};
```

## Finally
```js
membersRead();
```
