react-supermodel
================
react-supermodel uses mobx to create observable models and collections
 


`npm install react-supermodel --save`

Model example:

```javascript
// src/models/user
import { Model } from 'react-supermodel';
import { observable } from 'mobx';

export class User extends Model {
    
    options() { 
        return {
            baseUrl: 'https://<api-server-url>',
            basePath: '/api/v1',
            resource: 'user'
        }
    }
    
    fields() {
        return {
            name: ''
        };
    }
}

// somewhere else



// to fetch one model from the server
const user = new User();
user.id = 'some-uuid';

// this will make a GET request to https://<api-server-url>/api/v1/user/some-uuid
user.fetch();

// or, you can define the id as an option
const user = new User({
    fields: {
        id: 'some-uuid'
    }
});
// this makes a GET request to https://<api-server-url>/api/v1/user/some-uuid 
user.fetch();


// This makes a POST request to https://<api-server-url>/api/v1/user
// or a PUT request to the https://<api-server-url>/api/v1/user/some-uuid if id is set, as it is above
user.save()
```


Collection:

```javascript
// src/collections/user
import { Collection } from 'react-supermodel';
import User from '../models/user';

export class UserCollection extends Model {
    static Model = User;
}

// somewhere else
const users = new UserCollection();
// fetches users from /api/v1/user - which is bringing back an array of results or data
// (both keys are checked on the response's payload)
// that then is stored in a map based on ID of the model
users.fetch();
```

# Usage in your Views

```javascript
import React, { Component } from 'react';
import { observer } from 'mobx-react';
import { Model, Collection } from 'react-mobx-supermodel';

class Comment extends Model {
    options() { 
        return {
            baseUrl: 'https://<api-server-url>',
            basePath: '/api/v1',
            resource: 'comments'
        }
    }
    
    fields() {
        return {
            title: '',
            description: '',
            created_at: new Date()
        };
    }
}

class Comments extends Collection {
   static Model = Comment;
}

export default observer(class CustomView extends Component {
 constructor(props, context) {
   super(props, context);
   
   this.collection = new Comments();
 }
 
 componentDidMount() {
   this.collection.fetch();
 }
 
 // In react-native, use Views and Text instead of divs
 render() {
   return (
     <div>
      {this.collection.map((model) => {
       return (
         <div>
           <div>{model.title}</div>
           <div>{model.description}</div>
           <div>{model.created_at}</div>
         </div>
       )
      })}
     </div>
   )
 }
});

