# Getting ready to use TypeScript with React
Good thing is, [Create React App](https://github.com/facebook/create-react-app) is now supporting TypeScript. Also, it already comes with ESLint and Jest for linting and testing respectively so getting ready with React and Typescript is done very quickly.
1. [Initial Setup](#Initial-Setup)
2. [Setting up ESLint](#Setting-up-ESLint)
3. [Debugging with VSCode](#Debugging-with-VSCode)
4. [Useful links](#Useful-links)

## Initial setup
Create the app directory and run the following command inside  

  ```
    npx create-react-app . --typescript
  ```
  This will create a typescript ready React App with some scripts to get start:
>### `npm start`
>
>Runs the app in the development mode.<br />
Open [http://localhost:3000](http://localhost:3000) to view it in the browser.
>
>The page will reload if you make edits.<br />
>You will also see any lint errors in the console.
>
>### `npm test`
>
>Launches the test runner in the interactive watch mode.<br />
>See the section about [running tests](https://facebook.github.io/create-react-app/docs/running-tests) for more information.
>
>### `npm run build`
>
>Builds the app for production to the `build` folder.<br />
It correctly bundles React in production mode and optimizes the build for the best performance.
>
>The build is minified and the filenames include the hashes.<br />
Your app is ready to be deployed!
>
>See the section about [deployment](https://facebook.github.io/create-react-app/docs/deployment) for more information.
>
>### `npm run eject`
>
>**Note: this is a one-way operation. Once you `eject`, you can’t go back!**
>
>If you aren’t satisfied with the build tool and configuration choices, you can `eject` at any time. This command will remove the single build dependency from your project.
>
>Instead, it will copy all the configuration files and the transitive dependencies (Webpack, Babel, ESLint, etc) right into your project so you have full control over them. All of the commands except `eject` will still work, but they will point to the copied scripts so you can tweak them. At this point you’re on your own.
>
>You don’t have to ever use `eject`. The curated feature set is suitable for small and middle deployments, and you shouldn’t feel obligated to use this feature. However we understand that this tool wouldn’t be useful if you couldn’t customize it when you are ready for it.

## Setting up ESLint
ESlint and all necessary modules to lint TypeScript are already installed.
- Let's start with creating a file **.eslintignore** to ignore some files, put the following inside
  ```
  src/serviceWorker.ts
  src/react-app-env.d.ts
  ``` 
- Create a config file **.eslintrc** in the root directory and paste this in
  ```json
  {
    "extends": [
        "react-app"
    ],
    "rules": {
        "no-console": "warn",
        "semi": ["error","never"],
        "quotes": [1,"single"]
    }
  }
  ```
- Add the following scripts in the **package.json**
  ```json
  "lint": "eslint src . --ext .tsx --ext .ts",
  "lint:fix": "eslint src . --ext .tsx --ext .ts --fix"
  ```
  `lint` is to check for problems and `lint:fix` is to fix what ESLint can fix automatically.

We can now lint our code, but let's just add Airbnb plugin.
- Install the plugin via `npm add -d eslint-config-airbnb` and change the content of **.eslintrc** to
  ```json
  {
    "extends": [
      "airbnb",
      "airbnb/hooks",
      "react-app"
    ],
    "rules": {
      "no-console": "warn",
      "semi": ["error","never"],
      "quotes": [1,"single"],
      "react/jsx-filename-extension": 0,
      "react/jsx-one-expression-per-line": 0,
      "array-bracket-newline": [1, "consistent"]
    },
    "settings": {
      "import/resolver": {
        "node": {
          "extensions": [".ts",".tsx"]
        }
      }
    }
  }
  ```
  **_Remarks_**:
  - The _settings_ is added because for things like `import App from './App'`, Eslint is giving an error `Unable to resolve path to module './App'.eslint(import/no-unresolved)`
  - The rule `"react/jsx-filename-extension": 0` is because it does not allow the use of JSX in files without *.jsx* extension.
  - The rule `"react/jsx-one-expression-per-line": 0` is to avoid only having one expression per line (you can see how ugly is the default App.tsx without this).

Last thing is to use VSCode to automate linting
- Install [ESLint extension](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint) in VSCode, this will allow us to auto-fix on save. Add the following in the workspace _settings.json_ (can be done by creating the file inside the **.vscode** folder)
  ```json
  "eslint.autoFixOnSave": true,
  "eslint.validate": [
    "javascript",
    "javascriptreact",
    {
      "language": "typescript",
      "autoFix": true
    },
    {
      "language": "typescriptreact",
      "autoFix": true
    }
  ],
  ```
  >**_Note:_** I also added more stuff to the _settings.json_
  >```json
  >"editor.tabSize": 2,
  >"editor.insertSpaces": true,
  >"editor.detectIndentation": false,
  >"files.eol": "\n"
  >```
  >**`"files.eol": "\n"`** is to use LF line ending in VSCode, default for Windows is CRLF.

## Debugging with VSCode
To get ready for debugging, install the [Debugger for Chrome extension](https://marketplace.visualstudio.com/items?itemName=msjsdiag.debugger-for-chrome) for VSCode. Add the following debug configuration
  ```json
  {
    "version": "0.2.0",
    "configurations": [
      {
        "name": "Chrome",
        "type": "chrome",
        "request": "launch",
        "url": "http://localhost:3000",
        "webRoot": "${workspaceFolder}/src",
        "sourceMapPathOverrides": {
          "webpack:///src/*": "${webRoot}/*"
        }
      }
    ]
  }
  ```
That's it! now we can start the app and then start the debugging.

## Useful links
- [Advanced React Configurations](https://create-react-app.dev/docs/advanced-configuration/)  
  It is important to note that we need to set `GENERATE_SOURCEMAP=false` in order to not ship our nice source code with the production build.
- [React TypeScript Cheatsheet](https://github.com/typescript-cheatsheets/react-typescript-cheatsheet)
- [React: Debugging Tests](https://create-react-app.dev/docs/debugging-tests)
