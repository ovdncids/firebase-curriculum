# CRUD (Create, Read, Update, Delete)

## Backend
* [Download](https://github.com/ovdncids/vue-curriculum/raw/master/download/node-server.zip)

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
            <td><input type="text" placeholder="Name" name="member-name" /></td>
            <td><input type="text" placeholder="Age" name="member-age" /></td>
            <td></td>
            <td>
              <button name="member-update" onclick="update(event)">Update</button>
              <button name="member-delete" onclick="del(event)">Delete</button>
              <input type="hidden" name="member-key" />
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
      <button onclick="create()">Create</button>
    </div>
  </body>
</html>
```

## Create
./js/members.js
```js
const create = function() {
  const member = {
    name: document.getElementById('member-name').value,
    age: document.getElementById('member-age').value
  };
  axios.post('https://[PROJECT_ID].firebaseio.com/members.json', member).then(function(response) {
    console.log(response.data);
    read();
  });
};
```

## Read
```js
const read = function() {
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
      document.getElementsByName('member-name')[index].value = member.name;
      document.getElementsByName('member-age')[index].value = member.age;
      trMember.children[2].innerHTML = member.createdDate;
      document.getElementsByName('member-key')[index].value = key;
      index++;
    }
  });
};
```

## Update
```js
const update = function(event) {
  const memberUpdate = document.getElementsByName('member-update');
  const memberKey = document.getElementsByName('member-key');
  let index = 0, key;
  for (; index < memberUpdate.length; index += 1) {
    if (event.srcElement === memberUpdate[index]) {
      key = memberKey[index].value;
      break;
    }
  }
  const member = {}
  member[key] = {
    name: document.getElementsByName('member-name')[index].value,
    age: document.getElementsByName('member-age')[index].value
  };
  axios.patch('[PROJECT_ID].firebaseio.com/members.json', member).then(function(response) {
    console.log(response.data);
    read();
  });
};
```

## Delete
```js
const del = function(event) {
  const memberDelete = document.getElementsByName('member-delete');
  const memberKey = document.getElementsByName('member-key');
  let index = 0;
  for (; index < memberDelete.length; index += 1) {
    if (event.srcElement === memberDelete[index]) {
      key = memberKey[index].value;
      break;
    }
  }
  axios.delete('[PROJECT_ID].firebaseio.com/members/' + key + '.json').then(function(response) {
    console.log(response.data);
    read();
  });
};
```

## Finally
```js
read();
```
