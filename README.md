# Accounts for Apollo (GraphQL)

## Resolvers

http://dev.apollodata.com/tools/graphql-tools/generate-schema.html#modularizing

```js
import uuid from 'uuid/v4';
import {
  resumeWithToken,
  validateToken,
} from 'apollo-accounts';
import {
  signUpWithPassword,
  signInWithPassword,
  validatePassword,
} from 'apollo-accounts-password';
import {
  FacebookUser,
  signUpWithFacebook,
} from 'apollo-accounts-facebook';

const resolveFunctions = {
  Query: {
    user(root, args, { user }) {
      return user;
    },
  },

  Mutation: {
    signUpWithPassword(...args) {
      const { email, services } = signUpWithPassword({ ...args });
      // Store the user somewhere and save the service settings.
      const user = {
        _id: uuid,
        services,
        emails: [ email ],
      };
      Collection.insert(user);
      return user;
    },

    signInWithPassword(...args) {
      const { email, services } = signInWithPassword({ ...args });
      const user = Collection.findOne({ email });
      if (validatePassword({ user, ...args })) {
        return user;
      }
    },

    async signUpWithFacebook(...args) {
      const {
        services,
        fields: {
          first_name,
          last_name,
        },
      } = await signUpWithFacebook({
        ...args,
        fields: {
          first_name: 1,
          last_name: 1,
        },
      });
      // Store the user somewhere and save the service settings.
      const user = {
        _id: uuid(),
        services,
        first_name,
        last_name,
      };
      Collection.insert(user);
      return user;
    },
  },

  User: {
    emails: ({ emails }) => emails,
    facebook: ({ userId, first_name, last_name }) => ({
      userId, first_name, last_name,
    }),
  },

  FacebookUser,
};
```

## Typedefs
```js
import gql from 'graphql-tag';
import { Email } from 'apollo-accounts-password';
import { FacebookUser } from 'apollo-accounts-facebook';

const User = gql`
  type User {
    _id: ID!
    emails: [Email]
    facebook: FacebookUser
  }
`;

const Mutation = gql`
  mutation Mutation {
    signUpWithPassword(
      email: String!
      password: String!
    ): User

    signInWithPassword(
      email: String!
      password: String!
    ): User

    signUpWithFacebook(
      accessToken: String!
      expiresAt: String!
    ): User
  }
`;

const Query = gql`
  type Query {
    user: User
  }
`;

const Schema = gql`
schema {
  query: Query
  mutation: Mutation
}`;

export default () => [
  User,
  Email,
  FacebookUser,
  InputEmail,
  InputPassword,
  Query,
  Mutation,
  Schema,
];
```

## Middlewares
