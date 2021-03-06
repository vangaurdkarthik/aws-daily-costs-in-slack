# aws-daily-costs-in-slack

## Overview
* This project will send daily AWS cost estimates of AWS accounts in an Organization. Ideally it should be deployed as a lambda function and configured with a daily cron job. The source code is in `src/` directory.
* The lambda function needs to be deployed in the master account, with necessary IAM permissions to access cost explorer APIs.
* This project uses slack's incoming webhook URL, to send the cost info. Refer slack's [doc](https://api.slack.com/messaging/webhooks) for more info.
* Each Cost Explorer API request costs $0.01.

## Prerequisites
* Update `src/appconfig.json` with your slack channel's incoming webhook URL.
* Update `src/appconfig.json` with your list of Account IDs to fetch the daily unblended cost estimates.
* Update `deploy/bin/aws-daily-costs-in-slack.ts` with your master account ID and the region you wish to deploy.
* Ability to deploy the code as a lambda function in the master account.

## Directory Structure

```
.
├── README.md
├── deploy
│   ├── bin
│   │   └── aws-daily-costs-in-slack.ts
│   ├── cdk.json
│   ├── lib
│   │   └── aws-daily-costs-in-slack-stack.ts
│   ├── package-lock.json
│   ├── package.json
│   └── tsconfig.json
└── src
    ├── appconfig.json
    ├── lambda.js
    ├── package-lock.json
    └── package.json
```

* `src` consists of the source code of this project. 
* `deploy` consists of the deployment source code, using AWS CDK.

## Deploy
* I am using AWS CDK, to the deploy the code as a lambda function and configure it with cron settings.
* If you would like to use CDK as well, then:

```shell
git clone https://github.com/raywonkari/aws-daily-costs-in-slack.git
cd aws-daily-costs-in-slack/
# update the config. See Prerequisites section.

cd src/
npm install

cd ../deploy/
npm install
tsc
cdk deploy
```

* See `deploy/` directory for some more info on the deployment.
* Alternately, you could use any deployment method, just make sure to install dependencies in `./src/` by running `npm install` & deploy the `./src/` directory in the master account, ideally as a lambda function.

## TODO
1. Dollar is used as default currency. See `line 103` in `./src/lambda.js`. Make it dynamic, so we don't need to hard code the currency.
2. We are computing `UNBLENDED` costs, and it is hard coded. If possible make it dynamic, so we could set the cost type in the appconfig, so that the filter and further processing is done automatically.
3. Currently one list of accounts and one slack message is sent using one webhook URL. Make it more robust, so we could set different account groupings in appconfig, and different slack webhooks.

## Blog

* This project is used as a reference to my blog post here: https://raywontalks.com/aws-costs-in-slack/
