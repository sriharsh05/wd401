# Dockerize the Application

Docker is a platform for developing, shipping, and running applications using containerization. It allows developers to package an application and its dependencies into a standardized unit called a container, which can be run on any system that has Docker installed.

## Dockerfile

A Dockerfile is a text document that contains all the commands a user could call on the command line to assemble an image.

```Dockerfile
FROM --platform=$BUILDPLATFORM node:lts-alpine as base
WORKDIR /app
COPY package.json /
EXPOSE 4000

FROM base as production
ENV NODE_ENV=production
RUN npm install
COPY . /app
CMD node index.js

FROM base as dev
ENV NODE_ENV=development
RUN npm install -g nodemon && npm install
COPY . /app
CMD npm run start
```

## Explanation

- `FROM --platform=$BUILDPLATFORM node:lts-alpine as base`: This line specifies the base image for the Dockerfile. The `--platform=$BUILDPLATFORM` flag is used to specify the platform for which the image is being built. In this case, it is set to the value of the `BUILDPLATFORM` build argument. The `node:lts-alpine` image is used as the base image for the application.

- `WORKDIR /app`: This line sets the working directory for the application to `/app`.

- `COPY package.json /`: This line copies the `package.json` file from the host machine to the `/` directory in the Docker image.

- `EXPOSE 4000`: This line exposes port `4000` on the Docker container.

- `FROM base as production`: This line specifies a new stage in the Dockerfile called `production` that is based on the `base` stage. This stage is used for building the production image of the application.

- `ENV NODE_ENV=production`: This line sets the `NODE_ENV` environment variable to `production`.

- `RUN npm install`: This line installs the dependencies specified in the `package.json` file.

- `COPY . /app`: This line copies the application files from the host machine to the `/app` directory in the Docker image.

- `CMD node index.js`: This line specifies the command to run the application in the production image.

- `FROM base as dev`: This line specifies a new stage in the Dockerfile called `dev` that is based on the `base` stage. This stage is used for building the development image of the application.

- `ENV NODE_ENV=development`: This line sets the `NODE_ENV` environment variable to `development`.

- `RUN npm install -g nodemon && npm install`: This line installs the `nodemon` package globally and installs the dependencies specified in the `package.json` file.

- `CMD npm run start`: This line specifies the command to run the application in the development image.


# Configure Environment Variables in Docker

## Purpose of Environment Variables

- `NODE_ENV`: This environment variable is used to specify the environment in which the application is running. It can have the values `development`, `staging`, or `production`, and is used to determine the behavior of the application based on the environment.

- `PORT`: This environment variable is used to specify the port on which the application should listen for incoming requests. It can have any valid port number as its value.

- `DB_HOST`: This environment variable is used to specify the hostname or IP address of the database server that the application should connect to.

- `DB_PORT`: This environment variable is used to specify the port on which the database server is listening for incoming connections.

- `DB_USER`: This environment variable is used to specify the username for authenticating with the database server.

- `DB_PASSWORD`: This environment variable is used to specify the password for authenticating with the database server.

- `DB_NAME`: This environment variable is used to specify the name of the database that the application should connect to.

## Secure Practices for Managing Sensitive Information

- Using a secrets management tool to securely store and manage sensitive information such as database credentials.

- Using environment variables to pass sensitive information to the application at runtime, rather than hardcoding it in configuration files or source code.

- Avoid logging sensitive information to prevent accidental exposure of credentials in log files.


# Define Docker Compose for Multiple Services

Docker Compose is a tool for defining and running multi-container Docker applications. It allows developers to define the services that make up an application in a `docker-compose.yml` file and run them using a single command.

## Docker Compose File

```yaml

version: "3.8"
services:
  app:
    build:
      context: .
      target: dev
    image: todo-401:development
    volumes:
      - .:/app
    ports:
      - "4000:4000"
    env_file:
      - .env
    depends_on:
      - db

  db:
    image: postgres:15
    volumes:
       - pg-dev-data:/var/lib/postgresql/data
    env_file: 
      - .env
    environment:
      POSTGRES_DB: $DATABASE_NAME
      POSTGRES_USER: $DB_USERNAME
      POSTGRES_PASSWORD: $DB_PASSWORD
      
volumes:
  pg-dev-data:

```

## Explanation

- `version: "3.8"`: This line specifies the version of the Docker Compose file format being used.

- `services`: This section defines the services that will be run as part of the Docker Compose configuration.

