# Powertools for AWS Lambda (.NET) - Triaging Template

This repository contains a sample .NET project to deploy an AWS Lambda function using AWS SAM. The main goal of this project is to provide a testing ground for triaging issues and bug reports related to Powertools for AWS Lambda (.NET).

## Prerequisites

Before you begin, ensure you have met the following requirements:

- .NET SDK (8.0 or later)
- AWS CLI
- AWS SAM CLI
- Docker (for local testing)

# Setting up your .NET development environment

## Install dotnet SDK

To install the .NET SDK, follow the instructions for your operating system on the [.NET download page](https://dotnet.microsoft.com/download).

To develop and build your Lambda functions, you can use any of the commonly available .NET integrated development environments (IDEs), including Microsoft Visual Studio, Visual Studio Code, and JetBrains Rider. To simplify your development experience, AWS provides a set of .NET project templates, as well as the Amazon.Lambda.Tools command line interface (CLI).

Run the following .NET CLI commands to install these project templates and command line tools.

## Installing the .NET project templates
To install the project templates, run the following command:

```shell
dotnet new install Amazon.Lambda.Templates
```

## Installing and updating the CLI tools

Run the following commands to install, update, and uninstall the Amazon.Lambda.Tools CLI.

To install the command line tools:

```shell
dotnet tool install -g Amazon.Lambda.Tools
```

## Adding Powertools

The following commands will add the latest Powertools packages for AWS Lambda (.NET) to your `HelloWorld.csproj` file:

```shell
cd src/HelloWorld
dotnet add package AWS.Lambda.Powertools.Logging
dotnet add package AWS.Lambda.Powertools.Metrics
dotnet add package AWS.Lambda.Powertools.Tracing
```

## Triaging workflow

1. Fork the repository using the "Use this template" button (green, top right).
2. Clone your forked repository to your local machine.
3. Run `dotnet build` to compile the project and install dependencies.
4. Make changes to the Lambda function code in `src/HelloWorld/Function.cs`. (run `sam build` to build the changes)
5. Deploy the stack using `sam build && sam deploy --guided` (first time) or `sam deploy` (subsequent deployments).
6. Test the Lambda function by invoking it with a sample event payload from the issue report or by using the provided event in the `events/` directory.
7. Observe the logs in CloudWatch to verify the function's behavior.
8. If you are able to reproduce the issue, update this README.md file with the details of the issue, including:
- Steps to reproduce
- Expected behavior
- Actual behavior
- Any relevant logs or error messages
9. Push your changes to your forked repository and report your findings in the original issue report on the Powertools for AWS Lambda GitHub repository.

## Project Structure

This project contains source code and supporting files for a serverless application that you can deploy with the SAM CLI. It includes the following files and folders.

- src - Code for the application's Lambda function.
- events - Invocation events that you can use to invoke the function.
- test - Unit tests for the application code. 
- template.yaml - A template that defines the application's AWS resources.

The application uses several AWS resources, including Lambda functions and an API Gateway API. These resources are defined in the `template.yaml` file in this project. You can update the template to add AWS resources through the same deployment process that updates your application code.

## Deploy the sample application

The Serverless Application Model Command Line Interface (SAM CLI) is an extension of the AWS CLI that adds functionality for building and testing Lambda applications. It uses Docker to run your functions in an Amazon Linux environment that matches Lambda. It can also emulate your application's build environment and API.

To use the SAM CLI, you need the following tools.

* SAM CLI - [Install the SAM CLI](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/serverless-sam-cli-install.html)
* .NET Core - [Install .NET Core](https://www.microsoft.com/net/download)
* Docker - [Install Docker community edition](https://hub.docker.com/search/?type=edition&offering=community)

To build and deploy your application for the first time, run the following in your shell:

```bash
sam build
sam deploy --guided
```

The first command will build the source of your application. The second command will package and deploy your application to AWS, with a series of prompts:

* **Stack Name**: The name of the stack to deploy to CloudFormation. This should be unique to your account and region, and a good starting point would be something matching your project name.
* **AWS Region**: The AWS region you want to deploy your app to.
* **Confirm changes before deploy**: If set to yes, any change sets will be shown to you before execution for manual review. If set to no, the AWS SAM CLI will automatically deploy application changes.
* **Allow SAM CLI IAM role creation**: Many AWS SAM templates, including this example, create AWS IAM roles required for the AWS Lambda function(s) included to access AWS services. By default, these are scoped down to minimum required permissions. To deploy an AWS CloudFormation stack which creates or modifies IAM roles, the `CAPABILITY_IAM` value for `capabilities` must be provided. If permission isn't provided through this prompt, to deploy this example you must explicitly pass `--capabilities CAPABILITY_IAM` to the `sam deploy` command.
* **Save arguments to samconfig.toml**: If set to yes, your choices will be saved to a configuration file inside the project, so that in the future you can just re-run `sam deploy` without parameters to deploy changes to your application.

You can find your API Gateway Endpoint URL in the output values displayed after deployment.

## Use the SAM CLI to build and test locally

Build your application with the `sam build` command.

```bash
sam build
```

The SAM CLI installs dependencies defined in `src/HelloWorld/HelloWorld.csproj`, creates a deployment package, and saves it in the `.aws-sam/build` folder.

Test a single function by invoking it directly with a test event. An event is a JSON document that represents the input that the function receives from the event source. Test events are included in the `events` folder in this project.

Run functions locally and invoke them with the `sam local invoke` command.

```bash
sam local invoke HelloWorldFunction --event events/event.json
```

The SAM CLI can also emulate your application's API. Use the `sam local start-api` to run the API locally on port 3000.

```bash
sam local start-api
curl http://localhost:3000/
```

## Fetch, tail, and filter Lambda function logs

To simplify troubleshooting, SAM CLI has a command called `sam logs`. `sam logs` lets you fetch logs generated by your deployed Lambda function from the command line. In addition to printing the logs on the terminal, this command has several nifty features to help you quickly find the bug.

`NOTE`: This command works for all AWS Lambda functions; not just the ones you deploy using SAM.

```bash
sam logs -n HelloWorldFunction --stack-name pt-triage --tail
```

You can find more information and examples about filtering Lambda function logs in the [SAM CLI Documentation](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/serverless-sam-cli-logging.html).

## Unit tests

Tests are defined in the `test` folder in this project.

```bash
dotnet test test/HelloWorld.Test --filter "Category!=E2E" --verbosity normal --framework net8.0
```

## Powertools Configuration

The project is configured with the following Powertools environment variables in template.yaml:

- POWERTOOLS_LOG_LEVEL: Set to INFO
- POWERTOOLS_LOGGER_SAMPLE_RATE: Set to 0.1 (10% sampling)
- POWERTOOLS_LOGGER_LOG_EVENT: Set to true (logs incoming events)
- POWERTOOLS_METRICS_NAMESPACE: Set to TriagingTemplate
- POWERTOOLS_SERVICE_NAME: Set to triage

## Cleanup

To delete the sample application that you created, use the AWS CLI. Assuming you used your project name for the stack name, you can run the following:

```bash
sam delete --stack-name pt-triage
```
