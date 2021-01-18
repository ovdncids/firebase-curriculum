# CRUD (Create, Read, Update, Delete)

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
  axios.post('https://ovdncids-red-firebase-default-rtdb.firebaseio.com/members.json', member).then(function(xhr) {
    console.log(xhr.data);
    read();
  });
};
```

## Read
```js
const read = function() {
  axios.get('https://ovdncids-red-firebase-default-rtdb.firebaseio.com/members.json').then(function(xhr) {
    console.log(xhr.data);
    const members = xhr.data;
    const tbodyMembers = document.getElementById('tbody-members');
    const tbodyTemplateMembers = document.getElementById('tbody-template-members');
    while (tbodyMembers.children.length) {
      tbodyMembers.removeChild(tbodyMembers.children[0]);
    }
    let i = 0;
    for (let key in members) {
      const member = members[key];
      const trMember = tbodyTemplateMembers.children[0].cloneNode(true);
      tbodyMembers.appendChild(trMember);
      document.getElementsByName('member-name')[i].value = member.name;
      document.getElementsByName('member-age')[i].value = member.age;
      trMember.children[2].innerHTML = member.createdDate;
      document.getElementsByName('member-key')[i].value = key;
      i++;
    }
  });
};
```

## Update
```js
const update = function(event) {
  const memberUpdate = document.getElementsByName('member-update');
  const memberKey = document.getElementsByName('member-key');
  let i = 0, key;
  for (; i < memberUpdate.length; i += 1) {
    if (event.srcElement === memberUpdate[i]) {
      key = memberKey[i].value;
      break;
    }
  }
  const member = {}
  member[key] = {
    name: document.getElementsByName('member-name')[i].value,
    age: document.getElementsByName('member-age')[i].value
  };
  axios.patch('https://ovdncids-red-firebase-default-rtdb.firebaseio.com/members.json', member).then(function(xhr) {
    console.log(xhr.data);
    read();
  });
};
```

## Delete
```js
const del = function(event) {
  const memberDelete = document.getElementsByName('member-delete');
  const memberKey = document.getElementsByName('member-key');
  let i = 0;
  for (; i < memberDelete.length; i += 1) {
    if (event.srcElement === memberDelete[i]) {
      key = memberKey[i].value;
      break;
    }
  }
  axios.delete('https://ovdncids-red-firebase-default-rtdb.firebaseio.com/members/' + key + '.json').then(function(xhr) {
    console.log(xhr.data);
    read();
  });
};
```

## Finally
```js
read();
```
