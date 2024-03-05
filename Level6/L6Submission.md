# Environment Variable Configuration

## Purpose

The purpose of environment variables is to provide a way to set up configuration for the application that can be easily changed without modifying the code. This is useful for separating configuration from code, and for providing a way to configure the application for different environments, such as development, testing, and production.

## Managing Sensitive Information

Sensitive information, such as API keys or database credentials, should be managed using environment variables to ensure that they are not exposed in code or configuration files. This helps to protect sensitive information from being leaked or compromised.

## Development Environment

In the development environment, one can set up environment variables to configure the application for local development. This might include setting up a local database username and password etc.

Here are some example environment variables that one might use in a development environment:

- `DATABASE_USERNAME`: The username for local development database.
- `DATABASE_PASSWORD`: The password for local development database.
- `DATABASE_NAME`: The name of the local development database


## Production Environment

In the production environment, one can set up environment variables to configure the application for deployment to a production server. This might include setting up a production database connection string, using a different API key for production, or other production-specific configuration.

Here are some example environment variables that  might use in a production environment:

- `POSTGRES_USER`: The username for the production database.
- `POSTGRES_DB`: The name of the production database.
- `POSTGRES_PASSWORD`: The password for the production database.
- `RENDER_API_KEY`: The API key for the Render service.
- `RENDER_SERVICE_ID`: The service ID for the Render service.
- `SLACK_WEBHOOK_URL`: The webhook URL for the Slack integration.

 
# PM2 Cluster Mode Deployment

## PM2 Configuration Settings

The PM2 configuration file which is called as ecosystem.config.js is used to configure the application for deployment in cluster mode. It is created using the command `pm2 ecosystem` and then the configuration parameters are set in the file.

Here is the ecosystem.config.js file :

```javascript

module.exports = {  
  apps: [  
    {  
      name: "Todo-401",  
      script: "index.js",  
      instances: "8",  
      exec_mode: "cluster",  
      watch: true,  
      max_memory_restart: "14",  
      log_date_format: "YYYY-MM-DD HH:mm Z",  
      env: {  
        NODE_ENV: "development",  
      },  
      env_production: {  
        NODE_ENV: "production",  
      },  
    },  
  ],  
};
  
  ```

### Configuration Parameters

- `name`: The name of the application.
- `script`: The entry point of the application.
- `instances`: The number of instances to run in cluster mode.
- `exec_mode`: The execution mode for the application.
- `watch`: Whether to watch for changes in the application files.
- `max_memory_restart`: The maximum memory to use before restarting the application.
- `log_date_format`: The format for the log date.
- `env`: The environment variables for the development environment.
- `env_production`: The environment variables for the production environment.

To run the application in cluster mode using PM2, we can use the following command:

```bash
pm2 start ecosystem.config.js
```

To stop all the instanses, we can use the following command:

```bash
pm2 stop all
```

To delete all the instances, we can use the following command:

```bash
pm2 delete all
```

# Security Measures

## Securing Environment Variables

To secure environment variables and ensure that they are not exposed unintentionally, we can follow these best practices:

- Use a .env file to store environment variables, and add the .env file to the .gitignore file to prevent it from being committed to the repository.
- Use a package like dotenv to load environment variables from the .env file into the application.
- Use environment variables to store sensitive information, such as API keys or database credentials, and avoid hardcoding them in the code.

Here is an example of how to use the dotenv package to load environment variables from a .env file:

```javascript
const app = require("./app");
const dotenv = require("dotenv");

dotenv.config();

const port = process.env.PORT || 3000;

app.listen(port, () => {
  console.log(`Started express server at port ${port}`);
});
```

In the above code snippet, we are using the dotenv package to load environment variables from the .env file into the application. This allows us to store sensitive information in the .env file and avoid hardcoding it in the code.

By following these best practices, we can ensure that environment variables are not exposed unintentionally and that sensitive information is properly secured.


## Video demonstration

[Watch the video](https://drive.google.com/file/d/1e4nbAt4Nh8acoyEOQfjC6fAAl0X0kbkY/view?usp=sharing)