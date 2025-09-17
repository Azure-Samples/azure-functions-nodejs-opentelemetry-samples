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
curl http://localhost:4000/api/httpTrigger1?name=test
```

---

Changes compared to version from Rohit:

- Converted to ESM modules
- Replaced the @azure/functions with @azure/monitor-opentelemetry
  - Using the same OTEL versions, to avoid mixing OTEL 2.0 with 1.x as used in @azure/monitor-opentelemetry
  - See https://github.com/Azure/azure-sdk-for-js/blob/main/sdk/monitor/monitor-opentelemetry/package.json
- Split the application into otel setup (otel.mjs) and application setup (index.mjs)
- experimental loader added to NODE_OPTIONS and languageWorkers**node**arguments to load otel.mjs as per https://github.com/open-telemetry/opentelemetry-js/blob/main/doc/esm-support.md

Status: Failing to report remote spans in Azure Monitor

See the failing.log file for a failed startup, problematic lines are marked with -->

- index.mjs and function/\* files are loaded before the OTEL init runs
- OTEL span is undefined when invoking the function

To reproduce locally:

Create a local settings file:

```
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

Build and start:

npm i
npm start

Access the function

curl http://localhost:4000/api/httpTrigger1?name=test
