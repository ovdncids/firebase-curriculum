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
        <tbody>
          <tr id="tr-template-members">
            <td name="members-name"></td>
            <td><input type="text" placeholder="Age" name="members-age" /></td>
            <td>
              <button name="members-update" onclick="memberUpdate(event)">Update</button>
              <button name="members-delete" onclick="memberDelete(event)">Delete</button>
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
      <button onclick="memberCreate()">Create</button>
    </div>
  </body>
</html>
```

## Create
./js/members.js
```js
const memberCreate = function() {
  const member = {
    name: document.getElementById('member-name').value,
    age: document.getElementById('member-age').value
  };
  axios.post('https://[PROJECT_ID].firebaseio.com/members.json', member).then(function(response) {
    console.log('Done memberCreate', response.data);
    membersRead();
  });
};
```

## Read
```js
const membersRead = function() {
  axios.get('https://[PROJECT_ID].firebaseio.com/members.json').then(function(response) {
    const members = response.data;
    const tbody = document.getElementById('tbody-members');
    while (tbody.children.length) {
      tbody.removeChild(tbody.children[0]);
    }
    let index = 0;
    for (let key in members) {
      const member = members[key];
      const tr = document.getElementById('tr-template-members').cloneNode(true);
      tbody.appendChild(tr);
      document.getElementsByName('members-name')[index].innerHTML = member.name;
      document.getElementsByName('members-age')[index].value = member.age;
      document.getElementsByName('members-key')[index].value = key;
      document.getElementsByName('members-update')[index].index = index;
      document.getElementsByName('members-delete')[index].index = index;
      index++;
    }
    console.log('Done membersRead', response.data);
  });
};
```

## Update
```js
const memberUpdate = function(event) {
  const index = event.currentTarget.index;
  const member = {}
  member[document.getElementsByName('members-key')[index].value] = {
    name: document.getElementsByName('members-name')[index].innerHTML,
    age: document.getElementsByName('members-age')[index].value
  };
  axios.patch('https://[PROJECT_ID].firebaseio.com/members.json', member).then(function(response) {
    console.log('Done memberUpdate', response.data);
    membersRead();
  });
};
```

## Delete
```js
const memberDelete = function(event) {
  const index = event.currentTarget.index;
  const key = document.getElementsByName('members-key')[index].value;
  axios.delete('https://[PROJECT_ID].firebaseio.com/members/' + key + '.json').then(function(response) {
    console.log('Done memberDelete', response.data);
    membersRead();
  });
};
```

## Finally
```js
membersRead();
```
