# HidingEnvApp :monocle_face:

The purpose of this repo is to help keep our environment variables hidden as best as possible using dotenv, colors and ts-node. Based on [David Dal Busco's article.](https://itnext.io/github-actions-hide-and-set-angular-environment-variables-e753d06d16a8) :ok_hand:

## Dependencies 
[Colors](https://www.npmjs.com/package/colors), [dotenv](https://www.npmjs.com/package/dotenv), [ts-node](https://www.npmjs.com/package/ts-node), [angular fire](https://www.npmjs.com/package/@angular/fire) and [firebase](https://firebase.google.com/?hl=es) are required for this project. 

## Procedure
After installing the dependencies and configuring firebase for the project we must create 2 files: `set-env.ts` and `.env`
. The first handles the creation of the `environment.prod.ts` with the information from the `.env` and the other will contain the environment variables that will be hidden in the `.gitignore`.

```ts

//set-env.ts
import { writeFile } from 'fs';
// Configure Angular environment.ts file path
const targetPath = './src/environments/environment.prod.ts';
// Load node modules
const colors = require('colors');
require('dotenv').config();
// 'environment.ts file structure
const envConfigFile = `export const environment = {
   production: '${process.env['PRODUCTION']}',
   firebase: {
    apiKey: '${process.env['FIRE_KEY']}',
    authDomain: '${process.env['AUTH_DOMAIN']}',
    projectId: '${process.env['PROJECT_ID']}',
    storageBucket: '${process.env['STORAGE_BUCKET']}',
    messagingSenderId: '${process.env['MESSAGING_SENDER_ID']}',
    appId: '${process.env['APP_ID']}',
   }
}
`;
console.log(
  colors.magenta(
    'The file `environment.ts` will be written with the following content: \n'
  )
);
console.log(colors.grey(envConfigFile));
writeFile(targetPath, envConfigFile, function (err) {
  if (err) {
    throw console.error(err);
  } else {
    console.log(
      colors.magenta(
        `Angular environment.ts file generated correctly at ${targetPath} \n`
      )
    );
  }
});

```

Apart the colors used for the logs (by `colors` module, to have a bit of debug of what will be written in the `environment.prod.ts`), the concept is pretty clear: we are accessing the `process.env` variable made available by Node , and creating/overwriting the `environment.prod.ts` file that contains the environment object as in any Angular project.

```

//.env file
PRODUCTION=true

FIRE_KEY=YOUR_KEY
AUTH_DOMAIN=YOUR_KEY
PROJECT_ID=YOUR_KEY
STORAGE_BUCKET=YOUR_KEY
MESSAGING_SENDER_ID=YOUR_KEY
APP_ID=YOUR_KEY

```

We must also change the `package.json` scripts

```json

"scripts": {
    "ng": "ng",
    "config": "ts-node set-env.ts",
    "start": "npm run config && node server",
    "build": "npm run config && ng build",
    "watch": "ng build --watch --configuration development",
    "test": "ng test",
    "lint": "eslint . --ext .js,.ts,.css,.html",
    "format": "prettier --config .prettierrc \"./**/*.{ts,js,json,html,css}\" --write",
    "server": "npm run config && ng serve",
    "postinstall": "npm run build",
    "deploy": "npm run config && ng build && ng deploy"
  },

```

And that's it :bangbang: :heavy_check_mark: :stuck_out_tongue_winking_eye: 
