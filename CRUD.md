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
            <th>Created Date</th>
            <th>Modify</th>
          </tr>
        </thead>
        <tbody id="tbody-members">
        </tbody>
      </table>
      <table style="display: none;">
        <tbody id="tbody-template-members">
          <tr>
            <td><input type="text" placeholder="Name" name="members-name" /></td>
            <td><input type="text" placeholder="Age" name="members-age" /></td>
            <td></td>
            <td>
              <button name="button-members-update" onclick="membersUpdate(event)">Update</button>
              <button name="button-members-delete" onclick="membersDelete(event)">Delete</button>
              <input type="hidden" name="input-members-key" />
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
    console.log(response.data);
    membersRead();
  });
};
```

## Read
```js
const membersRead = function() {
  axios.get('https://[PROJECT_ID].firebaseio.com/members.json').then(function(response) {
    console.log(response.data);
    const members = response.data;
    const tbodyMembers = document.getElementById('tbody-members');
    const tbodyTemplateMembers = document.getElementById('tbody-template-members');
    while (tbodyMembers.children.length) {
      tbodyMembers.removeChild(tbodyMembers.children[0]);
    }
    let index = 0;
    for (let key in members) {
      const member = members[key];
      const trMember = tbodyTemplateMembers.children[0].cloneNode(true);
      tbodyMembers.appendChild(trMember);
      document.getElementsByName('members-name')[index].value = member.name;
      document.getElementsByName('members-age')[index].value = member.age;
      trMember.children[2].innerHTML = member.createdDate;
      document.getElementsByName('input-members-key')[index].value = key;
      index++;
    }
  });
};
```

## Update
```js
const membersUpdate = function(event) {
  let index = 0;
  const eventElement = event.currentTarget || event.srcElement;
  for (; index < document.getElementsByName(eventElement.name).length; index++) {
    if (eventElement === document.getElementsByName(eventElement.name)[index]) break;
  }
  const member = {}
  member[document.getElementsByName('input-members-key')[index].value] = {
    name: document.getElementsByName('members-name')[index].value,
    age: document.getElementsByName('members-age')[index].value
  };
  axios.patch('https://[PROJECT_ID].firebaseio.com/members.json', member).then(function(response) {
    console.log(response.data);
    membersRead();
  });
};
```

## Delete
```js
const membersDelete = function(event) {
  let index = 0;
  const eventElement = event.currentTarget || event.srcElement;
  for (; index < document.getElementsByName(eventElement.name).length; index++) {
    if (eventElement === document.getElementsByName(eventElement.name)[index]) break;
  }
  const key = document.getElementsByName('input-members-key')[index].value;
  axios.delete('https://[PROJECT_ID].firebaseio.com/members/' + key + '.json').then(function(response) {
    console.log(response.data);
    membersRead();
  });
};
```

## Finally
```js
membersRead();
```
