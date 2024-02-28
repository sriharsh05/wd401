# GitHub Actions Pipeline

```yaml

name: CI/CD

on: push

env:
  PG_DATABASE: "${{ secrets.POSTGRES_DATABASE }}"
  PG_USER: "${{ secrets.POSTGRES_USER }}"
  PG_PASSWORD: "${{ secrets.POSTGRES_PASSWORD }}"

# Jobs
jobs:
  
  run-tests:
    runs-on: ubuntu-latest

    services:
      postgres:
        image: postgres:11.7
        env:
           PG_DATABASE: "${{ secrets.POSTGRES_DATABASE }}"
           PG_USER: "${{ secrets.POSTGRES_USER }}"
           PG_PASSWORD: "${{ secrets.POSTGRES_PASSWORD }}"
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
        run: npm ci

      - name: Run unit tests
        run: npm test

      - name: Run the app
        id: run-app
        run: |
          npx sequelize-cli db:drop
          npx sequelize-cli db:create
          npx sequelize-cli db:migrate
          PORT=3000 npm start &
          sleep 5

      - name: Run integration tests
        run: |
          npm install cypress cypress-json-results
          npx cypress run

```

## Explanation
- The pipeline is triggered on every push to the repository.
- The pipeline runs on an ubuntu-latest runner.
- The pipeline uses a postgres database service to run the integration tests.
- The pipeline has the following steps:
  - Check out the repository code.
  - Install dependencies.
  - Run unit tests.
  - Run the app.
  - Run integration tests.

- The pipeline uses the following services:
    - postgres: The postgres database service is used to run the integration tests.

- The pipeline uses the following jobs:
    - run-tests: This job runs the tests for the project.

- The pipeline uses the following steps:
    - Check out repository code: This step checks out the code from the repository.
    - Install dependencies: This step installs the dependencies for the project.
    - Run unit tests: This step runs the unit tests for the project.
    - Run the app: This step runs the app and sets up the database.
    - Run integration tests: This step runs the integration tests for the project.

- The pipeline uses the following run-app step:
    - The run-app step installs the dependencies, sets up the database, starts the app, and waits for 5 seconds.

- The pipeline uses the following run integration tests step:
    - The run integration tests step installs cypress and runs the integration tests for the project.    

# Automated Test Cases

## Unit Tests

- The unit tests for the project is done using jest.
- All the below tests are run as part of unit tests in the pipeline.

<img width="960" alt="jest" src="https://github.com/sriharsh05/wd401/assets/114745442/b4ffbfc7-01ee-4755-bf60-b0f018799725">


## Integration Tests

- The integration tests for the project is done using cypress.
- All the below tests are run as part of integration tests in the pipeline.

<img width="637" alt="cypress1" src="https://github.com/sriharsh05/wd401/assets/114745442/33bb7204-0bd7-4a46-91a1-9ecbe302aaaa">

<img width="614" alt="cypress2" src="https://github.com/sriharsh05/wd401/assets/114745442/ca1213f6-1385-4d13-a95d-03360a3de9db">

- These tests are run as part of the pipeline and provide an additional layer of validation before deployment.
- These tests covers the key aspects of the application's features.


# Environment Variable Configuration

- The pipeline uses the following environment variables:
  - PG_DATABASE: name of the postgres database
  - PG_USER: name of the postgres user
  - PG_PASSWORD: password for the postgres user
  - SLACK_WEBHOOK: webhook URL for slack

- These environment variables encompass any sensitive or environment-specific information required for the application to function correctly across various stages.

- The environmantal variables are stored in the `Action secrets and variables` section of the repository settings.
- These variables are then used in the action workflow file in the following way.

```yaml
env:
  PG_DATABASE: ${{ secrets.PG_DATABASE }}
  PG_USER: ${{ secrets.PG_USER }}
  PG_PASSWORD: ${{ secrets.PG_PASSWORD }}
```

- This ensures that the environment variables are adequately configured within the CICD pipeline.

# Error Reporting Integration

- The pipeline is integrated with error reporting mechanisms.
- In case of any errors during the pipeline execution, the development team is immediately notified by posting detailed error messages and logs to a designated Slack channel.
- This ensures that the development team is immediately notified in case of any errors during the pipeline execution.

## Steps to configure error reporting integration in slack:

- Create a new Slack app in the Slack API website.
- Install the app to the workspace and obtain the webhook URL.
- Add the webhook URL to the action workflow file in the following way.

```yaml
name: CI/CD
on: push


env:
  PG_DATABASE: "${{ secrets.POSTGRES_DATABASE }}"
  PG_USER: "${{ secrets.POSTGRES_USER }}"
  PG_PASSWORD: "${{ secrets.POSTGRES_PASSWORD }}"

jobs:
  run-tests:
    runs-on: ubuntu-latest
    services:
      postgres:
        image: postgres:11.7
        env:
          POSTGRES_USER: "postgres"
          POSTGRES_PASSWORD: "admin"
          POSTGRES_DB: "wd-todo-test"
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
        run: npm ci
      - name: Run unit tests
        run: npm test
      - name: Run the app
        id: run-app
        run: |
          npx sequelize-cli db:drop
          npx sequelize-cli db:create
          npx sequelize-cli db:migrate
          PORT=3000 npm start &
          sleep 5
      - name: Run integration tests
        run: |
          npm install cypress cypress-json-results
          npx cypress run

  deploy:
    needs: run-tests
    runs-on: ubuntu-latest
    if: needs.run-tests.result == 'success'

    steps:
      - name: Deploy to production
        uses: johnbeynon/render-deploy-action@v0.0.8
        with:
          service-id: "${{ secrets.MY_RENDER_SERVICE_ID }}"
          api-key: "${{ secrets.MY_RENDER_API_KEY }}"

  notify:
    needs: [run-tests, deploy]
    runs-on: ubuntu-latest

    if: ${{ always() }}
    steps:
      - name: Send Slack notification on success

        if: ${{ needs.run-tests.result == 'success' && needs.deploy.result == 'success' }}
        uses: slackapi/slack-github-action@v1.25.0
        with:
          payload: |
            {
              "text": "CI/CD process succeeded!" 
            }
        env:
          SLACK_WEBHOOK_URL: ${{ secrets.SLACK_WEBHOOK_URL }}

      - name: Send Slack notification on failure
        if: ${{ needs.run-tests.result != 'success' || needs.deploy.result != 'success' }}
        uses: slackapi/slack-github-action@v1.25.0
        with:
          payload: |
            {
              "text": "*${{ github.workflow }}* failed. Access the details https://github.com/${{ github.repository }}/actions/runs/${{ github.run_id }}."
               
            }
        env:
          SLACK_WEBHOOK_URL: ${{ secrets.SLACK_WEBHOOK_URL }}
```

## Explanation

- The pipeline is integrated with error reporting mechanisms.
- In case of any errors during the pipeline execution, the development team is immediately notified by posting detailed error messages and logs to a designated Slack channel.

- The error reporting integration is configured using the following action workflow file:
  - The action workflow file includes a notify job that sends a Slack notification on success and failure of the pipeline.
  - The notify job uses the slackapi/slack-github-action action to send the Slack notification.
  - The notify job includes the SLACK_WEBHOOK_URL environment variable to configure the Slack webhook URL.

- This ensures that the development team is immediately notified in case of any errors during the pipeline execution.

### Video of test cases and pipeline execution: 

https://drive.google.com/file/d/1UxJlTyiSbCtGsgJWB1UwVY_IzIhJ3691/view?usp=sharing
