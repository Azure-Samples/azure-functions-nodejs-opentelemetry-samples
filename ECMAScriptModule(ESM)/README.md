# How to Run This Project

## Prerequisites

- [Node.js](https://nodejs.org/) (v18 or later recommended)
- [Azure Functions Core Tools](https://docs.microsoft.com/azure/azure-functions/functions-run-local) (v4)
- [pnpm](https://pnpm.io/) or [npm](https://www.npmjs.com/)

## Setup

1. **Clone this repository** (if you haven't already).
2. **Install dependencies:**
   ```sh
   npm install
   # or
   pnpm install
   ```
3. **Create a `local.settings.json` file** in the project root with the following content (replace `<REDACTED>` with your Application Insights connection string):
   ```json
   {
     "IsEncrypted": false,
     "Values": {
       "FUNCTIONS_WORKER_RUNTIME": "node",
       "APPLICATIONINSIGHTS_CONNECTION_STRING": "<REDACTED>",
       "languageWorkers__node__arguments": "--inspect=4001 --enable-source-maps --experimental-loader=@opentelemetry/instrumentation/hook.mjs --import ./src/otel.mjs",
       "NODE_OPTIONS": "--inspect=4001 --enable-source-maps --experimental-loader=@opentelemetry/instrumentation/hook.mjs --import ./src/otel.mjs"
     },
     "ConnectionStrings": {}
   }
   ```

## Run the Function App

```sh
npm start
```

## Test the Function

You can test the HTTP trigger with:

```sh
curl http://localhost:7071/api/httpTrigger1?name=test
```
