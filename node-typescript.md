# Getting ready to use TypeScript with NodeJS
This documentation shows how to setup the environment to work using TypeScript with NodeJS.
1. [Initial Setup](#Initial-Setup)
2. [Debugging with Nodemon and VSCode](#Debugging-with-Nodemon-and-VSCode)
3. [Adding Jest for testing](#Adding-Jest-for-testing)
4. [Setting up ESLint](#Setting-up-ESLint)

## Initial Setup
- Initialize the project  `npm init`
- Install the dependencies  
  `
  npm add -D typescript @types/node ts-node
  `
  - **typescript** for obvious reason
  - **@types/node**  contains type definitions for Node.js
  - **ts-node** is here to run transcript without transpiling
- Initialize _tsconfig.json_ by running `npx tsc --init`, which is good because it is prefilled with almost all possible configurations (commented out) with comments explaining what they do.
- Update the _tsconfig_ to have the followings  
  ```typescript
  {
    "compilerOptions": {
      /* Basic Options */
      "target": "ES2018",                       /* Specify ECMAScript target version: 'ES3' (default), 'ES5', 'ES2015', 'ES2016', 'ES2017', 'ES2018', 'ES2019' or 'ESNEXT'. */
      "module": "commonjs",                     /* Specify module code generation: 'none', 'commonjs', 'amd', 'system', 'umd', 'es2015', or 'ESNext'. */
      "outDir": "dist",                        /* Redirect output structure to the directory. */

      /* Strict Type-Checking Options */
      "strict": true,                           /* Enable all strict type-checking options. */
      "noImplicitAny": true,                 /* Raise error on expressions and declarations with an implied 'any' type. */

      /* Module Resolution Options */
      "moduleResolution": "node",              /* Specify module resolution strategy: 'node' (Node.js) or 'classic' (TypeScript pre-1.6). */
      "esModuleInterop": true,                  /* Enables emit interoperability between CommonJS and ES Modules via creation of namespace objects for all imports. Implies 'allowSyntheticDefaultImports'. */
    },
    "include": [
      "src/**/*.ts"
    ],
    "exclude": [
      "node_modules",
      "**/*.spec.ts",
      "**/*.test.ts"
    ]
  }
  ```
- Create the source folder **src** where all typescript code will be placed, and add **_index.ts_** inside to serve as entry point of the app
- Add some useful scripts and change the main entry in _package.json_  
  ```json
  "main": "dist/index.js",
  "scripts": {
    "start": "ts-node src/index.ts",
    "compile": "tsc -p .",
    "start:js": "node ."
  }
  ```
  - _start_: starting the app without transpiling using ts-node,
  - _compile_: transpiling typescript into javascript, _.js_ files are in the **dist** folder,
  - _start:js_: start the **nodejs** app in the dist folder   

That's it! we are ready! But to make developing easier, let's add more stuff.

## Debugging with Nodemon and VSCode
>nodemon is a tool that helps develop node.js based applications by automatically restarting the node application when file changes in the directory are detected.

So, why not.
- Install Nodemon locally  
  `npm add -D nodemon`
- Add the following scripts to *package.json*  
  ```json
  "dev": "nodemon",
  "debug": "node -r ts-node/register --inspect=9000 src/index.ts"
  ```
- Create a nodemon config file **nodemon.json** in the root folder and add the following:
  ```json
  {
    "watch": ["src"],
    "ext": "ts",
    "ignore": ["**/*.test.ts", "**/*.spec.ts"],
    "exec": "npm run debug",
    "inspect": true
  }
  ```
  Basically, it means that nodemon will watch for **ts** file changes in the **src** folder to trigger the restart, except the files ending with **.test.ts** or **.spec.ts** which are normally reserved for test files. Nodemon will execute the command `npm run debug` everytime it is restarting. The last bit `"inspect": true` will allow inspecting with VSCode.

- In **VSCode**, add a debug configuration, replace the default with the following
  ```json
  {
    // Use IntelliSense to learn about possible attributes.
    // Hover to view descriptions of existing attributes.
    // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
      {
        "type": "node",
        "request": "attach",
        "name": "Attach to Process",
        "port": 9000,
        "restart": true,
        "protocol": "inspector"
      }
    ]
  }
  ```
- To debug, first run the nodemon process with `npm run dev`, then run the **_Attach to Process_** debugger in VSCode. 

>**Notes**:  
> Debugger won't be attached if Nodemon has exited. So make sure you for example have `express` server running.  
> The port `9000` can be changed to whatever free port you like. Just make sure that we have the same port in the _package.json_ and _launch.json_.


That's it! Now we can add breakpoints to our code. Tests are very important so let's add a test framework to our project, I'll be using **Jest**.

## Adding Jest for testing
- Install dependency  
  `npm add -D jest @types/jest ts-jest`

- Add the following jest configuration to the _package.json_
  ```json
  "jest": {
    "roots": [
      "<rootDir>/src"
    ],
    "transform": {
      ".ts": "ts-jest"
    },
    "testRegex": "(/__tests__/.*|\\.(test|spec))\\.ts$",
    "moduleFileExtensions": [
      "ts",
      "tsx",
      "js",
      "json"
    ],
    "collectCoverage": true
  }
  ```
  >`ts-jest` is a TypeScript preprocessor with source map support for Jest that lets you use Jest to test projects written in TypeScript.  

  `collectCoverage` helps seeing the coverage of the test
- Add more scripts to _package.json_
  ```json
  "test": "jest",
  "test:watch": "jest --watch"
  ```
  `npm run test` will run the tests once while `npm run test:watch` will run the test everytime we save some changes in the **src** folder.
  >**Note:**  
  >`--watch` is not supported without git/hg, we should use `--watchAll` instead.  
  >Make you have at least one test file and one test before running the test commands.

Now we can test with Jest! One last thing but also really good to have, we need code linting to help us preserving same code style but also catch some early errors.

## Setting up ESLint
Yes, ESLint not TSLint because TSLint will be deprecated in favor of an ESLint pluggin, read [here](https://medium.com/palantir/tslint-in-2019-1a144c2317a9) for more info.  
I will be using Airbnb code style without semicolon but feel free to use whatever you want.
- Obviously, start with the dependencies.
  - `npm add -D eslint eslint-plugin-import eslint-config-airbnb-base`
  - `npm add -D @typescript-eslint/parser @typescript-eslint/eslint-plugin`

- Create an ESLint config file **.eslintrc** in the root folder and add the following
  ```json
  {
    "env": {
      "es6": true,
      "node": true,
      "jest": true
    },
    "extends": [
      "airbnb-base"
    ],
    "globals": {
      "Atomics": "readonly",
      "SharedArrayBuffer": "readonly"
    },
    "parser": "@typescript-eslint/parser",
    "parserOptions": {
      "ecmaVersion": 2018,
      "sourceType": "module"
    },
    "plugins": [
      "@typescript-eslint"
    ],
    "rules": {
      "linebreak-style": ["error", "windows"],
      "semi": ["error", "never"],
      "no-console": [0, "allow"],
      "quotes": [1, "single"],
      "import/no-extraneous-dependencies": [2, {"devDependencies": true}]
    }
  }
  ```
  - `env` is used so that ESLint won't complain when using some global variables
  - We use `airbnb-base` to define most of the rules
  - `globals` is also used to define specific globals that are not defined in `env`. Here we add _Atomics_ and _SharedArrayBuffer_ just in case
  - `@typescript-eslint/parser` is used as parser. This is a custom parser which allow ESLint to lint TypeScript source code
  - We need to use the `@typescript-eslint` plugins
  - `rules` allow us to customize rules  
 
  See [here](https://eslint.org/docs/user-guide/configuring) for more info about ESLint configuration.
- We also need a **.eslintignore** in the root folder and write **dist** into it so that ESLint does not take into account the produced javascript.
- Add more scripts in the _package.json_
  ```json
  "lint": "eslint src/*",
  "lint:fix": "eslint src/* --fix",
  ```
  `lint` is to check for problems and `lint:fix` is to fix what ESLint can fix automatically.

That is enough but to make it more fun, we have one more step
- Install ESLint extension in VSCode, this will allow us to auto-fix on save. Add the following in the workspace _settings.json_ (can be done by creating the file inside the **.vscode** folder)
  ```json
  "eslint.autoFixOnSave": true,
  "eslint.validate": [
    "javascript",
    { "language": "typescript", "autoFix": true }
  ]
  ```
  >**_Note:_** I also added more stuff to the _settings.json_
  >```json
  >"editor.tabSize": 2,
  >"editor.insertSpaces": true,
  >"editor.detectIndentation": false,
  >```

Now we are completely ready!!!
