# Daily Deals

The Daily Deals app displays a list of deals and discounts on various products. We'll have a list of publicly available deals that anyone can see and a list of private deals available only to registered members. The private deals are exclusive to registered members, and should hopefully be better.

## How to run
### For server

Use the following commands from the project's root directory
```
cd server
npm install
node app.js
```
### For client
- If you don't already have an [Auth0](https://auth0.com/) account, sign up for a free one.

- Log into your Auth0 management dashboard and click "Create Application." Name the new application something like Daily Deals (Angular) and choose Single Page Application. Click "Create."

feel the form as following

![Create API data](https://cdn.auth0.com/blog/angular2-auth-dd/creating-api-fixed.png)

- In the settings that follow, add http://localhost:4200/callback to the Allowed Callback URLs field.

- Add http://localhost:4200 to the Allowed Logout URLs field.

- Finally, click on the Advanced Settings link at the bottom and select the OAuth tab. Make sure that the JsonWebToken Signature Algorithm is set to RS256.

- Open up the app.js file located in your server directory and make the following edits:
```
// server.js
'use strict';

const express = require('express');
const app = express();
// Import the required dependencies
const jwt = require('express-jwt');
const jwks = require('jwks-rsa');
const cors = require('cors');
const bodyParser = require('body-parser');

app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: true }));
app.use(cors());

// We are going to implement a JWT middleware that will ensure the validity of our token. We'll require each protected route to have a valid access_token sent in the Authorization header
const authCheck = jwt({
  secret: jwks.expressJwtSecret({
        cache: true,
        rateLimit: true,
        jwksRequestsPerMinute: 5,
        jwksUri: "https://{YOUR-AUTH0-DOMAIN}.auth0.com/.well-known/jwks.json"
    }),
    // This is the identifier we set when we created the API
    audience: '{YOUR-API-AUDIENCE-ATTRIBUTE}',
    issuer: "{YOUR-AUTH0-DOMAIN}", // e.g., https://you.auth0.com/
    algorithms: ['RS256']
});

app.get('/api/deals/public', (req, res)=>{
  let deals = [
        // Array of Private Deals here
        {
            id: 1234,
            name: 'Name of Product',
            description: 'Description of Product',
            originalPrice: 19.99, // Original price of product
            salePrice: 9.99 // Sale price of product
        }, {
            id: 1235,
            name: 'Name of Product',
            description: 'Description of Product',
            originalPrice: 19.99, // Original price of product
            salePrice: 9.99 // Sale price of product
        }
    ];
  res.json(deals);
})

// For the private route, we'll add this authCheck middleware
app.get('/api/deals/private', authCheck, (req,res)=>{
  let deals = [
        // Array of Private Deals here
        {
            id: 1236,
            name: 'Name of Product',
            description: 'Description of Product',
            originalPrice: 15.99, // Original price of product
            salePrice: 12.99 // Sale price of product
        }, {
            id: 1237,
            name: 'Name of Product',
            description: 'Description of Product',
            originalPrice: 20.19, // Original price of product
            salePrice: 10.99 // Sale price of product
        }
    ];
  res.json(deals);
})

app.listen(3001);
console.log('Listening on localhost:3001');
```

- Open your client/src/environments/environment.ts file and add an auth property to the constant with the following information:

```
export const environment = {
  production: false,
  auth: {
    clientID: 'YOUR-AUTH0-CLIENT-ID',
    domain: 'YOUR-AUTH0-DOMAIN', // e.g., https://you.auth0.com/
    audience: 'YOUR-AUTH0-API-IDENTIFIER', // e.g., http://localhost:3001
    redirect: 'http://localhost:4200/callback',
    scope: 'openid profile email'
  }
};
```
- Use the following commands from project root directory
```
cd client
npm install
ng serve
```
