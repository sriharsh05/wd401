# Configuration of Testing Framework

## Jest

Jest is a delightful JavaScript Testing Framework with a focus on simplicity. It works with projects using: Babel, TypeScript, Node, React, Angular, Vue and more. Jest is a zero-configuration testing platform and it is recommended to use Jest as a test runner for your project.

### Configuration

Jest can be configured by installing the just package and then running the following command:

```bash
npm install jest --save-dev
```

This will install the jest package and add it to the devDependencies in the package.json file. After that, create a folder with name `__tests__` in the root of the project and add the test files in this folder. Then, run the following command to run the tests:

```bash
npm test
```

This will run the tests and display the results in the terminal.

## Cypress

Cypress is a next generation front end testing tool built for the modern web. It is a fast, easy and reliable testing for anything that runs in a browser. It is recommended to use Cypress for end-to-end testing in your project.

### Configuration

Cypress can be configured by installing the cypress package and then running the following command:

```bash
npm install cypress --save-dev
```

This will install the cypress package and add it to the devDependencies in the package.json file. After that, create a folder with name `cypress` in the root of the project and add a folder named integration in the cypress folder. Then, add the test files in the integration folder.

Create a cypress.config.js file in the root of the project and add the following code:

```javascript
const { defineConfig } = require("cypress");

module.exports = defineConfig({
  integration: {
    setupNodeEvents(on, config) {
      // implement node event listeners here
      require("cypress-json-results")({
        on,
        filename: "results.json",
      });
    },
  },
});

```

Then configure the pakage.json file with the following command in th scripts section:

```json
"scripts": {
 "cy:test": "npx cypress run"
}
```

This will run the tests and display the results in the terminal.

```bash
npm run cy:test
```

# Test Suite Coverage

The entire application is covered in a test suite using unit tests and integration tests. The test suite is configured using Jest and Cypress.

## Jest

Jest is used for unit testing in the project. It is a delightful JavaScript Testing Framework with a focus on simplicity. It works with projects using: Babel, TypeScript, Node, React, Angular, Vue and more. Jest is a zero-configuration testing platform. 

### Unit Tests

The unit tests are written for the individual units of the application. The individual units are tested in isolation from the rest of the application. The unit tests are written for the functions, components and modules of the application.

## Cypress

Cypress is used for integration testing in the project. It is a next generation front end testing tool built for the modern web. It is a fast, easy and reliable testing for anything that runs in a browser. 

### Integration Tests

The integration tests are written for the entire application. The integration tests are written for the interaction between the individual units of the application. The integration tests are written for the user interface and the user experience of the application.

The test suite coverage is complete and the entire application is covered in the test suite using unit tests and integration tests. The test suite is configured using Jest and Cypress.


# Automatic Test Suite Execution on GitHub


The test suite runs automatically when pushed to GitHub. The test suite is configured using Jest and Cypress. The test suite is executed using GitHub Actions.

## GitHub Actions

GitHub Actions is used to automate the test suite execution on GitHub. It is a CI/CD platform that allows you to automate your software development workflows. It is integrated with GitHub and it allows you to build, test and deploy your code directly from GitHub.

These are the tests that are executed using GitHub Actions:

### Unit tests using Jest 
<img width="960" alt="jest" src="https://github.com/sriharsh05/wd401/assets/114745442/b4ffbfc7-01ee-4755-bf60-b0f018799725">


### Integration tests using Cypress
<img width="637" alt="cypress1" src="https://github.com/sriharsh05/wd401/assets/114745442/33bb7204-0bd7-4a46-91a1-9ecbe302aaaa">

<img width="614" alt="cypress2" src="https://github.com/sriharsh05/wd401/assets/114745442/ca1213f6-1385-4d13-a95d-03360a3de9db">


The test suite runs automatically when pushed to GitHub. The test suite is configured using Jest and Cypress.


# GitHub Actions Walkthrough

The GitHub Actions are implemented to automate the test suite execution on GitHub. The test suite is configured using Jest and Cypress. The test suite is executed using GitHub Actions.

## Workflow File

Create a workflow file in the .github/workflows directory of the project. The workflow file is a YAML file that defines the actions to be executed. The workflow file is named as main.yml.

Add the following code to the main.yml file:

```yaml
name: Auto test the project
on: push
env:
  PG_DATABASE: wd-project-test
  PG_USER: postgres
  PG_PASSWORD: admin
jobs:
  
  run-tests:
    
    runs-on: ubuntu-latest

    services:
=      postgres:
        image: postgres:11.7
        env:
          POSTGRES_USER: postgres
          POSTGRES_PASSWORD: admin
          POSTGRES_DB: wd-project-test
        options: >-
          --health-cmd pg_isready
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5
        ports:
          - 5432:5432

    steps:
      - name: Check out repository code
        uses: actions/checkout@v3

      - name: Install dependencies
        run:  npm ci

      - name: Run unit tests
        run: npm test
      - name: Run the app
        id: run-app
        run: |
          npm install
          npx sequelize-cli db:drop
          npx sequelize-cli db:create
          npx sequelize-cli db:migrate
          PORT=3000 npm start &
          sleep 5

      - name: Run integration tests
        run: |
          npm install cypress cypress-json-results
          npx cypress run --env STUDENT_SUBMISSION_URL="http://localhost:3000/"

```

This is the workflow file that defines the actions to be executed. The workflow file is a YAML file that defines the actions to be executed.

### Workflow File Explanation

- name: The name of the workflow file.
- on: The event that triggers the workflow file.
- jobs: The jobs to be executed in the workflow file.
- runs-on: The operating system on which the jobs are executed.
- steps: The steps to be executed in the jobs.

The workflow file contains the following steps:

- Check out repository code: Downloads a copy of the code in your repository before running CI tests.
- Install dependencies: Installs the dependencies of the project.
- Run unit tests: Runs the unit tests using Jest.
- Run the app: Runs the application using npm start.
- Run integration tests: Runs the integration tests using Cypress.

This way, the workflow file defines the actions to be executed and automates the test suite execution on GitHub.

The following is the workflow that is executed using GitHub Actions:
<img width="940" alt="actions" src="https://github.com/sriharsh05/wd401/assets/114745442/9730dc5a-c4d0-482c-83e0-28e3f58b1dfe">
