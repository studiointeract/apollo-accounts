# Accounts for Apollo (GraphQL)

```js
import gql from 'graphql-tags';
import { User } from 'apollo-accounts';
import {
  Email,
  InputEmail,
  InputPassword,
  signUpWithPassword,
} from 'apollo-accounts-password';
import {
  Facebook,
  signUpWithFacebook,
} from 'apollo-accounts-facebook';

const resolveFunctions = {
  Query: {
    user(root, args, { userId }) {
      return userId;
    },
  },
  Mutation: {
    signUpWithPassword,
    signUpWithFacebook,
  },
  User,
  Email,
  Facebook,
};

const typeDefs = `
  type Facebook {
    first_name
    last_name
    picture
  }
  
  type Email {
    address: String
    verified: Boolean
  }
  
  type User {
    _id: ID!
    emails: [Email]
    facebook: Facebook
    twitter: Twitter
  }
  
  type Query {
    user: User
  }
  
  scalar InputEmail
  scalar InputPassword
  
  mutation Mutation {
    signUpWithPassword(
      email: InputEmail!,
      password: InputPassword!
    ): User
  }
  
  schema {
    query: Query
    mutation: Mutation
  } 
`;
```