- `app`: This section defines the configuration for the Node.js application service.

  - `build`: This section specifies the build context and target for the Docker image. The `context` is set to the current directory, and the `target` is set to `dev` to build the development image.

  - `image`: This line specifies the name of the Docker image for the application service.

  - `volumes`: This line mounts the current directory to the `/app` directory in the Docker container.

  - `ports`: This line specifies the port mapping for the application service.

  - `env_file`: This line specifies the path to the `.env` file containing environment variables for the application service.

  - `depends_on`: This line specifies that the application service depends on the `db` service.

- `db`: This section defines the configuration for the database service.
    
      - `image`: This line specifies the name of the Docker image for the database service.
    
      - `volumes`: This line specifies the volume to be mounted for the database service.
    
      - `env_file`: This line specifies the path to the `.env` file containing environment variables for the database service.
    
      - `environment`: This section specifies the environment variables for the database service.

- `volumes`: This section defines the volumes to be used by the services.

The communication between the application and the database service is configured, and necessary environment variables for the database service are specified in this file.

# Setup CICD Pipeline

## GitHub Actions Workflow

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
    # Runs on Ubuntu latest version

    runs-on: ubuntu-latest

    # Define a PostgreSQL service for running tests
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

    # Steps to execute within the job
    steps:
      # Check out repository code
      - name: Check out repository code
        uses: actions/checkout@v3

        # Install dependencies
      - name: Install dependencies
        run: npm ci
      # Run unit tests
      - name: Run unit tests
        run: npm test
      # Run the app
      - name: Run the app
        id: run-app
        run: |
          npx sequelize-cli db:drop
          npx sequelize-cli db:create
          npx sequelize-cli db:migrate
          PORT=3000 npm start &
          sleep 5
      # Run integration tests
      - name: Run integration tests
        run: |
          npm install cypress cypress-json-results
          npx cypress run
  build:
    runs-on: ubuntu-latest
    steps:
      -
        name: Checkout
        uses: actions/checkout@v4
      -
        name: Login to Docker Hub
        uses: docker/login-action@v3
        with:
          username: ${{ secrets.DOCKERHUB_USERNAME }}
          password: ${{ secrets.DOCKERHUB_TOKEN }}
      -
        name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v3
      -
        name: Build and push
        uses: docker/build-push-action@v5
        with:
          context: .
          file: ./Dockerfile
          push: true
          tags: ${{ secrets.DOCKERHUB_USERNAME }}/clockbox:latest        

  # Job to deploy the app to production
  deploy:
    # Define the job dependencies
    needs: run-tests
    runs-on: ubuntu-latest
    if: needs.run-tests.result == 'success'

    # Steps to execute within the job
    steps:
      # Deploy to production using a custom action
      - name: Deploy to production
        uses: johnbeynon/render-deploy-action@v0.0.8
        with:
          service-id: "${{ secrets.MY_RENDER_SERVICE_ID }}"
          api-key: "${{ secrets.MY_RENDER_API_KEY }}"

  # Job to send Slack notifications
  notify:
    # Define the job dependencies
    needs: [run-tests, deploy]
    runs-on: ubuntu-latest

    if: ${{ always() }}
    steps:
      - name: Send Slack notification on success

        # Send a Slack notification if the tests and deployment are successful
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

- GitHub Actions is used to define a CI/CD workflow that automates the deployment of the Dockerized application.

- The workflow is triggered on every push to the repository.

- The workflow consists of multiple jobs, including `run-tests`, `build`, `deploy`, and `notify`.

- The `run-tests` job runs unit tests, integration tests, and the application itself using a PostgreSQL service.

- The `build` job builds the Docker image for the application and pushes it to Docker Hub.

- The `deploy` job deploys the application to production using a custom action.

- The `notify` job sends Slack notifications based on the success or failure of the tests and deployment.

### build-push-action

- The `docker/build-push-action` is used to build and push the Docker image to Docker Hub.

- The `context` is set to the current directory, and the `file` is set to `./Dockerfile`.

- The `tags` are specified using secrets for the Docker Hub username and the image name.

This way the CICD pipeline automates the deployment of the Dockerized application, and proper error handling and reporting are integrated within the pipeline.


# Video Demonstartion

https://drive.google.com/file/d/1JwpH_6d7J0Uq9HxkN7Fd73g5WzoKFWor/view?usp=sharing