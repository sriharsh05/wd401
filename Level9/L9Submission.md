# Error Tracking System

## Introduction

I used Sentry to integrate a robust error tracking system that captures and logs errors occurring within the React.js application. 

Sentry is a third-party tool that specializes in error tracking and provides a comprehensive set of features for monitoring and managing errors. It allows developers to collect relevant information about the errors, including stack traces, error messages, and contextual data such as user actions leading to the error.
Additionally, I configured alerts and notifications to inform developers promptly when critical errors occur, allowing for quick response and resolution.

## Integration of Sentry

- Create a Sentry Account.
- Create a new project in Sentry.
- Install the Sentry package in the React.js application using npm.
```bash
npm install --save @sentry/react
```

- Configure Sentry in the application.
```javascript

import ReactDOM from "react-dom/client";
import App from "./App.tsx";
import "./index.css";
import './i18n.ts'
import * as Sentry from "@sentry/react";


Sentry.init({
    dsn: "https://ede4dcda42c7d59248953586b5aff04e@o4505862073024512.ingest.us.sentry.io/4506920956657664",
    integrations: [
      Sentry.browserTracingIntegration(),
      Sentry.replayIntegration({
        maskAllText: false,
        blockAllMedia: false,
      }),
    ],
    tracesSampleRate: 1.0, 
    tracePropagationTargets: ["localhost", /^https:\/\/yourserver\.io\/api/],
    replaysSessionSampleRate: 0.1, 
    replaysOnErrorSampleRate: 1.0, 
  });

ReactDOM.createRoot(document.getElementById("root")!).render(<App />);
```
- Configure alerts and notifications in Sentry using gamil to inform developers promptly when critical errors occur. When an error occurs, Sentry sends an email to the developers with the error details.

# Debugger Capability

I used the browser DevTools to inspect component hierarchies, examine state and props, and debug application logic. 

## How I used a debugger to track a bug in the application

I used the browser DevTools to track a bug in the application. The bug was related to an incorrect api request that was causing the application to crash. I used the  DevTools to inspect the component and examine the components involved in the api request. 

## Root Cause Analysis (RCA) for the bug

The bug was caused by an incorrect api request that was being made by the application. The incorrect api request was causing the application to not show data. The root cause of the bug was a typo in the api endpoint url. The incorrect api endpoint url was causing the application to make an invalid api request, which resulted in the application to not function properly. The bug was fixed by correcting the api endpoint url and making the correct api request.

### How I used a devtools to track a bug in the application

- I used developer tools to inspect the api request and response to identify the issue.
- I used network tab to inspect the api request and response to identify the issue.
- I have opened the api request which is causing the issue and inspected the request and response to identify the issue.
- I have used initiator tab to identify the component which is making the api request.
- I have used console tab to inspect the error message and stack trace to identify the issue.
- I have used sources tab to debug the application logic to identify the issue.
- This way I used the debugger to track the bug in the application.

