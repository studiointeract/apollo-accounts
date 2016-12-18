# Accounts for Apollo (GraphQL)

## Resolvers
http://dev.apollodata.com/tools/graphql-tools/generate-schema.html#modularizing
```js
import { User } from 'apollo-accounts/resolvers';
import {
  Email,
  InputEmail,
  InputPassword,
  signUpWithPassword,
} from 'apollo-accounts-password/resolvers';
import {
  Facebook,
  signUpWithFacebook,
} from 'apollo-accounts-facebook/resolvers';

const resolveFunctions = {
  Query: {
    user(root, args, { userId }) {
      return { userId };
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

```

## Typedefs:
```js
import gql from 'graphql-tag';
import { Email } from 'apollo-accounts-password/typedefs';
import { Facebook } from 'apollo-accounts-facebook/typedefs';

const User = gql`
  type User {
    _id: ID!
    emails: [Email]
    facebook: Facebook
  }
`;

const Mutation = gql`    
  mutation Mutation {
    signUpWithPassword(
      email: InputEmail!,
      password: InputPassword!
    ): User

    signUpWithFacebook(
      client_token: String
    ): User
  }
`;

export default () => [
  User,
  Email,
  Facebook,
  InputEmail,
  InputPassword,
  Mutation,
];
```
