# Week 3 — Decentralized Authentication

### Definintion
Decentralized authentication refers to an authentication process that is based on decentralized systems and technologies. In traditional centralized authentication, users must provide their credentials (such as usernames and passwords) to a central authority (such as a server or service) for verification. This central authority is responsible for managing user authentication and authorizing access to resources.

In contrast, decentralized authentication is based on distributed systems and technologies that allow users to authenticate themselves without relying on a central authority. Instead, authentication is based on cryptographic methods, such as public key cryptography and digital signatures, that provide secure and verifiable authentication without the need for a central authority.

## Required Homework

### 1. Provision via ClickOps a Amazon Cognito User Pool

**Amazon Cognito**
AWS Cognito is a powerful and flexible identity and access management service that enables developers to add user authentication, authorization, and management features to their applications quickly and easily

**User Pools**
Amazon Cognito user pools are a managed service that lets you add secure authentication and authorization to your apps, and can scale to support millions of users.

### Provision Cognito User Pool
Here is the user pool I created using the AWS console
![AWS Cognito user Pool](./assets/aws-cognito-userpool-created.png)

#### Install AWS Amplify
```sh
cd frontend-react-js
npm i aws-amplify --save
```

### Configure Amplify
I configured amplify by importing it into my `App.js` and add the configure script below

```js
import { Amplify } from 'aws-amplify';

Amplify.configure({
  "AWS_PROJECT_REGION": process.env.REACT_AWS_PROJECT_REGION,
  "aws_cognito_identity_pool_id": process.env.REACT_APP_AWS_COGNITO_IDENTITY_POOL_ID,
  "aws_cognito_region": process.env.REACT_APP_AWS_COGNITO_REGION,
  "aws_user_pools_id": process.env.REACT_APP_AWS_USER_POOLS_ID,
  "aws_user_pools_web_client_id": process.env.REACT_APP_CLIENT_ID,
  "oauth": {},
  Auth: {
    // We are not using an Identity Pool
    // identityPoolId: process.env.REACT_APP_IDENTITY_POOL_ID, // REQUIRED - Amazon Cognito Identity Pool ID
    region: process.env.REACT_AWS_PROJECT_REGION,           // REQUIRED - Amazon Cognito Region
    userPoolId: process.env.REACT_APP_AWS_USER_POOLS_ID,         // OPTIONAL - Amazon Cognito User Pool ID
    userPoolWebClientId: process.env.REACT_APP_AWS_USER_POOLS_WEB_CLIENT_ID,   // OPTIONAL - Amazon Cognito Web Client ID (26-char alphanumeric string)
  }
});
```

In configuring Amplify, I imported the Ampli

### 2. Install and configure Amplify client-side library for Amazon Congito
### 3. Implement API calls to Amazon Coginto for custom login, signup, recovery and forgot password page
### 4. Show conditional elements and data based on logged in or logged out
### 5. Verify JWT Token server side to serve authenticated API endpoints in Flask Application
